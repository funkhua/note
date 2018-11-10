## uniq命令功能说明是 检测或删除文本中重复的行。

注意： 这里去重复，是指对于**连续重复**出现的行只显示一次！！

示例：

	[root@test ~]# cat uniq.pr 
	1
	2
	2
	1
	2
	3
	1
	注意：此处只是去除了连续重复的行
	[root@test ~]# uniq uniq.pr 
	1
	2
	1
	2
	3
	1
	所以如果要去除重复行，应该先排序，然后去重
	[root@test ~]# sort uniq.pr | uniq
	1
	2
	3
	要统计重复出现的次数的话，也应该先排序，再统计
	[root@test ~]# sort uniq.pr | uniq -c
	  3 1
	  3 2
	  1 3
	
	统计ip访问次数
	zcat access.log-20180327.gz | awk '{print $1}' | sort | uniq -c | sort -rn



​	
