#iptables详解

##### iptables 是一个配置 Linux 内核 防火墙 的命令行工具。

###防火请
- 防火墙 firewall：工作于主机或网络的边缘，对于进出的报文根据事先定义的规则做检查，将那些能够被规则所匹配到的报文做出相应处理组件。
	- 种类：
		- 主机防火墙
		- 网络防火墙
#
- netfilter：网络过滤器，在内核中工作在TCP/IP网络协议栈上的框架。
	- netfilter框架：
		- 5个 hooks function:
			- prerouting: 进入本机后路由功能之前
			- input：到达本机应用内部
			- output：由本机发出
			- forword：由本机转发
			- postrouting：路由功能发生之后，即将离开本机之前
#
- 路由发生的时刻：
	- 报文进入本机后：
		- 判断目标地址
	- 报文离开本机之前：
		- 判断经由哪个接口发出
#
- 报文流向经过的位置：
	- 到本机内部： prerouting，input
	- 由本机发出： output，postrouting
	- 由本机转发： prerouting，forward，postrouting
###iptables介绍
- iptables功能：可以检测、修改、转发、重定向和丢弃 IPv4 数据包。过滤 IPv4 数据包的代码已经内置于内核中，并且按照不同的目的被组织成 **表** 的集合。**表** 由一组预先定义的 **链** 组成，**链** 包含遍历顺序规则。每一条规则包含一个谓词的潜在匹配和相应的动作（称为 目标），如果谓词为真，该动作会被执行。也就是说条件匹配。iptables 是用户工具，允许用户使用 链 和 规则。
- 参考图： 
	- 图一：
		- ![](https://i.imgur.com/xEKEhQc.jpg)
	- 图二：
		- ![](https://i.imgur.com/8aoK17M.png)
#
- 添加到规则可以实现的功能：
	- filter（过滤）：包过滤，用于防火墙规则。
	- nat（地址转换）：NAT(Network Address Translation) Server，用于网关路由器。
	- mangle(破坏者): 数据包修改（QOS），用于实现服务质量。
	- raw： 关闭nat表上启用的连接追踪功能，高级功能，如：网址过滤。
#
- **注意报文的流向，同一个链可能被包含在多个功能中，报文将按功能优先级流经所有链。参考上图**
	- filter：input，forward，output
	- nat：prerouting, ouput, postrouting
	- mangle: prerouting, input, forward, output, postrouting
	- raw: prerouting, output
#
- 链： 链上的规则次序即为检查次序，因此有一定的法则：
	1. 同类规则，匹配范围小的放上面；
	2. 不同规则，匹配报文几率较大的放上面；
	3. 应该设置默认规则；
		1. 白名单(推荐) Drop全部，放行白名单
		2. 黑名单 Accept全部，拒绝黑名单


----------


- iptables：
	- 添加规则时要考虑：
		- 要实现的功能，判断添加在哪个表上；
		- 报文流向及经由路径，判断添加在哪个链上；
	- 功能优先级：
		- 由高到低：
			- raw--> mangel --> nat --> filter
	- 规则组成部分：
		- 报文的匹配条件，匹配之后如何处理
			- 匹配条件： 基本匹配条件、扩展匹配条件
			- 如何处理： 内建处理机制、自定义处理机制(自定义链)
			- **注意:** 报文不可能经由自定义链，只有在被内置链上的引用才能生效(即作为自定义目标) 
	- iptables：规则管理工具
		- 自动实现规则管理工具
	- 规则和链都有计数器：
		- pkts： 由规则或者链匹配到的报文的个数；
		- bytes： 由规则或链匹配到的所有报文大小之和


----------

###iptables命令
- iptables命令生成规则，送往netfilter；规则通过内核接口直接送至内核，因此，会立即生效。当不会永久有效；如果要永久有效，需要保存至配置文件中，文件在开机时加载或者用户手动加载；
#
- 用法：

		iptables [-t TABLE] SUBCOMMAND CHAIN CRETERIA -j TARGET
		iptables -t 表名 <-A/I/D/R> 规则链名 [规则号] <-i/o 网卡名> -p 协议名 <-s 源IP/源子网> --sport 源端口 <-d 目标IP/目标子网> --dport 目标端口 -j 动作
		
		参数说明：
			-t TABLE：
				默认为filter, nat, mangle, raw 四表
			SUBCOMMAND：
				链:
					INPUT链：处理输入数据包。
					OUTPUT链：处理输出数据包。
					FORWARD链：处理转发数据包。
					PREROUTING链：用于目标地址转换（DNAT）。
					POSTOUTING链：用于源地址转换（SNAT）。
				-F: flush，清空指定表的指定链上所有规则，省略链名时，清空表中的所有链；
				-N: new，新建一个用户自定义的链；自定义链只能作为默认链上的跳转对象，即在默认链通过引用来生效自定义链；
				-X: drop,删除用户自定义的空链；非空自定义链和内置链无法删除；

		
		动作包括：
		ACCEPT：接收数据包。
		DROP：丢弃数据包。
		REDIRECT：重定向、映射、透明代理。
		SNAT：源地址转换。
		DNAT：目标地址转换。
		MASQUERADE：IP伪装（NAT），用于ADSL。
		LOG：日志记录。
		


24  2   27700



