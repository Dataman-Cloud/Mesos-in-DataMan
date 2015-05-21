如何在ucloud上使用数人科技 mesos-based PaaS 解决方案
======================================================

Apache Mesos 作为新兴的统一资源管理与调度平台，其编译，安装，配置还比较繁琐，且鲜有经过生产环境验证的安装包。数人科技对标准版进行定制和二次开发，并部署到用户的生产环境中。你可以参考该用户手册，快速搭建一套数人科技的 **Mesos/Marathon/Bamboo/Chronos** 环境。

*Note: 更多配置信息请访问[http://get.dataman.io/](http://get.dataman.io/)*

##快速启动Mesos Master, Marathon, Chronos


##快速启动Mesos Slave

##快速启动Bamboo

###Ubuntu 14.04 64位

  1. 创建主机时选择**行业**镜像里的**Bamboo数人科技Ubuntu企业版** ， 其它按需配置
  2. 登陆主机，按如下参数配置

###CentOS 7 64位


##docker 仓库（可选）

  我们经常需要在Marathon上使用docker container，这时候使用 Docker Hub 这样的公共仓库可能不方便。数人科技提供的解决方案有如下两个。

###方案一：使用国内的 docker 镜像加速器

  目前国内很多厂商提供 docker 加速服务， 譬如可以参考[daocloud 的 docker 加速服务](http://get.daocloud.io/)，为你的**Mesos集群**配置镜像加速器。

###方案二：搭建私有仓库

  我们也可以创建一个本地仓库供集群使用，这里建议参考dockerpool的文章[搭建私有仓库](http://dockerpool.com/static/books/docker_practice/repository/local_repo.html)来搭建私有仓库。 同时，可以结合方案一的加速服务来快速拉取公有镜像到内部仓库。
  
  另外，在使用 Ucloud 云主机搭建私有仓库时，请提前做好规划，申请足够大的数据盘来为本地仓库预留充足的存储空间。

##联系我们

  [数人科技团队](http://www.dataman-inc.com/contact.html)
  ![微信](http://www.dataman-inc.com/images/code-weixin.jpg)
