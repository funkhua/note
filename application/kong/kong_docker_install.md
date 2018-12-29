kong 安装


```
## 创建一个独立网络空间
# docker network create kong-net

## 启动postgres数据库
# docker run -d --name kong-database --network=kong-net -p 5432:5432 -e "POSTGRES_USER=kong" -e "POSTGRES_DB=kong" postgres:9.6

## 通过迁移kong 初始化数据库
# docker run --rm --network=kong-net -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=kong-database" -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" kong:latest kong migrations up

## 启动kong
# docker run -d --name kong --network=kong-net -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=kong-database" -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" -e "KONG_PROXY_ERROR_LOG=/dev/stderr" -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" -p 8000:8000 -p 8443:8443 -p 8001:8001 -p 8444:8444 kong:latest

## 启动konga，没有使用数据库持久化存储
# docker run -p 1337:1337 --network=kong-net --name konga -e "NODE_ENV=development" -e "TOKEN_SECRET=123456" -d pantsel/konga

## 启动konga用数据库
# docker run  --name MariaDB --network=kong-net -p 3306:3306 -v /data/db/mariadb:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mariadb:latest

# mysql -h 127.0.0.1 -p
create database konga default character set utf8 collate utf8_general_ci;
grant all privileges on konga.* to 'konga'@'%' identified by '123456';

## 第一次运行要用 development 模式初始化 创建表，然后用 production 模式
docker run -p 1337:1337 --network=kong-net -e "TOKEN_SECRET=123456" -e "DB_ADAPTER=mysql" -e "DB_HOST=10.16.208.185" -e "DB_PORT=3306" -e "DB_USER=konga" -e "DB_PASSWORD=123456" -e "DB_DATABASE=konga" -e "NODE_ENV=development" --name konga -d pantsel/konga

```



测试kong1.0

```
https://hub.docker.com/_/kong/
kong:1.0rc3

# docker network create kong-net

# docker run -d --name kong-database --network=kong-net -p 5432:5432 -e "POSTGRES_USER=kong" -e "POSTGRES_DB=kong" postgres:9.6

# docker run --rm --network=kong-net -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=kong-database" -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" kong:1.0rc3 kong migrations bootstrap
# docker run --rm --network=kong-net -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=kong-database" -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" kong:1.0rc3 kong migrations up

# docker run -d --name kong --network=kong-net -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=kong-database" -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" -e "KONG_PROXY_ERROR_LOG=/dev/stderr" -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" -p 8000:8000 -p 8443:8443 -p 8001:8001 -p 8444:8444 kong:1.0rc3


```

