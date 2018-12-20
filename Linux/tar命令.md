tar命令

```
## /backups/current/driver_data 递归压缩目录下的文件
tar -czf /backups/archive/driver_data_$(date +'%y-%m-%d').tar.gz -C /backups/current/driver_data .

## /backups/current driver_data 压缩/backups/current目录下 driver_data 目录
tar -czf /backups/archive/driver_data_$(date +'%y-%m-%d').tar.gz -C /backups/current driver_data 

## 压缩两个日志文件到 /tmp 目录下
tar -czf /tmp/log.tar.gz -C /var/log messages -C /data/service_logs/nginx tomcat_access.log
```



```
-C 在命令行中间更改工作目录
```



参考链接：http://www.delorie.com/gnu/docs/tar/tar_98.html