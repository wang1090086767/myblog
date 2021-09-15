# Nginx 配置路径映射作为文件浏览器

## nginx 配置片段
nginx.conf加入如下 server 片段
~~~
server {
        listen 80;
        server_name ftp.bplatform.demo;
        location / {
                root /home/ftpusers/ftpu; # 映射的文件路径
                autoindex on; # 打开文件目录列表
                autoindex_exact_size on; # 显示文件大小 单位字节
                autoindex_localtime on; # 显示时间
                charset utf-8,gbk; # 设置编码防止中午乱码
        }
}
~~~