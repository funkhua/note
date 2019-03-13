

#### 架构

Nginx--->fastcgi_pass--->fpm

#### 现象

nginx 502 bad gateway

#### 调查过程

- 系统负载状态

```
Thu Aug 23 11:53:08 CST 2018
############### mem ##################
10852 root      20   0   77020  50056   3672 S   0.0  1.3 296:51.11 /usr/share/filebeat/bin/filebeat -path.home /usr/share/filebeat -path.config /etc/filebeat -path.data /var/lib/filebeat -path.logs+
30683 root      20   0  697224  43564  32148 S   0.0  1.1  23:33.63 php-fpm: master process (/etc/php-fpm.conf)
 4277 www       20   0  788492  36872  18900 S   0.0  1.0   0:47.93 php-fpm: pool www
23962 www       20   0  787944  32964  17492 S   0.0  0.9   0:19.31 php-fpm: pool www
21530 www       20   0  786540  33644  17648 S   0.0  0.9   0:23.15 php-fpm: pool www
21527 www       20   0  788072  33488  15964 S   0.0  0.9   0:23.34 php-fpm: pool www
26231 www       20   0  786012  32156  16668 S   0.0  0.8   0:16.13 php-fpm: pool www
25256 www       20   0  786112  32316  16608 S   0.0  0.8   0:18.14 php-fpm: pool www
22495 www       20   0  786152  32144  16468 S   0.0  0.8   0:22.22 php-fpm: pool www
21694 www       20   0  786004  31520  15936 S   0.0  0.8   0:23.04 php-fpm: pool www

 ############### cpu ##################
21506 www       20   0  786020  32092  16492 S  26.7  0.8   0:23.00 php-fpm: pool www
    9 root      20   0       0      0      0 S   0.0  0.0 152:13.90 [rcu_sched]
 9929 www       20   0  112092   1696      0 S   0.0  0.0   0:00.58 /data/service/node_export/node_exporter --web.listen-address=0.0.0.0:9100 --collector.ntp --collector.systemd --log.level=info
  942 postfix   20   0   89716   1760   1708 S   0.0  0.0   0:10.89 qmgr -l -t unix -u
  940 root      20   0   89544   1016    940 S   0.0  0.0   0:46.81 /usr/libexec/postfix/master -w
    8 root      20   0       0      0      0 S   0.0  0.0   0:00.00 [rcu_bh]
  883 ntp       20   0   29892   2024   1396 S   0.0  0.1   1:18.20 /usr/sbin/ntpd -u ntp:ntp -g
```

- nginx日志

```
access.log
- - 10.100.100.100 null.null.com 10.100.100.101 - [23/Aug/2018:11:53:11 +0800] "POST /api/account/queryFlow HTTP/1.1" 31.078 "31.078" 502 170 "-" "Java/1.8.0_144" [10.100.100.102:9001] [31.078] [502]

error.log            
2018/08/23 11:53:11 [error] 31784#31784: *818647074 recv() failed (104: Connection reset by peer) while reading response header from upstream, client: 10.100.100.100, server: null.null.com, request: "POST /api/account/queryFlow HTTP/1.1", upstream: "fastcgi://10.100.100.102:9001", host: "null.null.com"
```

- Php-fpm配置

```
cat /etc/php-fpm.d/www.conf 
[www]
user = www 
group = www 
listen.owner = www 
listen.group = www 
listen = 0.0.0.0:9001
pm = dynamic 
pm.max_children = 200
pm.start_servers = 10
pm.min_spare_servers = 10 
pm.max_spare_servers = 100 
pm.max_requests = 2048
pm.status_path = /php-status
slowlog = /data/service_logs/php71/slow.log 
request_slowlog_timeout = 10 
request_terminate_timeout = 30
rlimit_files = 65535
security.limit_extensions = .php .html
```

- 对应时间点php日志

```
[23-Aug-2018 11:53:11] WARNING: [pool www] child 21508, script '/data/www/bs_null/public/index.php' (request: "POST ") execution timed out (31.074165 sec), terminating
[23-Aug-2018 11:53:11] WARNING: [pool www] child 21508 exited on signal 15 (SIGTERM) after 3345.068954 seconds from start
[23-Aug-2018 11:53:11] NOTICE: [pool www] child 2368 started
```

基于以上配置和现象得出结论：

- 负载

```
cpu 内存 都没有问题
```

- 配置：

```
request_terminate_timeout = 30
```

- 执行超时：执行时间 31.074165 s > 30 s

```
[23-Aug-2018 11:53:11] WARNING: [pool www] child 21508, script '/data/www/bs_null/public/index.php' (request: "POST ") execution timed out (31.074165 sec), terminating
```

- 子进程收到SIGTERM信号退出

```
[23-Aug-2018 11:53:11] WARNING: [pool www] child 21508 exited on signal 15 (SIGTERM) after 3345.068954 seconds from start
```

- 由于子进程退出--->nginx报错

```
2018/08/23 11:53:11 [error] 31784#31784: *818647074 recv() failed (104: Connection reset by peer) while reading response header from upstream, client: 10.100.100.100, server: null.gg.com, request: "POST /api/account/queryFlow HTTP/1.1", upstream: "fastcgi://10.100.100.102:9001", host: "null.gg-corp.com"

```

- 结果 nginx返回502

```
- - 10.100.100.100 null.gg-corp.com 10.100.100.101 - [23/Aug/2018:11:53:11 +0800] "POST /api/account/queryFlow HTTP/1.1" 31.078 "31.078" 502 170 "-" "Java/1.8.0_144" [10.100.100.102:9001] [31.078] [502]
```

#### 结论

因为php子进程处理请求的时间大于request_terminate_timeout设定的时间，子进程收到SIGTERM信号退出，因为php子进程退出，所以nginx报502。

具体为什么超时，请调查具体的业务处理逻辑。




