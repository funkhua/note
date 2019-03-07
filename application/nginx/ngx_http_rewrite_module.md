## ngx_http_rewrite_module



```
Syntax: 	rewrite regex replacement [flag];
Default: 	—
Context: 	server, location, if

rewrite 正则规则 定向路径 重写类型;
```

如果指定的正则表达式与请求URI匹配，则URI将根据替换字符串中的指定进行更改。



重写指令按照它们在配置文件中的出现顺序依次执行。

可以使用标志终止对指令的进一步处理。

如果替换字符串(replacement)以“http：//”，“https：//”或“$ scheme”开头，则处理停止并将重定向返回给客户端。



可选的标志参数:

1. last：停止处理当前的rewrite，继续匹配，搜索与变更后的URI匹配的 location，浏览器地址栏URL地址不变;
2. break：停止处理当前的ngx_http_rewrite_module指令集，不继续匹配，浏览器地址栏URL地址不变。
3. redirect：返回302临时重定向，浏览器地址会显示跳转后的URL地址
4. permanent：返回301永久重定向，浏览器地址栏会显示跳转后的URL地址



示例：

```
server {
    ...
    rewrite ^(/download/.*)/media/(.*)\..*$ $1/mp3/$2.mp3 last;
    rewrite ^(/download/.*)/audio/(.*)\..*$ $1/mp3/$2.ra  last;
    return  403;
    ...
}
但是如果这些指令放在“/ download /”位置内，最后一个标志应该被break替换，否则nginx将进行10个循环并返回500错误：
location /download/ {
    rewrite ^(/download/.*)/media/(.*)\..*$ $1/mp3/$2.mp3 break;
    rewrite ^(/download/.*)/audio/(.*)\..*$ $1/mp3/$2.ra  break;
    return  403;
}

如果替换字符串包含新请求参数，之前的请求参数将附加在它们之后。如果这是不希望的，在替换字符串的末尾加上一个问号可以避免附加它们，例如：
rewrite ^/users/(.*)$ /show?user=$1? last;

如果正则表达式包含“}”或“;”字符，则整个表达式应包含在单引号或双引号中。
```



参考：http://nginx.org/en/docs/http/ngx_http_rewrite_module.html#rewrite

网络示例：

```
server {
    # 访问 /last.html 的时候，页面内容重写到 /index.html 中
    rewrite /last.html /index.html last;
    # 访问 /break.html 的时候，页面内容重写到 /index.html 中，并停止后续的匹配
    rewrite /break.html /index.html break;
    # 访问 /redirect.html 的时候，页面直接302定向到 /index.html中
    rewrite /redirect.html /index.html redirect;
    # 访问 /permanent.html 的时候，页面直接301定向到 /index.html中
    rewrite /permanent.html /index.html permanent;
    # 把 /html/*.html => /post/*.html ，301定向
    rewrite ^/html/(.+?).html$ /post/$1.html permanent;
    # 把 /search/key => /search.html?keyword=key
    rewrite ^/search\/([^\/]+?)(\/|$) /search.html?keyword=$1 permanent;
}

last和break的区别:
因为301和302不能简单的只返回状态码，还必须有重定向的URL，这就是return指令无法返回301,302的原因了。这里 last 和 break 区别有点难以理解：
  - last一般写在server和if中，而break一般使用在location中
  - last不终止重写后的url匹配，即新的url会再从server走一遍匹配流程，而break终止重写后的匹配
  - break和last都能组织继续执行后面的rewrite指令

在location里一旦返回break则直接生效并停止后续的匹配location

server {
    location / {
        rewrite /last/ /q.html last;
        rewrite /break/ /q.html break;
    }
    location = /q.html {
        return 400;
    }
}
访问/last/时重写到/q.html，然后使用新的uri再匹配，正好匹配到locatoin = /q.html然后返回了400
访问/break时重写到/q.html，由于返回了break，则直接停止了

参考链接：https://xuexb.com/post/nginx-url-rewrite.html
```

