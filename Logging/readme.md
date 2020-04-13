```yaml
events {}

http {

  include mime.types;

  server {

    listen       80 default_server;
    listen       [::]:80 default_server;
    server_name  192.168.56.101;
    root         /usr/share/nginx/html;

    location /secure {

      # Add context specific log
      access_log /var/log/nginx/secure.access.log;
     error_log /var/log/nginx/error.log warn;        ####warn, error crit, alert,emerg levels are logged

      # Disable logs for context
      #access_log off;

      return 200 "Welcome to secure area.";
    }

  }
}
```
------------------------------Logging to Syslog----------------------------------------------

error_log server=unix:/var/log/nginx.sock debug;

access_log syslog:server=[2001:db8::1]:1234,facility=local7,tag=nginx,severity=info;

The facility= Other possible values are: auth, authpriv, daemon, cron, ftp, lpr, kern, mail, news, syslog, user, uucp, local0 ... local7.

The tag= parameter applies a custom tag to syslog messages (nginx in our example).

The severity= Possible values in order of increasing severity are: debug, info, notice, warn, error (default), crit, alert, and emerg.
Messages are logged at the specified level and all more severe levels. In our example, the severity level error also enables crit, alert, and emerg levels to be logged.


------------------------------Gzip----------------------------------------------

http {
    log_format compression '$remote_addr - $remote_user [$time_local] '
                           '"$request" $status $body_bytes_sent '
                           '"$http_referer" "$http_user_agent" "$gzip_ratio"';

    server {
        gzip on;
        access_log /spool/logs/nginx-access.log compression;
        ...
    }
}

------------------------------Stream----------------------------------------------

$upstream_connect_time – The time spent on establishing a connection with an upstream server

$upstream_header_time – The time between establishing a connection and receiving the first byte of the response header from the upstream server

$upstream_response_time – The time between establishing a connection and receiving the last byte of the response body from the upstream server

$request_time – The total time spent processing a request

http {
    log_format upstream_time '$remote_addr - $remote_user [$time_local] '
                             '"$request" $status $body_bytes_sent '
                             '"$http_referer" "$http_user_agent"'
                             'rt=$request_time uct="$upstream_connect_time" uht="$upstream_header_time" urt="$upstream_response_time"';

    server {
        access_log /spool/logs/nginx-access.log upstream_time;
        ...
    }
}

------------------------------Conditional----------------------------------------------

map $status $loggable {
    ~^[23]  0;
    default 1;
}

```
access_log /path/to/access.log combined if=$loggable;
```
