
## 关于Systemd
- Systemd用于提供用户空间的第一个进程，即PID为1的进程，它负责以预定义好的方式完成系统初始化。 Systemd由内核直接启动，并且对SIGKILL信号免疫，因此，其将无法由kill命令终止。而其他的程序均直接或者间接地由此进程启动。CentOS从第7版开始使用Systemd取代之前使用的upstart，而OpenSUSE从12版开始使用systemd取代之前的SysV init程序。其它转向使用Systemd系统包括Debian、Mageia、Gentoo等，不过systemd与SysV init完全兼容。
- Systemd在完成系统引导时以并行启动模式启动相关的服务或进程，因此，系统启动速度大大提升。进一步的说，systemd是按需启动服务，这意味着服务只有在首次被用到时才会真正被启动，而非在系统引导期间启动。此外，systemd还支持内核控制组(cgroups)、快照(snapshotting)以及系统状态恢复(restoring system state)等特性。
- Systemd架构图
- ![](http://www.ruanyifeng.com/blogimg/asset/2016/bg2016030703.png)

## Systemd用法
- SysV init系统通过几种不同的命令来处理"服务"，如 init scripts、 insserv、 telinit等等。 而systemd则将它们统一为一种管控机制。
- 基本用法如下：
	- `systemctl [general OPTIONS] subcommand [subcommand OPTIONs]`
	- subcommand：
		- `systemctl reload | restart | start | status | stop | ... <my_service>.service`
		- systemctl 可以一次操作多个服务，只需要将他们在命令行列出来即可。
			- `systemctl start service1.service service2.service`



/usr/lib/systemd/system/



- Centos 5，6，7使用的init软件
	- 5: SysV init
		- /etc/rc.d/rc.sysinit
		- /etc/init.d/*
		- 串行启动
	- 6: upstart, d-bus
		- /etc/init/*
		- /etc/init.d/*
		- 增强服务启动的并行性，当A服务启动时，启动B服务，不用等A启动完成，再启动B
	- 7: systemd, 
		- 使用grub2 