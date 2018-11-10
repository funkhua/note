


##日志文件
- 日志：历史事件的相关记录
- 记录的内容：事件发生的事件、事件内容
- 日志级别：事件的关联程序，loglevel

##linux常见日志
- 注意：日志文件可以查看到很多系统重要的事件，包括登录者的信息，因此日志文件的权限通常设置为仅有root能够读。
- /var/log/dmesg:记录系统在开机的时候内核监测过程产生的各项信息。
- /var/log/lastlog:记录系统上面所有的账号最近一次登录系统时的相关信息。该文件文件类型为DBase 3 data file，通过lastlog命令查看。
- /var/log/messages:几乎系统发生的错误信息或者重要信息都会记录在这个文件中，如果系统发生莫名的错误时，这个文件一定要查阅的日志文件之一。
- /var/log/secure:只要涉及到需要输入账号密码的软件，当登录时不管正确或错误都会记录，login、su、sudo、ssh、telnet等等
- /var/log/wtmp: 记录正确登录系统的账户信息。通过last命令查看。
- /var/log/btmp: 记录错误登录时使用的账户信息。通过lastb命令查看。

###rsyslog是记录日志文件的服务，特性：
- 多线程工作
- 支持UDP、TCP、SSL、TLS、RELP
- 支持将日志存储到MySQL、PGSQL、Oracle等多种关系数据库中
- 强大的过滤器，可以实现过滤系统信息中的任何部分
- 自定义输出格式
- 适用于企业级别日志记录需求

###核心概念：
- facility： 设施，从功能或程序上对日志进行分类，并由专门工具负责记录相应的日志信息；
	- auth，authpriv：认证相关设施
	- cron：定时任务
	- daemon：守护进程
	- kern：内核日志设施
	- lpr：打印设施
	- mail：邮件设施
	- mark：防火墙日志设施
	- news：新闻组日志
	- security：安全日志设施
	- syslog：本身日志设施
	- user：用户的日志设施
	- uucp：unix copy相关日志设施
	- local0 - local7：用户自定义使用的日志设施
- priority： 级别
	- debug:调试级别
	- info：基本信息
	- notice：除了info的信息，还需要注意的信息
	- warn，warning：警示信息，可能有问题，基本不会造成系统运行困扰
	- err,error：重大错误信息，例如 引起服务无法启动的信息
	- crit：比err严重的信息。crit是临界点critical的缩写。
	- alert：警告信息，比crit严重
	- emerg,panic: 系统或应用程序马上就要挂啦
		- 指定级别的方式：
			-  *：所有级别
			-  none：没有级别
			-  priority：比次级别高的（包含）所有级别的日志信息都会记录
			-  =priority：仅记录指定级别
- rsyslog的配置文件：/etc/rsyslog.conf
	- RULLS规则：
		- facility（服务设施）.priority(级别)		target（位置）
	- target：
		- 文件路径：将日志记录于指定的文件中
		- 用户：将日志信息通知给文件或者用 * 代表通知给所有用户
		- 日志服务器： @SERVER  server表示日志服务器
		- 管道： | COMMAND
	- cron.*      /var/log/cron

- 日志文件的格式：
	- 事件产生的时间		主机		进程(PID):		事件
	- Apr 15 17:35:50 www sshd[20839]: Failed password for root from 223.71.85.6 port 3135 ssh2
- 模块
  - 启用本机成为日志服务器
  	- \# Provides UDP syslog reception
  	- \#$ModLoad imudp
  	- \#$UDPServerRun 514
  	- or
  	- \# Provides TCP syslog reception
  	- \#$ModLoad imtcp
  	- \#$InputTCPServerRun 514
  - 日志存储到mysql中
    - 有可用的mysql服务器
    - 安装rsyslog-mysql程序包

      - 安装包中包含创建数据库的脚本 /usr/share/doc/rsyslog-mysql-VERSION/
    - 创建rsyslog依赖的数据库
    - 配置rsyslog启用ommysql模块
    	- \#### MODULES ####
    	- $ModLoad ommysql
    - 定义某facility把日志记录到指定数据的表中
    	- \#### RULES ####
    	- facility.priority		:ommysql:SERVER_IP,DATABASE,USERNAME,PASSWORD
    - 可选：使用loganalyzer(WebGUI)来展示日志信息 php程序
    	- 依赖LAMP组件
    		- yum install httpd php php-mysql php-gd
    		- 下载loganalyzer 
