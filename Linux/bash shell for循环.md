#bash shell for循环

##方法一：
	一般格式：
	for((变量初始化；布尔表达式；变量更新))
	do
		命令1
		命令2
		命令N
	done
###示例：
	sum=0
	for ((i=0;i<=100;i++))
	do
    	sum=$[sum+i]
	done
	echo $sum

##方法二：
	一般格式为：
	for 变量 in 常量列表
	do
    	命令1
    	命令2
    	...
    	命令N
	done
###示例：
	for file in $(ls /root/);
	do
	    echo $file;
    done;