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
