```sh
mkdir /etc/nginx/logrotate/
```
```sh
cat > /etc/nginx/logrotate/logrotate-nginx.conf << EOF
/var/nginx/logs/*log* {
create 0644 nginx nginx
daily
missingok
notifempty
dateext
dateformat -%Y-%m-%d
rotate 1000
compress
postrotate
/bin/kill -USR1 `cat /etc/nginx/run/nginx.pid 2>/dev/null` 2>/dev/null || true
endscript
su nginx nginx 
}
EOF
```
**Cronjob**
```sh
0 0 * * *  /usr/sbin/logrotate -f /etc/nginx/logrotate/logrotate-nginx.conf > /dev/null 2>&1
```
