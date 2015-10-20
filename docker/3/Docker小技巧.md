# docker使用小技巧

## 1 安装注意
- 不要直接用apt-get安装docker， apt-get安装的版本有些低。 安装用curl -sSL https://get.docker.com/ubuntu/ | sudo sh 可以安装最新版

- docker对linux内核版本有要求，内核版本不能太低， 如果太低会导致docker的一些功能不能使用， 比如docker exec 命令在低版本的linux内核下不能用， 运行linux命令uname -r 可以查看linux内核版本， docker官方文档说linux内核版本不能低于3.13 ， 升级linux内核 ： sudo apt-get install linux-image-generic-lts-trusty 手动升级方法： https://www.linux.com/community/blogs/133-general-linux/803937-installupgrade-linux-kernel-to-3183-stable-in-ubuntulinux-mintpeppermint1

- 高版本的linux内核不支持aufs的存储类型，建议用devicemapper存储类型。
- docker 1.7.1有严重bug，请勿使用。

- CentOS系列提醒

CentOS/Redhat/Fedora/AMI系统，使用devicemapper作为存储后端，但注意使用devicemapper必须分配两个独立的磁盘分区给docker使用，loopback模式100%会出现故障，请避免使用！

编辑 /etc/sysconfig/docker配置文件，注意替换里面的basesize,datadev和metadatadev

    DOCKER_OPTS="--storage-driver=devicemapper --storage-opt dm.basesize=50G --storage-opt dm.datadev=/dev/sde1 --storage-opt dm.metadev=/dev/sdf1"
注解：

    basesize 表示镜像和容器的空间大小，不同容器如果使用相同镜像，不会增加大小。容器内的修改应用COW技术，修改后的内容不计算此空间中，默认10G，建议basesize设为datadev设备分区大小的1/3
    datadev 存放镜像和容器，要求必须是ext4或xfs文件系统，默认ext4，如果设为xfs，使用dm.fs指定
    metadatadev 用于存放元数据，对空间要求不大，可建立一个10G的分区。建议和datadev使用不同设备，如果条件许可，使用SSD，使用时需要将分区前4k清0。 dd if=/dev/zero of=$metadata_dev bs=4096 count=1

## 2 封装原则
- 容器要有专职

    尽量一个容器只有一个服务，原则上说一个进程就一个容器，不要让一个容器里面有多个进程，进程越多，耦合性越大。

- 容器内可以用supervision管理进程，防止进程异常退出。

- 环境变量作为容器配置项
    
    Dockerfile中可以用 ENV设置环境变量 ， docker run 命令可以用 -e 设置环境变量。 注意这里设置的环境命令都是root用户的。 如果想让Apache 用户也使用这些环境变量， 执行下面shell命令 env | grep JD_ | sed "s/^/export /g" >> /etc/apache2/envvars 表示以JD_ 开头的环境变量都设置为Apache的环境变量。 
如果是一个纯前端的网站项目，要获得系统环境变量， 可以在index.html 中 加载一个php文件： 在json.php 中获得环境变量输出。

## 3 docker的坑
- --link 链接容器

    用docker自带的--link把多个容器链接在一起， 有重启或升级的问题， 比如很多容器都依赖于 db 这个容器， 然后db容器重启了， 重启时docker分配的ip会变， 导致其他依赖于db的容器都要重启。--link 链接的容器还有启动顺序的问题， 需要先启动db容器再启动其他依赖于db的容器， 这样导致 --link和--restart=always 不能一起用， 如果一起用会发现宿主机重启了， docker容器并没有全部重启，因为这时候docker容器是同时被启动的，并不知道启动顺序。
最后决定不用 --link 链接容器了。 另外有两种链接容器的方法，一种是给容器设置固定ip ， 这个方法比较复杂： http://beijing0414.blog.51cto.com/8612563/1614660。4 另外还有一种简单的链接方式， 可以用宿主机的端口链接， 比如一个mysql容器，先设置宿主机的3306端口映射到mysql容器中。 然后查宿主机的内网ip ， 用ifconfig 查，eth0的网卡可以看见内网ip， 假设内网ip为10.128.130.175 ， docker容器是都可以访问这个宿主机内网ip的， 这样其他容器要链接mysql容器，链接数据库时 链接 10.128.130.175:3306 即可。 我们可以在docker run启动容器时用 --add-host 参数为容器设置一个hosts 。这样容器内代码可以用指定的域名去访问数据库， 不用关心内网ip的变化。

- pid
    
    docker容器中的进程有时会生成pid文件， 比如Apache进程会生成的pid文件为 /var/run/apache2/apache2.pid ， 当进程启动时，这个pid文件就存在，当进程退出时，这个pid文件也会被删除， 我们把正在运行的容器用docker commit 提交为镜像时会把pid文件也提交到镜像中，这样从新的镜像运行容器时，容器可能因为已经存在pid文件而无法启动。 以Apache为例，可能就会报 “httpd(pid 1) ready runing” 之类的错误 ， 报错告诉你httpd正在运行，但其实并没有运行只是存在pid文件而已。 所以最好是用docker stop 把容器停掉， 再用docker commit 创建镜像。
pid的问题还可能会出现在docker run时设置--restart=always 参数的时候， 设置了此参数容器退出了会自动重启， 宿主机重启了容器也能自动重启。 但是容器在重启的时候很容易pid文件存在。pid文件存在时进程会自动退出， 但又因为设置了--restart=always 进程退出的那一瞬间 容器又自动重启， 所以容器就在那里不断的退出再自动重启退出再自动重启。 此时 运行命令 docker ps 是能看见容器是运行中的状态。 但是用 docker exec -it container_name bash 始终进不了容器， 会报如下错误：
Cannot run exec command 0eb8e17609dd78c9137c62d94cfaa62795de161d643fc3cb00387b60f11090be in container 8837b983fe2f08f5f3b9999259d5f255a83774b19282b6f9c21a9d688f7f7f2a: No active container exists with ID 8837b983fe2f08f5f3b9999259d5f255a83774b19282b6f9c21a9d688f7f7f2a
No active container exists with ID 这句的意思是说 找不到有效的容器。 但是docker ps又看见容器是在运行中， 为什么会找不到有效的容器？ 这是因为这个容器现在其实一直在不断重启中， 所以进不去。
解决方法， 可以在容器启动命令脚本（如 /run.sh） 中 加上一句删除pid文件的代码， 如 rm /var/run/apache2/apache2.pid 这样在启动进程之前强制删除了pid文件，就不会重复重启了。

- docker容器的hosts文件

    在正在运行的容器 用docker exec 进入修改 /etc/hosts 文件 ，这个容器被重启后会发现 hosts文件会被还原。 所以不要直接修改hosts文件， 需要增加hosts ，在docker run时 用 --add-host 参数。

- 虚拟目录不会提交到镜像

    Dockerfile 中 VOLUME 指定的目录 或 docker run 时 -v 参数指定的目录， 在docker commit 时不会提交到镜像中。 如果-v 参数指定的容器内的目录原本有文件， 原本的文件都会被删除， 只存在宿主机目录的文件。

- docker容器的重启

    一个apache的容器， 我们要重启Apache ， 应该怎么重启？ 可能新手会 docker exec -it container_name bash 进入容器， 然后运行 service apache2 restart 启动Apache ， 这样是不能启动apache的， 只会把容器停止掉。 因为容器的主进程就是Apache ， 主进程退出时会退出容器， 在重启apache的时候 主进程先退出了， 这时候docker容器也跟着退出了，所以Apache不会重启。 要重启Apache 用docker命令： docker restart container_name

- 退出容器的方法
    如果是docker run 运行一个容器， 没有加 -d 参数让它后台运行， 这时候 ctrl+c 退出进程也会让容器停止， 如果想退出但不停止容器可以ctrl+p 然后 ctrl+q

## 4 Dockerfile编写技巧
- 将Ubuntu的源设置为国内的源，这样安装软件会快很多

        RUN sed -i "s/archive\.ubuntu\.com/mirrors\.163\.com/g" /etc/apt/sources.list

- RUN
    
    Dockerfile 中可以RUN执行系统命令创建镜像。不能用RUN命令来常驻进程。 比如不能运行 RUN gearmand -d 。

- ONBUILD
    
    ONBUILD修饰的命令在子Dockerfile文件中也会被执行，举例说明：
一个Dockerfile中有ONBUILD命令， 如 ONBUILD ADD . /app/src ， 运行 docker build -t Image_name . 创建一个名为Image_name的镜像。 另外在创建一个Dockerfile ， 第一个行是 FROM Image_name 现在这个Dockerfile是继承于前一个Dockerfile的，现在这个Dockerfile在build时会执行他父Dockerfile的ONBUILD命令， 所以现在这个Dockerfile在build时也会执行 ADD . /app/src 这个命令。

- ENTRYPOINT
    感觉 ENTRYPOINT 会比 CMD 更省资源， ENTRYPOINT 使用时用数组形式。如：
entrypoint ["/init.sh", "/usr/bin/supervisord", "-n", "-c", "/etc/supervisord.conf"]
/init.sh 是一个初始化脚本，初始化脚本内容大概为：

        #!/bin/bash
        set -e
        #执行一些初始化代码
        exec "$@"
        #最后要跟上exec "$@" ，这样才能让init.sh 脚本后的 supervisord被执行。

- .dockerignore
    .dockerignore文件中列的文件不会被 ADD或COPY 指令添加到容器中。

- 清理docker
      
          docker rm `docker ps -a | grep Exited | awk '{print $1 }’
删除没有用的镜像。 （有容器运行的镜像不会被删除）：

        docker rmi `docker images -aq`