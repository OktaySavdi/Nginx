

## Nginx Best Practice

**1- Disable swap**

    vi /etc/fstab 

    #/dev/mapper/rhel-swap   swap                    swap    defaults        0 0

**2- After swap disable reboot machine**

**3- Set ulimit**

    ulimit -n 65535

**4- Add parameter for "sysctl.conf"**

    vi /etc/sysctl.conf

**#Decrease TIME_WAIT seconds**

    net.ipv4.tcp_fin_timeout = 30

**#Recycle and Reuse TIME_WAIT sockets faster**

    net.ipv4.tcp_max_syn_backlog=65535
    net.core.somaxconn=65535

**#enable selective acknowledgements**

    net.ipv4.tcp_sack=1

**#needed for selective acknowledgements**

    net.ipv4.tcp_timestamps=1

**# scale the network window**         		  

    net.ipv4.tcp_window_scaling=1 

**# better congestion algorythm**

    net.ipv4.tcp_congestion_control=cubic

**# enable syn cookied**

    net.ipv4.tcp_syncookies=1

**# recycle sockets quickly**

    net.ipv4.tcp_tw_recycle=1
    vm.overcommit_memory = 1

**#Avoid a smurf attack**

    net.ipv4.icmp_echo_ignore_broadcasts = 1 

**#Turn on protection for bad icmp error messages**

    net.ipv4.icmp_ignore_bogus_error_responses = 1

**#Turn on syncookies for SYN flood attack protection**

    net.ipv4.tcp_syncookies = 1

**# Increase system file descriptor limit**
   

     fs.file-max = 65535

**5- Closing Transparent Huge Pages**

    echo 'never' > /sys/kernel/mm/transparent_hugepage/enabled
    echo 'never' > /sys/kernel/mm/transparent_hugepage/defrag

These settings are lost when the machine restarts
The "disable-thp" file must be added as an init script to be performed each time the system is booted

    mv disable-thp /etc/init.d/
    chmod +x /etc/init.d/disable-thp
    chkconfig --add disable-thp

**6- Change Nginx Version Header**
Edit src/http/ngx_http_header_filter_module.c, enter:  
`# vi +48 src/http/ngx_http_header_filter_module.c`

Find line

    static char ngx_http_server_string[] = "Server: nginx" CRLF;
    static char ngx_http_server_full_string[] = "Server: " NGINX_VER CRLF;

Change them as follows:

    static char ngx_http_server_string[] = "Server: Ninja Web Server" CRLF;
    static char ngx_http_server_full_string[] = "Server: Ninja Web Server" CRLF;

Save and close the file. Now, you can compile the server. Add the following in nginx.conf to turn off nginx version number displayed on all auto generated error pages:

    server_tokens off

**7- Linux Iptables: Throttle Nginx Connections Per Second**

    /sbin/iptables -A INPUT -p tcp --dport 80 -i eth0 -m state --state NEW -m recent --set
    /sbin/iptables -A INPUT -p tcp --dport 80 -i eth0 -m state --state NEW -m recent --update --seconds 60  --hitcount 15 -j DROP
    service iptables save
