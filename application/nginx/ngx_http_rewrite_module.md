







```
Syntax: 	rewrite regex replacement [flag];
Default: 	—
Context: 	server, location, if
```











如果指定的正则表达式与请求URI匹配，则URI将根据替换字符串中的指定进行更改。



重写指令按照它们在配置文件中的出现顺序依次执行。

可以使用标志终止对指令的进一步处理。

如果替换字符串(replacement)以“http：//”，“https：//”或“$ scheme”开头，则处理停止并将重定向返回给客户端。



可选的标志参数:

