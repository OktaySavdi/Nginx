## Custom Nginx Image

	
· First, the necessary packages are downloaded to compile.
```bash
sudo yum group install -y "Development tools" #Needed to compile
sudo yum install -y gd gd-devel GeoIP GeoIP-devel libxslt libxslt-devel libxml2 libxml2-devel openssl-devel pcre pcre-devel perl perl-devel perl-ExtUt
```

 - After the necessary packages are installed, the most stable package
   is downloaded from http://nginx.org/en/download.html and extracted
   from tar.

 - The module required to hide HTTP Header is downloaded from
   https://github.com/openresty/headers-more-nginx-module/tags and
   removed from tar.	

 - Go to the Nginx installation directory.

      cd /PATH/TO/nginx-installation

 - It is downloaded to /**PATH/TO/headers-more-nginx-module-0.33** and
   extracted from tar.gz as follows Enter the full path to the
   headers-more module and then configure it.

```bash
./configure --add-dynamic-module=/tmp/headers-more-nginx-module-0.33 \
            --prefix=/opt/nginx \
            --sbin-path=/opt/nginx/sbin/nginx \
            --modules-path=/opt/nginx/lib64/nginx/modules \
            --conf-path=/opt/nginx/nginx.conf \
            --error-log-path=/log/error.log \
            --pid-path=/opt/nginx/run/nginx.pid \
            --lock-path=/opt/nginx/run/nginx.lock \
            --user=myuser \
            --group=myuser \
            --with-select_module \
            --with-poll_module \
            --with-threads \
            --with-file-aio \
            --with-http_ssl_module \
            --with-http_v2_module \
            --with-http_realip_module \
            --with-http_addition_module \
            --with-http_xslt_module=dynamic \
            --with-http_image_filter_module=dynamic \
            --with-http_geoip_module=dynamic \
            --with-http_sub_module \
            --with-http_dav_module \
            --with-http_flv_module \
            --with-http_mp4_module \
            --with-http_gunzip_module \
            --with-http_gzip_static_module \
            --with-http_auth_request_module \
            --with-http_perl_module=dynamic \
            --with-http_random_index_module \
            --with-http_secure_link_module \
            --with-http_degradation_module \
            --with-http_slice_module \
            --with-http_stub_status_module \
            --with-http_v2_module \
            --with-http_xslt_module=dynamic \
            --http-log-path=/web_log/access.log \
            --http-client-body-temp-path=/opt/nginx/cache/nginx/client_temp \
            --http-proxy-temp-path=/opt/nginx/cache/nginx/proxy_temp \
            --http-fastcgi-temp-path=/opt/nginx/cache/nginx/fastcgi_temp \
            --http-uwsgi-temp-path=/opt/nginx/cache/nginx/uwsgi_temp \
            --http-scgi-temp-path=/opt/nginx/cache/nginx/scgi_temp \
            --with-mail=dynamic \
            --with-mail_ssl_module \
            --with-stream=dynamic \
            --with-stream_ssl_module \
            --with-stream_realip_module \
            --with-stream_geoip_module=dynamic \
            --with-stream_ssl_preread_module \
            --with-compat \
            --with-pcre-jit \
            --with-openssl-opt=no-nextprotoneg \
            --with-debug
```
• In the last output of the **./configure** command, the folders where **nginx** will install will appear below.
```bash
nginx path prefix: "/opt/nginx"
nginx binary file: "/opt/nginx/sbin/nginx"
nginx modules path: "/opt/nginx/lib64/nginx/modules"
nginx configuration prefix: "/opt/nginx"
nginx configuration file: "/opt/nginx/nginx.conf"
nginx pid file: "/opt/nginx/run/nginx.pid"
nginx error log file: "/log/error.log"
nginx http access log file: "/log/access.log"
nginx http client request body temporary files: "/opt/nginx/cache/nginx/client_temp"
nginx http proxy temporary files: "/opt/nginx/cache/nginx/proxy_temp"
nginx http fastcgi temporary files: "/opt/nginx/cache/nginx/fastcgi_temp"
nginx http uwsgi temporary files: "/opt/nginx/cache/nginx/uwsgi_temp"
nginx http scgi temporary files: "/opt/nginx/cache/nginx/scgi_temp"
```

• Then the installation is terminated by saying **make** and **make install** in the nginx installation directory.
```bash
make
sudo make install
```
• With the following command, the **/usr/lib64/nginx/** modules folder is connected to the **/etc/nginx/** modules folder with **symlink**.  The reason we do this is to add dynamic modules **load_module modules/ngx_foo_module.so** to the nginx configuration; form, to be able to add without entering the full path.
```bash
sudo ln -s /opt/nginx/lib64/nginx/modules /opt/nginx/modules
```
• When Nginx is running, user and group information is created.
```bash
sudo useradd --system --home /opt/nginx/cache/nginx --shell /sbin/nologin --comment "nginx user" --user-group nginx
```
• With the following command, it is checked whether nginx is running or not and the file paths with errors are created.
```bash
sudo /usr/nginx/sbin/nginx -t
```
```
nginx: the configuration file /opt/nginx/nginx.conf syntax is ok nginx: 
[emerg] mkdir() "/opt/nginx/cache/nginx/client_temp" failed 
(2: No such file or directory) nginx: configuration file /opt/nginx/nginx.conf test failed
```
· Folders with errors are created and tested until the errors are fixed and a positive result is obtained.

· The folder that caused the error is created
```bash
sudo mkdir -p /opt/nginx/cache/nginx/client_temp && sudo nginx -t
```

· The service file is created.
```bash
sudo bash -c 'cat <<EOF > /usr/lib/systemd/system/nginx.service
[Unit]
Description=nginx - high performance web server
Documentation=https://nginx.org/en/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target
[Service]
Type=forking
PIDFile=/opt/nginx/run/nginx.pid
ExecStartPre=/opt/nginx/sbin/nginx -t -c /opt/nginx/nginx.conf
ExecStart=/opt/nginx/sbin/nginx -c /opt/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID
[Install]
WantedBy=multi-user.target
EOF
'
```
· After everything is done, Headers More module settings are made in the required /opt/nginx/nginx.conf file.

```nginx
user nginx;
.....................................................
load_module modules/ngx_http_headers_more_filter_module.so; #Modul soldaki gibi aktiflestirilir.
.....................................................
events {
    .....................................................;
}
.....................................................
http {
    .....................................................
    more_clear_headers 'Server'; #http bloğuna header bilgisi içeriği ornekte "Server" olarak girilir.
    .....................................................
}
```

· The Nginx.conf file is organized as follows.
```nginx,
#user  nobody;
worker_processes  1;

load_module modules/ngx_http_headers_more_filter_module.so;

events {
    worker_connections  1024;
}


http {
    include       /opt/nginx/mime.types;
    default_type  application/octet-stream;
    server_names_hash_bucket_size  128;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /log/access.log  main;
    error_log   /log/error.log;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;
    more_clear_headers 'Server';

    #gzip  on;
    charset         UTF-8;

    include /opt/nginx/conf.d/*.conf;
}
```

· The Nginx service is opened and activated.
```bash
nohup /opt/nginx/sbin/nginx > /dev/null 2>&1 &
```
or 
```bash
sudo systemctl start  nginx.service
sudo systemctl enable nginx.service
```
