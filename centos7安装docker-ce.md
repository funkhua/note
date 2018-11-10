#centos7安装docker-ce

商业化的docker1.13之后，分为社区版和企业版

社区版 docker-ce

Edge版本每月发布，提供一个月支持。

Stable版本每季度发布，提供4个月支持。

可以通过Docker EE订阅 延长Stable版本支持以及补丁修复。

企业版 docker-ee


旧版docker名称为 docker或者docker-engine

##环境
- OS： Centos7
- Docker：docker—ce 17

###第一种 通过yum源安装

#####1 安装新版本前请先卸载旧版本
	$ sudo yum remove docker docker-common docker-selinux docker-engine

#####2 安装docker-ce用到的工具
	yum-utils提供yum-config-manager工具，devicemapper存储驱动需要device-mapper-persistent-data 和 lvm2
	$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2

#####3 安装docker-ce的yum源
	$ sudo yum-config-manager  --add-repo https://download.docker.com/linux/centos/docker-ce.repo
注意：如果不配置yum源，使用yum install docker进行安装，默认安装的是docker 1.12版本。
#####4 默认开启的stable版本库，如果要体验持续更新的docker可以开启edge库
	#开启
	$ sudo yum-config-manager --enable docker-ce-edge
	#关闭
	$ sudo yum-config-manager --disable docker-ce-edge

#####5 如果开启了edge版本，可以通过
	$ sudo yum list docker-ce --showduplicates | sort -r
然后选择要安装的版本

	$ sudo yum install docker-ce-<Version>

#####6 启动docker
	$ sudo systemctl start docker

#####7 通过docker version查看版本信息
	$ sudo docker version
![](https://s5.51cto.com/wyfs02/M02/A6/F0/wKioL1nd5_7CRi4hAAAXwTAX1_8634.png)

#####8 运行hello-world (由于网络问题可能会报错)
	$ sudo docker run hello-world



###第二种 通过软件包安装
####docker-ce软件包: https://download.docker.com/linux/centos/7/x86_64/stable/Packages/
	$ sudo yum install /path/to/package.rpm

##卸载docker-ce 
	$ sudo yum remove docker-ce

卸载不会删除主机上的镜像、容器、数据卷或者自定义的配置文件。

要删除所有相关文件

	$ sudo rm -rf /var/lib/docker




######参考:https://docs.docker.com/engine/installation/linux/docker-ce/centos/