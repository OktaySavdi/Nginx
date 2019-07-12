

## Nginx Usefull Commands

**#Test Nginx Configuration**

    nginx -t

**#Check Nginx Version**

    nginx -v
    systemctl -v nginx

**#Show Command Help**

    nginx -h

**#Start Nginx**

    nginx -s start
    systemctl start nginx

**#Stop Nginx**

    nginx -s stop
    systemctl stop nginx

**#Nginx to stop even faster**

    killall -9 nginx

**#Restart Nginx**

    systemctl restart nginx

**#Reload Nginx**

    nginx -s reload
    systemctl reload nginx

**#View Server Status**

    systemctl status nginx

**#Is the port really open and the service listening?**

    ss -ltn
    lsof -P -n -i :80 -i :443 | grep LISTEN

$ ss -ltn
State      Recv-Q Send-Q        Local Address:Port          Peer Address:Port 
LISTEN     0      128                       *:80                       *:*     
LISTEN     0      128                       *:443                      *:*

**#Check the default main log files**

    tail -f /var/log/nginx/access.log /var/log/nginx/error.log

**#Check the permissions and full path access to the files accessed by Nginx**

    namei -om /usr/share/nginx/html/index.html

**#Enable debug mode**
`error_log /var/logs/nginx/error.log debug;` **#/etc/nginx/nginx.conf file**

**#Check your DNS records properly configured**

    $ dig -t A serverdensity.com

serverdensity.com has address 104.16.63.34
serverdensity.com has address 104.16.64.34

**#Too many open files**
`worker_rlimit_nofile 8192;`  **#/etc/nginx/nginx.conf file**

**#Address already in use**
[emerg]: bind() to 0.0.0.0:80 failed (98: Address already in use)

    fuser -k 80/tcp or pkill culprit_process

**#Reaching maximum POST size**
[error] 2589#0: *6437 client intended to send too large body: 13106010 bytes, client: 127.0.0.1, request: "POST /upload/ HTTP/1.1", host: "www.example.com", referrer: "http://www.example.com/upload/"
`client_max_body_size 10m;`   **#/etc/nginx/nginx.conf file**
