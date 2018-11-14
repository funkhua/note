```
Syntax: try_files file ... uri;
		try_files file ... =code;
Default: —
Context: server, location
```

- 检查指定顺序的文件是否存在，并使用第一个找到的文件进行请求处理
- 处理在当前上下文中执行。根据root和alias指令从文件参数构造文件的路径
- 可以通过在名称的末尾指定斜杠来检查目录的存在，例如，“$ URI /”
- 如果未找到任何文件，则会进行内部重定向到最后一个参数中指定的uri