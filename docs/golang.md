### 1. 同一个struct的两个实例可否用==比较？不同struct的两个实例可否用==比较？struct类型可否作为map的key？

+ 同一struct类型的两个实例，当结构体中不包含不可比较的类型（切片、map）时，可以使用==比较，否则不能使用==比较
+ 不同struct类型的两个实例不能用==比较，因为编译报错
+ 如果struct类型中只包含可比较类型则可以用作map的key，否则不行

### 2. GC（垃圾回收）及运行原理

使用算法：三色标记+混合写屏障+辅助gc

触发时机：

* 定时触发（每2分钟内未执行过gc）
* 分配内存时触发（当前内存是上一次gc执行完内存的两倍）
* 手动触发（runtime.GC()）

执行流程：

* gc初始化：收集根节点（全局对象、G Stack），开启写屏障，开启辅助GC（需要stw，但1.9版本之后已优化，不需要stw）
* 标记：初始时节点都为白色，从根节点出发，标记为灰色，然后放入灰色集合，然后从灰色集合拿出来一个将其标记为黑色，并将其引用的对象标记为灰色，并放入灰色集合，然后重复以上操作，直到灰色集合为空，此时要么是黑节点要么是白节点，白节点即为要清理的对象。（此阶段与用户代码并行）
* 标记结束：关闭写屏障和辅助GC（需要stw）
* 清理：清理白色节点（此阶段与用户代码并行）

辅助GC：当用户程序分配内存的速度比回收速度快时，辅助gc会执行stw停掉用户程序，用更多的cpu来跑垃圾回收程序。如果不这样，那么gc会一直卡在标记阶段，无法正常执行。

写屏障: 由于标记阶段,与用户代码并行,所以可能出现被引用的对象被标记为白色的问题。例如以下场景：

A首先被标记为黑色，B引用C，用户代码将B标记为黑色之前将引用给了C，这时B被标记为黑色，但是由于A已经被扫描过，所以A引用C不会再此被扫描，所以C会被标记成白色，但是应为黑色。

写屏障就是在标记阶段，与用户代码并行时，监控对象的状态，并重新标记。

流程图如下：

![gc](../images/gc.png)

参考：

https://juejin.im/post/6844903793855987719

https://www.jianshu.com/p/e20aaa039229

http://yangxikun.github.io/golang/2019/12/22/golang-gc.html

### 3. Go调度器原理

+ GPM模型  

  一个G对应一个goroutine

  一个P对应一个逻辑处理器，并维护一个G的本地运行队列，数量与GOMAXPROCS数量一致，指最大并行数

  一个M对应一个内核线程，数量不固定，由go运行时指定，默认设置为最大10000.

  gorutine 与 内核线程 N:M映射

+ 全局运行队列

  go运行时会维护一个G的全局运行队列，p会在一定条件下，来全局运行队列中拿G放到自己的本地运行队列中。全局运行队列会使用mutex来控制多个p的并发访问。

  全局运行对列使用mutex来实现多个p的并发访问，由于锁的竞争太严重，所以每个p中引入了本地运行队列，以减少锁的竞争。

+ 本地运行队列

  每一个p都会维护一个G的本地运行队列，p会不断的在本地运行队列中取出G挂载到内核线程上去运行。当通过 `go` 关键字创建一个新的 goroutine 的时候，它会优先被放入 P 的本地队列。

+ netpoll（网络轮询器）

  比如select/poll/epoll等IO多路复用，goroutine将被挂起，直到IO事件触发，这是将goroutine重新放回运行队列中。

+ 调度过程

  p首先检查**本地运行队列**，如果本地运行队列为空，首先会去检查**全局运行队列**（需要加锁），如果全局运行队列也为空，然后去检查**网络轮询器**(network poller)中是否有IO事件被触发，如果还没有，这时会进行”**窃取**“，即去其他p的本地运行队列中拿一部分G放到自己的本地运行队列中。

+ sysmon

  go程序在启动时，会启动一个sysmon（系统监视器）的m，这个m无须与p绑定即可运行，每20us~10ms启动一次,它完成的工作主要有：

  - 释放闲置超过5分钟的span物理内存；
  - 如果超过2分钟没有**垃圾回收**，强制执行；
  - 将长时间未处理的netpoll结果添加到任务队列；
  - 向长时间运行的G任务发出**抢占调度**；
  - 收回因syscall长时间阻塞的P；

+ 抢占式调度

  当某个goroutine执行超过10ms，sysmon会向其发起抢占调度请求，goroutine调度没有时间片的概念，通过设置标记来进行抢占式操作。

  基于协作的抢占式调度器 - 1.2 ~ 1.13

  基于信号的抢占式调度器 - 1.14 ~ 至今

+ channel

  试图写入或读取channel而被阻塞的gorutine会被阻塞到channel中的sendq或recvq（写/读队列中），不会放到全局运行队列，或者p的本地运行队列中

+ 总结

  goroutine的调度不需要让 CPU **在用户态和内核态之间切换**，这种实现方式相比内核级线程可以做的很轻量级，对系统资源的消耗会小很多

![](../images/goroutine.png)

参考：

https://tonybai.com/2020/03/21/illustrated-tales-of-go-runtime-scheduler/

https://tonybai.com/2017/06/23/an-intro-about-goroutine-scheduler/

https://wudaijun.com/2018/01/go-scheduler/

### 4. Slice底层结构及实现原理

```go
// runtime/slice.go
type slice struct {
    array unsafe.Pointer // 元素指针（底层数组）
    len   int // 长度 
    cap   int // 容量
}
```

+ 切片长度

  切片长度是切片引用的元素数目

+ 切片容量

  容量是底层数组的长度

+ slice作为参数传递

  当slice类型作为函数参数传递时，是以slice结构进行值拷贝进行传递（64位机器上为24个字节，32位机器上为12个字节），由于扩容时底层数组可能变化，即array指针地址会变，所以函数中如果有调用append函数对切片扩容，那么应传递切片指针作为参数。如：

  ```go
  func appendSlice(s *[]int) {
  	for i := 0; i < 1000; i ++ {
  		*s = append(*s, i)
  	}
  }
  ```

+ 扩容机制

  当切片容量小于1024时，则每次扩容2倍，当大于等于1024时，每次扩容上次的四分之一。扩容过程中底层数组可能发生变化。

+ nil 切片

  var slice1 []int   slice1 与 nil 比较结果为true，json.Marshal结果为null

+ 空切片

  slice2 := make([]int,0)   slice2 与 nil比较结果为false，json.Marshal结果为[]

+ 切片的切片操作

  切片操作并不复制切片指向的元素。它创建一个新的切片并复用原来切片的底层数组。 因此，通过一个新切片修改元素会影响到原始切片的对应元素。如下：

```go
func doAppend(a []int) {
	_ = append(a, 0)
}

func main() {
	s := []int{1,2,3,4,5,6,7,8,9}
	s2 := s[2:5]
	fmt.Println(s) // 输出 [1 2 3 4 5 6 7 8 9]
	fmt.Println(s2) // 输出 [3 4 5]
	s2[0] = 88
	s2[1] = 88
	s2[2] = 88
	fmt.Println(s) // 输出 [1 2 88 88 88 6 7 8 9]
	fmt.Println(s2) // 输出 [88 88 88]
    
	a := []int{1, 2, 3, 4, 5}
	doAppend(a[0:2]) // 未指定容量，由于切片操作复用原底层数组，所以append操作会直接修改原底层数组上的值
	fmt.Println(a) // 输出 [1 2 0 4 5]

	b := []int{1,2,3,4,5}
	doAppend(b[0:2:2]) // 指定容量，在append时，发现容量不足，则需要扩容，不会修改原底层数组上的值
	fmt.Println(b) // 输出 [1 2 3 4 5]
}
```

+ copy 函数 func copy(dst, src []Type) int

  copy函数是值拷贝，新的拷贝切片修改不会影响旧切片

  copy函数返回值是拷贝的字节数，等于min(len(src),len(dst))，如果dst拷贝前有值则被覆盖。

+ slice常用操作

  ```go
  //删除
  func remove(slice []interface{}, i int) []interface{} {
      return append(slice[:i], slice[i+1:]...)
  }
  
  //插入
  func insert(slice *[]interface{}, index int, value interface{}) {
      rear := append([]interface{}{}, (*slice)[index:]...)
      *slice = append(append((*slice)[:index], value), rear...)
  }
  
  //清空slice
  func empty(slice *[]interface{}) {
      //    *slice = nil
      *slice = append([]interface{}{})
  }
  ```

+ range

  使用range遍历切片，拿到的value时切片元素的值拷贝

+ 在循环中可使用s[0:0]复用切片，而不需要每次循环重新申请新的切片

  ```go
  func main() {
  	var s []int
  	for i := 0 ;i < 10; i++ {
  		if s != nil {
  			s = s[0:0] // 这里可以复用切片，否则就需要每一次循环都需要重新申请一个新的slice
  		}
  		for j := i; j < 10; j ++ {
  			s = append(s, j)
  		}
  		fmt.Println(s)
  	}
  }
  ```

### 5. Map 实现原理

hash函数往往存在输入范围大于输出范围的问题，所以会出现哈希冲突（哈希碰撞的问题），通常有以下解决方法

+ 哈希冲突（哈希碰撞）

  1. 开放寻址

  ![](../images/kfxz.png)

  如上图，写如key3时，当hash函数命中key1时，就要线性往后查找第一个为空的位置，并存储key3。读取keys时，hash函数命中key1，此时就需要往后线性查找key3，直到找到或遇到空。

  2. 拉链法（golang map使用拉链法解决hash冲突）

     链表的数组

  ![](../images/lalianfa.png)

  如上图，key11经过hash函数命中2的位置，然后依次**遍历此桶中的链表**，如果找到key11，则对其进行更新操作，否则将key11添加到链表尾部。

  3. 再hash法

     当发生冲突时，使用第二个、第三个、哈希函数计算地址，直到无冲突。

+ golang map数据结构

  golang map的底层实现是哈希表，并采用拉链法解决哈希冲突

  ```go
  type hmap struct {
  	count     int  // 记录当前hash表元素数量
  	flags     uint8
            B         uint8 // 记录当前hash表中buckets的数量，由于hash表每次扩容2倍，所以存储的是对数形式，2^B = len(buckets)
  	noverflow uint16
  	hash0     uint32  // 传入hash函数，hash计算时使用
  
  	buckets    unsafe.Pointer
  	oldbuckets unsafe.Pointer // 用于hash扩容时，保存之前的buckets
  	nevacuate  uintptr
  
            extra *mapextra // 保存溢出数据的桶，数量是2 ^ (B-4)
  }
  ```

  ![](../images/hmap.png)

+ hash表扩容

  当hash表中的元素越来越多时，hash冲突的概率就会越来越高，hash表中桶的链表会越来越长，导致遍历链表耗时变长，这时就需要hash表扩容，一般每次扩容两倍。

  + 渐进式rehash

    map在rehash时，和redis一样采用渐进式rehash，使用oldbuckets字段保存旧的hash表，不一次性迁移完所有的buckets，而是把key的迁移分摊到每次的插入和删除操作中，在全部迁移完成后，释放oldbuckets。

  + 读取数据

    在扩容期间会发生读oldbuckets的情况，如果oldbuckets还未迁移完成则读oldbuckets

  + 触发扩容时机

    1. 装载因子大于6.5（即每个桶平均存储6.5个key，通常每个桶最大为8）（装载因子：元素数量/桶数量）
    2. 哈希使用了太多的溢出桶（当hash冲突数超过桶最大数量时，会存储在溢出桶中，并形成一个链表）

参考：

https://juejin.im/entry/6844903793927143438

https://juejin.im/post/6844903940866179079#heading-3

https://juejin.im/post/6844904078636482574#heading-15

### 6. init 函数执行顺序

- 在同一个go文件中，可以定义多个init方法，按照在代码中编写的顺序依次执行不同的init方法
- 在同一个package中，可以多个文件中定义init方法，不同文件中的init方法的执行`按照文件名先后`执行各个文件中的init方法
- 对不同package，如果没有依赖关系，则按照main包中import的顺序执行init()，如果存在依赖关系，则最早被依赖的包最早执行init()

下图为常量、全局变量、init函数、main函数的执行顺序图，main函数最后执行：

![](../images/run_order.png)



### 7. new和make区别

+ new(T)返回T类型的指针，make(T)返回T类型
+ new只分配内存，make分配内存并初始化
+ new可用于任意类型，make仅用于slice、channel、map

### 8. nil可与哪些类型比较？

+ 引用类型：channel、slice、map、接口、函数、指针

只有引用类型才可以与nil进行比较

### 6. Sync.Map 实现原理



### 7. Chanel 底层原理



### 8. Defer 常见坑



### 9. select， *select是随机的还是顺序的？*



### 10. context



### 11. Map 如何顺序读取



### 12. Golang内存泄漏，线上如果出现如何排查解决



### 13. Slice常见坑，len，cap，扩容等



### 14. 反射



### 15. sync.Pool用过吗，为什么使用，对象池，避免频繁分配对象（GC有关），那里面的对象是固定的吗？



### 16.  goroutine泄漏有没有处理



### 17. go使用踩过什么坑



### 18. go优缺点



### 19. go的值传递和引用



### 20. go的锁如何实现，用了什么cpu指令



### 21. go的runtime如何实现



### 22. go什么情况下会发生内存泄漏？



### 23. 怎么实现协程完美退出？



### 24. 用channel实现定时器？



### 25. 怎么理解go的interface



### 26. c++ 和 go对比



### 28. go怎么从源码编译到二进制文件



### 29. go函数中，返回值未命名，发生了panic，但是在函数内recover了。函数返回什么值？



### 30. *Go语言局部变量分配在栈还是堆？*























