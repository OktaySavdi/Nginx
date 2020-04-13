```yaml
http {
    upstream myapp1 {                               # list of servers to load balancing
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
    }

    server {
        listen 80;

        location / {
            proxy_pass http://myapp1;               # load balance address
        }
    }
}
-----------------------------------------------
resolver 10.0.0.1;

upstream dynamic {
    zone upstream_dynamic 64k;

    server backend1.example.com      weight=5;
    server backend2.example.com:8080 fail_timeout=5s slow_start=30s;
    server 192.0.2.1                 max_fails=3;
    server backend3.example.com      resolve;
    server backend4.example.com      service=http resolve;

    server backup1.example.com:8080  backup;
    server backup2.example.com:8080  backup;
}

server {
    location / {
        proxy_pass http://dynamic;
        health_check;
    }
}
-----------------------------------------------
upstream myapp1 {                                
        least_conn;                             
        server srv1.example.com;
        server srv2.example.com;
        server srv3.example.com;
    }
-----------------------------------------------
upstream myapp1 {
    ip_hash;                                     # ensures that all requests from the client always go to the same client
    server srv1.example.com;
    server srv2.example.com;
    server srv3.example.com;
}
-----------------------------------------------
upstream myapp1 {
        server srv1.example.com weight=3;
        server srv2.example.com;
        server srv3.example.com;
    }
-----------------------------------------------
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com down;
}
-----------------------------------------------
upstream backend {
    hash $request_uri consistent;
    server backend1.example.com;
    server backend2.example.com;
}
-----------------------------------------------
upstream backend {
    least_time header;
    server backend1.example.com;
    server backend2.example.com;
}
-----------------------------------------------
upstream backend {
    random two least_time=last_byte;
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
    server backend4.example.com;
}
```
