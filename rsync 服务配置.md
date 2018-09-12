## rsync 服务配置



#### 环境

- 系统：CentOS 7
- 服务端：192.168.1.108
- 备份端：192.168.1.109

#### 安装配置服务端 

##### 服务端：192.168.1.108

1. 安装rsync服务 
    ``` install 
    # yum install rsync -y
    ```

2. 配置服务

    ``` config
    # vim /etc/rsyncd.conf
    
    uid = nobody
    gid = nobody
    
    use chroot = yes
    read only = yes
    max connections = 4
    
    pid file = /var/run/rsyncd.pid
    
    exclude = lost+found/
    transfer logging = yes
    timeout = 900
    
    # 配置是否授权访问
    secrets file = /etc/rsyncd/rsyncd.secrets
    # 注释掉，允许了匿名访问
    #auth users = admin
    
    hosts allow = 192.168.1.109
    hosts deny = *
    
    [data]
        path = /data/web-data
        comment = "back up data"
    ```

3. 配置授权文件

    ``` secrets
    # mkdir /etc/rsyncd/
    # vim /etc/rsyncd/rsyncd.secrets
    admin:admin
    # chmod 600 /etc/rsyncd/rsyncd.secrets
    ```

4. 启动服务

    ``` start
    # systemctl start rsyncd.service && systemctl enable rsyncd.service
    ```

#### 备份端操作同步

##### 备份端

1. 查看资源

``` view list
# rsync --list-only 192.168.1.108::data
```

2. 保留一份，不保存历史备份

``` simple
# rsync -avzl --delete 192.168.1.108::data /backups/192.168.1.108/data
```

3. 完全备份，保留历史归档

``` backup
# rsync -avzl 192.168.1.108::data /backups/192.168.1.108/$(date +'%y-%m-%d')/data
```







### FQA

``` FQA
rsync error: some files/attrs were not transferred (see previous errors) (code 23) at main.c(1518)

原因是有文件没有写的权限，导致备份数据库权限不够，两种解决办法：
1、将服务端rsyncd.conf配置文件的uid和gid分别修改成root，重载下，再次执行同步，同步成功
2、将需要同步的文件夹及下属文件赋予777权限（chmod -R 777 xxx），再次执行同步，同步成功

注意：如果使用第一种办法，那么在执行完同步后，为了安全，记得将uid和gid修改回来，或修改成nobody



```

