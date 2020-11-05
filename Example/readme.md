## Nginx Example
```
  upstream maps {
    server 10.10.10.10:9081;
    server 10.10.10.11:9081;
  }
  
server {
    listen       80;
    server_name  11.11.11.11
	
    return 301 https://$host$request_uri; #redirect https   
}
server {

listen   443 ssl;
ssl      on;
server_name  MySitename.savdi.local;
ssl_certificate      /opt/cert/MyCert.crt;
ssl_certificate_key  /opt/cert/MyCert.rsa;
  
  location /Sample1/SimpleServlet {
        proxy_pass      http://maps;
	proxy_http_version 1.1;
  	proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Frame-Options "deny";
      }
  	
      error_page   500 502 503 504  /50x.html;
      location = /50x.html {
          root   /usr/share/nginx/html;
    }
}
```
