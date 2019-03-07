nginx_note

1. nginx反向代理envoy，状态码报 426

   ```
   原因和现象：
   nginx proxy_pass 默认使用http1.0 
   如果  client ---> nginx ---> envoy  会报 status code 426
   envoy proxy 只支持http1.1 http2
   
   解决方式：
   需要配置 proxy_http_version 1.1;
   
   Syntax: proxy_http_version 1.0 | 1.1;
   Default: proxy_http_version 1.0;
   Context: http, server, location
   ```

2. 在nginx下 将一个server_name的https开启http2，为什么其他所有https的server都变成 http2

   ```
   参考stackoverflow：
   https://stackoverflow.com/questions/51164994/one-nginx-virtual-host-config-http2-why-all-virtual-hosts-change-to-http2
   
   启动时，nginx首先为侦听同一ip:port组合的每组虚拟主机创建一个单独的进程，然后将该进程的功能设置为由所述进程处理的该组中每个虚拟主机的所有功能的总和。
   在您的情况下，只有一个进程处理绑定到*：443的所有虚拟主机，因此该进程包括http2功能。
   为了实现您的目标，您需要使nginx生成一个不同的进程，该进程在单独的ip:port组合并且没有http2功能。
   ```

3. 为什么会有websoket协议

   ```
   我们已经有了 HTTP 协议，为什么还需要另一个协议？它能带来什么好处？
   答案很简单，因为 HTTP 协议有一个缺陷：通信只能由客户端发起。
   
   举例来说，我们想了解今天的天气，只能是客户端向服务器发出请求，服务器返回查询结果。HTTP 协议做不到服务器主动向客户端推送信息。这种单向请求的特点，注定了如果服务器有连续的状态变化，客户端要获知就非常麻烦。我们只能使用"轮询"：每隔一段时候，就发出一个询问，了解服务器有没有新的信息。最典型的场景就是聊天室。轮询的效率低，非常浪费资源（因为必须不停连接，或者 HTTP 连接始终打开）。因此，工程师们一直在思考，有没有更好的方法。WebSocket 就是这样发明的。
   
   websocket协议，长连接，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于服务器推送技术的一种。
   
   
   nginx proxy websocket
   // 基础配置方法
   location /chat/ {
       proxy_pass http://backend;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection "upgrade";
   }
   
   // 透传客户端发送的 header
   http {
       map $http_upgrade $connection_upgrade {
           default upgrade;
           ''      close;
       }
   
       server {
           ...
           location /chat/ {
               proxy_pass http://backend;
               proxy_http_version 1.1;
               proxy_set_header Upgrade $http_upgrade;
               proxy_set_header Connection $connection_upgrade;
           }
       }
   参考链接：http://nginx.org/en/docs/http/websocket.html
   ```

4. nginx解析缓存问题（待深入）

   ```
   // proxy_pass 域名会缓存dns，通过如下设置变量的方式消除缓存
   
   server {
   
       listen 80;
   
       server_name momo.com;
       index index.php index.htm index.html;
   
       root    /data/www/momo/;
   
   
       # 指定 resolver 配合 set 变量,消除 dns cache 带来的影响
       resolver 192.168.1.252 valid=10s;
       set $momo_bg_rest http://momo-bg-rest.momo.com;
   
       # 注意此处使用proxy使用变量后，将丢弃 /get_info 后的参数
       location /get_info {
           proxy_http_version 1.1;
           proxy_pass $momo_bg_rest/api/rate/get_info?$args;
       }
   
       location /submit {
           proxy_http_version 1.1;
           proxy_pass $momo_bg_rest/api/rate/submit;
       }
   
       access_log /data/service_logs/nginx/momo_access.log misc;
       error_log  /data/service_logs/nginx/momo_error.log;
   }
   
   ```

5. nginx http 跳 https 方式

   ```
   方式一：添加header头
   	推荐方式，浏览器缓存发起，注意全域生效，需要全域支持https
   	add_header Strict-Transport-Security "max-age=31535999";
   	
   方式二：通过if判断跳转，有性能损耗
   	nginx不推荐用if，无论是http还是https都要进行判断
   	server {
   		listen 80;
   		listen 443 ssl;
   		...
   		if ( $scheme = http ){
       		return 301 https://$server_name$request_uri;
   		}
   		...
   	}
   
   方式三： 
   	推荐方式，没有性能损耗
   	server {
           listen 80 default_server;
           listen [::]:80 default_server;
           server_name _;
           return 301 https://$host$request_uri;
   	}
   	或
   	server {
           listen 80 default_server;
           listen [::]:80 default_server;
           server_name example.com www.example.com;
           return 301 https://$server_name$request_uri;
   	}
   ```

6. nginx 状态码 400

   ```
   如果你的请求中的header都很大，那么应该使用client_header_buffer_size，这样能减少一次内存分配。
   如果你的请求中只有少量请求header很大，那么应该使用large_client_header_buffers，因为这样就仅需在处理大header时才会分配更多的空间，从而减少无谓的内存空间浪费。
   ```

7. post 请求request_time 跟 upstream_time 差距大原因

   ```
   因为nginx会把http request body 缓存住，接收完毕后才会把数据传给后端处理。
   ```

8. nginx重试的喜与忧

   ```
   nginx 重试机制相关参数
   
   proxy_send_timeout 设置往后端/上游服务器发送请求的超时时间，默认为60s，此超时时间指的是两次成功写操作间隔时间，而不是发送整个请求的超时时间，如果在此超时时间内上游服务器没有接收任何响应，则Nginx关闭此连接。
   proxy_read_timeout 设置从后端/上游服务器读取响应的超时时间，默认为60s，此超时时间指的是两次成功读操作间隔时间，而不是读取整个响应体的超时时间，如果在此超时时间内上游服务器没有发送任何响应，则Nginx关闭此连接。
   proxy_connect_timeout 与后端/上游服务器建立连接的超时时间，默认为60s，对于内网高并发服务，请根据需要调整这几个参数，比如内网服务TP999为1s，可以将连接超时设置为100~500毫秒，而读超时可以为1.5~3秒左右。
   
   重试条件：
   Syntax:	proxy_next_upstream error | timeout | invalid_header | http_500 | http_502 | http_503 | http_504 | http_403 | http_404 | http_429 | non_idempotent | off ...;
   Default: proxy_next_upstream error timeout;
   Context: http, server, location
   
   防止无限重试，重试限制
   限制请求传递到下一台服务器的时间，0表示关闭这个限制。
   Syntax: proxy_next_upstream_timeout time;
   Default: proxy_next_upstream_timeout 0;
   Context: http, server, location
   This directive appeared in version 1.7.5.
   
   限制将请求传递到下一个服务器的可能尝试次数。0表示关闭这个限制。
   Syntax:	proxy_next_upstream_tries number;
   Default: proxy_next_upstream_tries 0;
   Context: http, server, location
   This directive appeared in version 1.7.5.
   
   重试踩坑
   比如有这么一个场景：   一个用于导入数据的web页面，上传一个excel，通过读取、处理excel，向数据库中插入数据，处理时间较长（如1分钟），且为同步操作（即处理完成后才返回结果）。暂且不论这种方式的好坏，若nginx配置的响应等待时间（proxy_read_timeout）为30秒，就会触发超时重试，将请求又打到另一台。如果处理中没有考虑到重复数据的场景，就会发生数据多次重复插入！ 或者发送短信的业务功能,发送的业务时间超时，也会引起发送了多条的短信信息；
   
   #控制retry
   fail_timeout=10s;
   
   upstream backend {
       server backend1;
       server backend2;
   }
   
   server {
       server_name proxy;
       location / {
           error_page 598 = @retry;
           error_page 599 = @no_retry;
           if ($request_method = POST) {
               return 599;
           }
           return 598;
       }
   
       location @retry {
           proxy_pass http://backend;
       }
   
       location @no_retry {
           proxy_pass http://backend;
           proxy_next_upstream off;
       }
   }
   ```

9.  414 400

   ```
   Syntax: 	large_client_header_buffers number size;
   Default: 	large_client_header_buffers 4 8k;
   Context: 	http, server
   
   设置用于读取大型客户端请求标头的缓冲区的最大数量和大小。请求行不能超过一个缓冲区的大小，否则会将414（Request-URI Too Large）错误返回给客户端。请求头字段也不能超过一个缓冲区的大小，或者400（错误请求）错误返回给客户端。缓冲区仅按需分配。默认情况下，缓冲区大小等于8K字节。如果在请求处理结束后连接转换为保持活动状态，则释放这些缓冲区。
   ```

10. 请求实体大 413

    ```
    Syntax: 	client_max_body_size size;
    Default: 	client_max_body_size 1m;
    Context: 	http, server, location
    设置客户端请求正文的最大允许大小，在“Content-Length”请求标头字段中指定。如果请求中的大小超过配置的值，则会将413（请求实体太大）错误返回给客户端。请注意，浏览器无法正确显示此错误。将大小设置为0将禁用检查客户端请求正文大小。
    ```

11. Ntpd

    在开机的时候，使用ntpdate强制同步时间，在其他时候使用ntpd服务来同步时间。要注意的是，ntpd有一个自我保护设置: 如果本机与上源时间相差太大, ntpd不运行. 所以新设置的时间服务器一定要先ntpdate从上源取得时间初值, 然后启动ntpd服务。ntpd服务运行后, 先是每64秒与上源服务器同步一次, 根据每次同步时测得的误差值经复杂计算逐步调整自己的时间, 随着误差减小, 逐步增加同步的间隔. 每次跳动, 都会重复这个调整的过程.