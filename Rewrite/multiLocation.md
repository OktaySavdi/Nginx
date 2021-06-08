```sh
server {
	listen  10.10.10.10:443 ssl;
	server_name  myapp.mydomain.com.tr;
	ssl_protocols TLSv1.2 TLSv1.3;
	#ssl_prefer_server_ciphers on;
	#ssl_ciphers TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256:TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384;
	
	ssl_certificate      /etc/nginx/mydomain.com.tr.crt;
	ssl_certificate_key  /etc/nginx/mydomain.com.tr.rsa;
	
	access_log           /web_log/myapp.access.log;
	error_log            /web_log/myapp.error.log;

	if ($scheme = http) {
		return 301 https://$host:443$request_uri;
	}

	location / {
		rewrite ^/$ /plan;
		if ($request_uri ~* ^/plan.*        ) { proxy_pass http://myweb.mydomain.com.tr:8080; }
		if ($request_uri ~* ^/Campaign.*     ) { proxy_pass http://myweb.mydomain.com.tr:8130; }
		if ($request_uri ~* ^/Offer.*        ) { proxy_pass http://myweb.mydomain.com.tr:8180; }
		if ($request_uri ~* ^/exam.*         ) { proxy_pass http://myweb.mydomain.com.tr:8230; }
		if ($request_uri ~* ^/design.*    ) { proxy_pass http://myweb.mydomain.com.tr:8280; }
		if ($request_uri ~* ^/view.* ) { proxy_pass http://myweb.mydomain.com.tr:8330; }
		if ($request_uri !~* ^/(plan|Campaign|Offer|exam|design|view).*) {return 404;}
		proxy_http_version 1.1;
		proxy_connect_timeout 60s;
		proxy_read_timeout 60s;
		proxy_send_timeout 60s;
		client_max_body_size 1m;
		proxy_ssl_server_name on;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Host $host;
		proxy_set_header X-Forwarded-Port $server_port;
		proxy_set_header X-Forwarded-Proto $scheme;
		proxy_buffering on;
		if ($http_origin ~* ^(https?://(.*fw\.mydomain\.com\.tr))$) { add_header Access-Control-Allow-Origin $http_origin; }
		add_header Access-Control-Allow-Credentials "true";
		add_header Access-Control-Allow-Headers "Accept, Origin, X-Requested-With, Content-Type, Last-Modified";
		add_header Strict-Transport-Security "max-age=63072000; includeSubDomains" always;
		add_header Content-Security-Policy "default-src 'self' data: blob: 'unsafe-eval' 'unsafe-inline' *.mydomainplan.com.tr *.mydomainexam.com.tr *.mydomainview.com.tr *.mydomain.com.tr *.mydomaindesign.com.tr;";
		add_header X-Content-Type-Options "nosniff";
		add_header X-Frame-Options "SAMEORIGIN" always;
		add_header X-XSS-Protection "1; mode=block";
		if ($request_method ~ ^(OPTIONS|TRACE|DELETE)$ ) { return 403; }
		
	}

	error_page   500 502 503 504  /50x.html;
	location = /50x.html {
		root   /usr/nginx/html;
	}
}
server {
	listen 10.10.10.10:80;
	server_name myapp.mydomain.com.tr;
	return 301 https://$host$request_uri;   ### Redirect
}
```
