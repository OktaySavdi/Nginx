
## Nginx SNI
```
 server {

listen   443 ssl;
ssl_protocols  TLSv1 TLSv1.1 TLSv1.2;
server_name  MySitename.savdi.local;
ssl_certificate      /opt/cert/MyCert.crt;
ssl_certificate_key  /opt/cert/MyCert.rsa;

access_log    /var/log/nginx/MySitename.access.log;
error_log     /var/log/nginx/MySitename.error.log;

if ($scheme = http) { return 301 https://$host:443$request_uri; }
  
  location / {
        
        proxy_pass      http://MyNewAdress.savdi.local;
        
        proxy_http_version 1.1;
        proxy_connect_timeout 60s;
        proxy_read_timeout 60s;
        proxy_send_timeout 60s;
        client_max_body_size 1m;
        proxy_ssl_server_name on; ###
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Host $host;
        proxy_set_header X-Forwarded-Port $server_port;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_buffering on;
      }
  	
      error_page   500 502 503 504  /50x.html;
      location = /50x.html {
          root   /usr/share/nginx/html;
    }
}
```
