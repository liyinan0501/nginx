# NGNIX

## NGNIX Basics

是一个高性能的http和反向代理服务器，特点是占的内存少，并发能力强。

### **Reverse Proxying**

1. Proxy

   在客户端浏览器配置代理服务器，通过代理服务器进行外网访问。

2. Reverse Proxying

   无须配置客户端浏览器，客户端直接访问。客户端只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，在返回给客户端。反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实服务器ip地址。

### Load balancing

单个服务器解决不了，增加服务器的数量，然后请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡。

### Separating dynamic and static

为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速度，降低原来单个服务器的压力。



```shell

tar zxvf nginx-1.22.1.tar.gz

./configure --prefix=/usr/local/nginx

# 关闭防火墙。
systemctl stop firewalld.service

# 禁止防火墙开机启动。
systemctl disable firewalld.service

# 启动。
./nginx
# 快速停止。
./nginx -s top
# 优雅关闭，在退出前完成已经接受的链接请求。
./nginx -s quit
# 重新加载配置，更改完配置文件后，立即生效，不用重启服务器。
./nginx -s reload


ps -ef | grep nginx
sudo ./nginx -s stop
配置脚本
systemctl status nginx

systemctl enable nginx.service
```

## NGNIX.conf

**worker_processes**

`worker_processes 1` 默认为1，表示开启一个业务进程

对应服务器主机物理的cpu内核数。

### events

**worker_connections**
`worker_connections 1024`; 单个业务进程可接受连接数

### https

**include mime.types;**
`include mime.types;`  引入另一个http mime类型配置文件

**default_type application/octet-stream;**
`default_type application/octet-stream;` 如果mime类型没匹配上，默认使用二进制流的方式传输。

**sendfile on;**
`sendfile on;  `使用linux的sendfile(socket, file, len) 高效网络传输，也就是数据0拷贝。
默认未开启sendfile

**keepalive_timeout 65;**
`keepalive_timeout 65;`

### Server

```
server {
    listen 80; 监听端口号
    server_name localhost; 主机名或域名，支持多个（用空格分开）及通配符和正则。
    location / { 匹配路径 uri
            root html; 文件根目录
            index index.html index.htm; 默认页名称
    }
    error_page 500 502 503 504 /50x.html; 报错编码对应页面
    location = /50x.html {
            root html;
    }
}
```

