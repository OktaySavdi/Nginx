# Nginx Redirect

```yaml

http {
    
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        location /logo {
        return 307 /thumb.png      ### Redirect
        }
    }
    
--------------------------------------

  server {
    listen 80;
    server_name 167.99.93.26;
    return 301 https://$host$request_uri;   ### Redirect
  }
  ```
