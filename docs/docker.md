### 1. 什么是docker

docker是一个容器化平台，docker以容器的形式将应用程序以及所有的依赖项打包到一起，以确保应用程序可以在任意环境下运行。

### 2. docker 镜像

docker镜像是docker容器的静态模板，用于创建容器。

### 3. docker 容器

docker 容器包含应用程序及其所有的依赖项，与其他容器共享宿主机内核，在用户空间容器以独立的进程运行。docker容器不依赖任何基础架构，可在任何操作系统、任何云平台运行。

### 4. docker 仓库

docker 仓库就是存放docker镜像文件的场所。

### 5. docker 与虚拟机的区别

（1）docker容器间共享宿主机内核、硬件、操作系统等资源，各容器在用户空间是以分离的进程进行运行； 虚拟机需要模拟一个完整的用户操作系统，包含应用、系统库、依赖项、硬件驱动等。

（2）虚拟机有hypervisor层和guestOS层，而容器没有，这样容器就避免了hypervisor和guestOS带来的性能损耗。

（3）虚拟机所采用的传统虚拟化技术是对硬件和操作系统的虚拟，而容器化技术是对进程的虚拟。

（4）docker隔离性更弱，docker是进程之间的隔离，而虚拟机是系统级别的隔离。

（5）docker启动秒级，虚拟机启动分钟级。

### 6. docker容器有几种状态

运行、已停止、重新启动、停止

### 7. dockerfile 常见的指令

**FROM**：指定基础镜像

**LABEL**：为镜像指定标签

**RUN**：运行指定的指令（编译镜像时）

**CMD**：容器启动要执行的命令（容器启动时）

### 8. COPY与ADD的区别

COPY和ADD的唯一区别是ADD支持从远程URL获取资源，COPY只能从docker build所在的上下文目录中读取资源到镜像中，COPY是ADD的子集。

### 9. docker常用命令

**docker pull** 从docker仓库拉取镜像

**docker push** 推送本地镜像到docker仓库

**docker rm** 删除容器

**docker rmi** 删除本地镜像

**docker images** 列出本地所有镜像

**docker ps** 列出所有容器

**docker cp** 从容器拷贝资源到宿主机或从宿主机拷贝资源到容器







