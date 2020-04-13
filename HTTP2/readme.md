
[Example - Link](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-nginx-in-ubuntu-16-04)

```json
worker_processes auto;

events {
  worker_connections 1024;
}

http {

  include mime.types;

  server {

    listen 443 ssl http2;   
    server_name company.savdi.local;   #  In addition to the ssl connection configuration you can use http2 command with nginx as http2    

    root /sites/demo;
    index index.php index.html;

    ssl_certificate     /etc/nginx/company.com.crt;
    ssl_certificate_key /etc/nginx/company.com.rsa;

    location / {
      try_files $uri $uri/ =404;
    }
  }
}
```
