# Nginx Worker Process


```yaml
user www-data;

worker_processes auto;       # Do the same with the recommended number of cores (see how many cores with lscpu or nproc) for best practice or auto

events {
  worker_connections 1024;   # You can see how many connections you can get with ulimit -n ps aux | grep nginx
}

http {

  include mime.types;

  server {

    listen 80;
    server_name 167.99.93.26;

    root /sites/demo;

  }
}
```

