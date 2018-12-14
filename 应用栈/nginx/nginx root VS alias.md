### root VS alias

#### root 

```
Syntax: 	root path;
Default: 	root html;
Context: 	http, server, location, if in location
```

- 请求的 根 目录，通过向 root 值添加URI来构造文件的路径。

  ```
  location /i/ {
      root /data/w3;
  }
  
  /data/w3/i/pic.jpg 文件响应 /i/pic.jpg 的请求
  ```

- 如果 URI 被修改过，就要使用 alias 别名来完成

- root 路径中可以包含变量，`$document_root` 和 `$realpath_root` 除外。

#### alias

```
Syntax: 	alias path;
Default: 	—
Context: 	location
```

- alias指定的路径是location的别名，不管location的值怎么写，资源的**真实路径都是 alias 指定的路径**

  ```
  location /i/ {
      alias /data/w3/images/;
  }
  
  当请求是 http://test.com/i/pic.jpg 时，服务器响应的是 /data/w3/images/pic.jpg 这个文件
  ```

- alias 路径中可以包含变量，`$document_root` 和 `$realpath_root` 除外。

- 如果在使用正则表达式定义的位置内使用别名，则此类正则表达式应包含捕获，而别名应引用这些捕获。

  ```
  location ~ ^/users/(.+\.(?:gif|jpe?g|png))$ {
      alias /data/w3/images/$1;
  }
  ```

- 当location匹配 alias 值的最后一部分时，最好用 root 代替

  ```
  location /images/ {
      alias /data/w3/images/;
  }
  
  location /images/ {
      root /data/w3;
  }
  ```


**参考链接:**

[nginx_doc_root](http://nginx.org/en/docs/http/ngx_http_core_module.html#root) 

[nginx_doc_alias](http://nginx.org/en/docs/http/ngx_http_core_module.html#alias)

