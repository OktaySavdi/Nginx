https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange

https://hackernoon.com/algorithms-explained-diffie-hellman-1034210d5100

```
server {
    listen      443;
    ssl         on;
    server_name  company.savdi.local;
    ssl_certificate     /etc/nginx/company.com.crt;
    ssl_certificate_key /etc/nginx/company.com.rsa;


    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
       proxy_pass http://127.0.0.1:8080;
    }


    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```
