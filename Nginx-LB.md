### Nginx ..> LB

#### Install Nginx
```
dnf install nginx -y
```
#### Custom HTML file
```
cd /usr/share/nginx/html/
```
```
rm -rf *
```
```
cat >> index.html
Hi From Server-1
```
```
cat >> index.html
Hi From Server-2
```
#### Start Nginx
```
systemctl start nginx
```
#### Enable Nginx
```
systemctl enable nginx
```
#### Nginx Default Configuration
```
cat /etc/nginx/nginx.conf
```
```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
}
```
#### Add below to nginx.conf
```
http {
    upstream backend {
      server 172.31.0.141; 
      server 172.31.8.88;
    }
}
```
### Nginx will always look for *.conf
- not recommended to change nginx.conf
- Inside cat /etc/nginx/nginx.conf we have this configuration for server{...}
```
include /etc/nginx/default.d/*.conf;
```
- create file inside `/etc/nginx/default.d/` with *conf
```
vim /etc/nginx/default.d/lb.conf
```
```
proxy_http_version 1.1;

location / { proxy_pass http://backend/; }
```
#### Restart Nginx
```
systemctl restart nginx
```
### Open Browser ..> http://34.205.174.211/ ..> Start to Distribute Load to Backend
#### HTML Files in Nginx
```
/usr/share/nginx/html
```
#### Logs of Nginx
```
/var/log/nginx
```