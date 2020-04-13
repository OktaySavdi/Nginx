```yaml

location /some/path/ {
    proxy_pass http://www.example.com/link/;
}

-------------------------------------------------------

location ~ \.php {
    proxy_pass http://127.0.0.1:8000;
}

-------------------------------------------------------
location /some/path/ {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_pass http://localhost:8000;
}

-------------------------------------------------------
location /some/path/ {
    proxy_set_header Accept-Encoding "";
    proxy_pass http://localhost:8000;
}

-------------------------------------------------------
location /some/path/ {
    proxy_buffers 16 4k;
    proxy_buffer_size 2k;
    proxy_pass http://localhost:8000;
}

-------------------------------------------------------
location /some/path/ {
    proxy_buffering off;
    proxy_pass http://localhost:8000;
}

-------------------------------------------------------
location /app1/ {
    proxy_bind 127.0.0.1;
    proxy_pass http://example.com/app1/;
}

location /app2/ {
    proxy_bind 127.0.0.2;
    proxy_pass http://example.com/app2/;
}

-------------------------------------------------------
location /app3/ {
    proxy_bind $server_addr;
    proxy_pass http://example.com/app3/;
}

```
