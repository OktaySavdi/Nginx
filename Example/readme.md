## Nginx Example
```json
  upstream maps {
    server 10.98.15.30:9081;
    server 10.98.15.31:9081;
  }
  
server {
    listen       80;
    server_name  10.98.15.63;#halkbank.com.tr
	
	return 301 https://$host$request_uri; #redirect https   
}
server {

listen   443 ssl;

ssl_certificate    /opt/cert/nginx-selfsigned.crt;
ssl_certificate_key    /opt/cert/nginx-selfsigned.key;

  server_name 10.98.15.63;
  
  location /Sample1/SimpleServlet {
        proxy_pass      http://maps;
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
