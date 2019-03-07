Kong_note



```
# cat custome_nginx.template
# ---------------------
# custom_nginx.template
# ---------------------

worker_processes ${{NGINX_WORKER_PROCESSES}}; # can be set by kong.conf
daemon ${{NGINX_DAEMON}};                     # can be set by kong.conf

pid pids/nginx.pid;                      # this setting is 强制的
error_log logs/error.log ${{LOG_LEVEL}}; # can be set by kong.conf

events {
    use epoll;
    worker_connections 51200;
}

http {
    # include default Kong Nginx config
    include 'nginx-kong.conf';
    default_type  application/octet-stream;
    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    sendfile on;
    tcp_nopush     on;
    keepalive_timeout 60;
    tcp_nodelay on;
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;
    fastcgi_intercept_errors on;
    fastcgi_hide_header Pragma;
    proxy_send_timeout 300;
    proxy_read_timeout 300;
    proxy_buffer_size 64k;
    proxy_buffers 4 64k;
    proxy_busy_buffers_size 128k;
    proxy_temp_file_write_size 128k;
    gzip on;
    gzip_min_length  1k;
    gzip_buffers     4 16k;
    gzip_http_version 1.0;
    gzip_comp_level 2;
    gzip_types       text/plain text/xml text/css application/x-javascript application/xml application/json text/javascript application/javascript;
    gzip_vary on;
    autoindex off;
    server_tokens off;
    server_name_in_redirect off;
}
```









