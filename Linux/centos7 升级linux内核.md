centos7 升级linux内核


1. 查看当前内核版本
```
# uname -r
3.10.0-862.11.6.el7.x86_64
```

2. 安装epel仓库
```
# rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
# rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
或者 
# yum install epel-release -y

安装仓库管理工具
# yum install yum-utils -y
开启内核仓库
# yum-config-manager --enable elrepo-kernel
```
**内核版本说明：**
内核长期支持版本：kernel-lt
内核主线最新版本：kernel-ml

3. 查看内核版本安装包
```
# yum list kernel-ml --showduplicates | sort -r
Repository epel is listed more than once in the configuration
kernel-ml.x86_64               4.18.14-1.el7.elrepo                elrepo-kernel
kernel-ml.x86_64               4.18.13-1.el7.elrepo                elrepo-kernel
Available Packages

# yum list kernel-lt --showduplicates | sort -r 
Repository epel is listed more than once in the configuration
kernel-lt.x86_64               4.4.161-1.el7.elrepo                elrepo-kernel
kernel-lt.x86_64               4.4.160-1.el7.elrepo                elrepo-kernel
Available Packages
```

4. 安装内核
```
#  yum install kernel-ml
```

5. 检验安装是否成功
```
# rpm -qa | grep kernel                                    
kernel-3.10.0-862.11.6.el7.x86_64
kernel-ml-4.18.14-1.el7.elrepo.x86_64

可以看到新安装的 kernel-ml-4.18.14-1.el7.elrepo 的版本，说明安装成功
```

6. 查看内核启动顺序，grub2中的信息
```
# egrep ^menuentry /etc/grub2.cfg | cut -f 2 -d \'
CentOS Linux (4.18.14-1.el7.elrepo.x86_64) 7 (Core)
CentOS Linux (3.10.0-862.11.6.el7.x86_64) 7 (Core)
CentOS Linux (0-rescue-8bf3bed0105348d09ad5862fe0fa197f) 7 (Core)
```

7. 设置默认启动内核，以上列表自上而下0开始排序，所以设置default 0
```
# grub2-set-default 0
```
8. 重启机器
```
# reboot
```
9. 查看内核版本
```
# uname  -r
4.18.14-1.el7.elrepo.x86_64
```



**说明：** 在下列情况中可以选择性删除内核版本

1. 在安装过程中提示 /boot 分区空间不足
2. 升级完成后，不需要就的内核版本
```
# yum remove $(rpm -qa | grep kernel | grep -v $(uname -r))
```
**注意：** 在你选择 y 之前，检查列表信息。