```yaml
events {}

http {

  include mime.types;

  server {

    listen 80;
    server_name 167.99.93.26;

    root /sites/demo;

    rewrite ^/user/(\w+) /greet/$1 last;   ->redirect user to greet context root
    rewrite ^/greet/john /thumb.png;       ->jredirect ohn greet thumb.png

    location /greet {

      return 200 "Hello User";
    }

    location = /greet/john {
      return 200 "Hello John";
    }
  }
}
```		

```
http://IP/user/john
```		
		
		
		
