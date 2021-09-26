

#### **Docker与虚拟化**

**1、Docker与LXC（Linux Container）有何不同？**

LXC利用Linux上相关技术实现容器，Docker则在如下的几个方面进行了改进：

- 移植性：通过抽象容器配置，容器可以实现一个平台移植到另一个平台；
- 镜像系统：基于AUFS的镜像系统为容器的分发带来了很多的便利，同时共同的镜像层只需要存储一份，实现高效率的存储；
- 版本管理：类似于GIT的版本管理理念，用户可以更方面的创建、管理镜像文件；
- 仓库系统：仓库系统大大降低了镜像的分发和管理的成本；
- 周边工具：各种现有的工具（配置管理、云平台）对Docker的支持，以及基于Docker的Pass、CI等系统，让Docker的应用更加方便和多样化。

**2 、Docker与Vagrant有何不同？**

两者的定位完全不同

Vagrant类似于Boot2Docker（一款运行Docker的最小内核），是一套虚拟机的管理环境，Vagrant可以在多种系统上和虚拟机软件中运行，可以在Windows。Mac等非Linux平台上为Docker支持，自身具有较好的包装性和移植性。

原生Docker自身只能运行在Linux平台上，但启动和运行的性能都比虚拟机要快，往往更适合快速开发和部署应用的场景。

**3、开发环境中Docker与Vagrant该如何选择？**

Docker不是虚拟机，而是进程隔离，对于资源的消耗很少，单一开发环境下Vagrant是虚拟机上的封装，虚拟机本身会消耗资源。



**Other FAQ**

**1、Docker能在非Linux平台（Windows+MacOS）上运行吗？**

可以

**2 、如何将一台宿主机的docker环境迁移到另外一台宿主机？**

停止Docker服务，将整个docker存储文件复制到另外一台宿主机上，然后调整另外一台宿主机的配置即可

**3、Docker容器创建后，删除了/var/run/netns 目录下的网络名字空间文件，可以手动恢复它：**

\# 查看容器进程ID，比如1234 sudo docker inspect --format='{{. State.pid}}' $container_id 1234 # 到proc目录下，把对应的网络名字空间文件链接到/var/run/netns,然后通过正常的系统命令查看操作容器的名字空间。

------



**1、Docker 和虚拟机有啥不同？**

Docker 是轻量级的沙盒，在其中运行的只是应用，虚拟机里面还有额外的系统。

**2、Docker 安全么？**

Docker 利用了 Linux 内核中很多安全特性来保证不同容器之间的隔离，并且通过签名机制来对镜像进行验证。大量生产环境的部署证明，Docker 虽然隔离性无法与虚拟机相比，但仍然具有极高的安全性。

**3、如何清理后台停止的容器？**

可以使用 sudo docker rm $sudo( docker ps -a -q) 命令。

**4、如何查看镜像支持的环境变量？**

可以使用 docker run IMAGE env 命令。

**5、当启动容器的时候提示：exec format error？如何解决问题**

检查启动命令是否有可执行权限，进入容器手工运行脚本进行排查。

**6、本地的镜像文件都存放在哪里？**

与 Docker 相关的本地资源都存放在/var/lib/docker/目录下，其中container目录存放容器信息，graph目录存放镜像信息，aufs目录下存放具体的内容文件。

**7、如何退出一个镜像的bash，而不终止它？**

按 Ctrl-p Ctrl-q。

**8、退出容器时候自动删除?**

使用 –rm 选项，例如 sudo docker run –rm -it ubuntu

**9、怎么快速查看本地的镜像和容器？**

可以通过docker images来快速查看本地镜像；通过docker ps -a快速查看本地容器。
