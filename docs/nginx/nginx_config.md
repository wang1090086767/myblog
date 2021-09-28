
# Nginx 配置记录

配置通用 proxy.conf
~~~
        proxy_http_version 1.1;
        proxy_read_timeout 3000;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Real-PORT $remote_port;
        proxy_cache_methods POST;
~~~
~~~
worker_processes  1;
events {
        worker_connections 1024;
}
http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}


server {
    listen 80;
    server_name localhost;

    location / {

        root /home/pvc/projects/pvc-ui;
            #error_page 405 =200 /index.html;
            try_files $uri $uri/ /index.html;
        index  index.html index.htm;
    }
    # noVNC的配置
    location /novnc/ {
        proxy_pass http://novnc.bplatform.demo:6080/;
        # handle ws request
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        #sub_filter '/favicon.ico' '/novnc/favicon.ico';
        #sub_filter_types *;
        #sub_filter_once off;

    }
    # webSSH配置
    location /webssh/ {

        add_header 'Access-Control-Allow-Origin' '*';

        proxy_pass http://webssh.bplatform.demo:8888/;
        # handle ws request
        include proxy.conf; #引用 proxy.conf
    }


    location /minio {
        proxy_pass http://minio32.bplatform.demo;
    }

}


server {
        listen 80;
        server_name gitea32.bplatform.demo;

        location / {
                proxy_pass http://gitea32.bplatform.demo:3000;
        proxy_hide_header X-Frame-Options;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;

        }
}
# ftp 配置
server {
        listen 80;
        server_name ftp.bplatform.demo;
        location / {
                root /home/ftpusers/ftpu;
                autoindex on; # 打开文件目录列表
                autoindex_exact_size on; # 显示文件大小 单位字节
                autoindex_localtime on; # 显示时间
                charset utf-8,gbk; # 设置编码防止中午乱码
        }

}
#nexus 的 docker 镜像仓库配置
server {
        listen 80;
        server_name docker.bplatform.demo;

        location / {
                proxy_pass http://docker.bplatform.demo:5000;
        proxy_hide_header X-Frame-Options;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;
        chunked_transfer_encoding off;
        client_max_body_size 1000m;
        }
}
# nexus 配置
server {
        listen 80;
        server_name nexus.bplatform.demo;

        location / {
                proxy_pass http://nexus.bplatform.demo:8081;
        proxy_hide_header X-Frame-Options;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;

        }
}

server {
        listen 80;
        server_name  drone32.bplatform.demo;
        location / {
                proxy_set_header X-Forwarded-For $remote_addr;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_pass http://drone32.bplatform.demo:8001;
                proxy_hide_header X-Frame-Options;
        }
}

server {
        listen 8000;
        server_name localhost;
        location / {
                proxy_set_header X-Forwarded-For $remote_addr;
                proxy_set_header X-Forwarded-Proto $scheme;
                proxy_pass http://drone32.bplatform.demo:8001;
                proxy_hide_header X-Frame-Options;
        }
}

server {
        listen 80;
        server_name minio32.bplatform.demo;

        location / {
                proxy_pass http://minio32.bplatform.demo:9000;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;

        proxy_connect_timeout 300;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        chunked_transfer_encoding off;
        client_max_body_size 1000m;
        }
}
}
~~~