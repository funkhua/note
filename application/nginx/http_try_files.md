#### try_files 用法

**nginx version: 1.11.2.5**

```
Syntax: try_files file ... uri;
	    try_files file ... =code;
Default: —
Context: server, location
```

- 检查指定顺序的文件是否存在，并使用第一个找到的文件进行请求处理
- 处理在当前上下文中执行。根据root和alias指令从文件参数构造文件的路径
- 可以通过在名称的末尾指定斜杠来检查目录的存在，例如，“$ URI /”
- 如果未找到任何文件，则会进行 **内部重定向** 到最后一个参数中指定的uri
- 最后一个参数可以是 uri、@name location、=状态码

**注意**：

1. 最后一个参数是回退URI且必须存在，否则会出现内部500错误。

2. 命名location(location @name {...})也可以使用在最后一个参数中。

3. 与rewrite指令不同，如果回退URI不是命名location那么$args不会自动保留，如果你想保留$args，则必须明确声明。

   ```
   例子：
   	try_files $uri $uri/ /index.php?q=$uri&$args;
   ```

#### 举例
1. 最后一个参数是 uri
```
location /images/ {
    try_files $uri /images/default.gif;
}
location = /images/default.gif {
    expires 30s;
}
```
2. 最后一个参数是 @name, 应用于代理
```
location / {
    try_files /system/maintenance.html
              $uri $uri/index.html $uri.html
              @mongrel;
}
location @mongrel {
    proxy_pass http://mongrel;
}
```
3. 最后一个参数是 @name, 应用于fastcgi
  ```
location / {
    try_files $uri $uri/ @drupal;
}

location ~ \.php$ {
    try_files $uri @drupal;

    fastcgi_pass ...;

    fastcgi_param SCRIPT_FILENAME /path/to$fastcgi_script_name;
    fastcgi_param SCRIPT_NAME     $fastcgi_script_name;
    fastcgi_param QUERY_STRING    $args;

    ... other fastcgi_param's
}

location @drupal {
    fastcgi_pass ...;

    fastcgi_param SCRIPT_FILENAME /path/to/index.php;
    fastcgi_param SCRIPT_NAME     /index.php;
    fastcgi_param QUERY_STRING    q=$uri&$args;

    ... other fastcgi_param's
}

等价于：
location / {
    try_files $uri $uri/ /index.php?q=$uri&$args;
}

location ~ \.php$ {
    try_files $uri @drupal;

    fastcgi_pass ...;

    fastcgi_param SCRIPT_FILENAME /path/to$fastcgi_script_name;
    fastcgi_param SCRIPT_NAME     $fastcgi_script_name;
    fastcgi_param QUERY_STRING    $args;

    ... other fastcgi_param's
}


  ```

4. 最后一个参数 =状态码

```
location / {
    try_files $uri $uri/index.html $uri.html =404;
}
```
4. 说明： 下面的两种配置等价
```
location / {
	try_files $uri $uri/ @drupal;
}

location / {
	error_page 404 = @drupal;
	log_not_found off;
}
```



参考链接 [http://nginx.org/en/docs/http/ngx_http_core_module.html#try_files]

