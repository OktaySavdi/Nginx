FROM registry.redhat.io/rhel8/nginx-118:latest as vars
RUN echo NGINX_ARGS=\"$(nginx -V 2>&1 | sed -n -e 's/^.*arguments: //p')\" >> variables && \
	echo NGINX_VERSION=\"$(nginx -v 2>&1 | awk -F "/" '{print $2}')\" >> variables

FROM registry.access.redhat.com/ubi8/s2i-base as module
COPY --from=vars /opt/app-root/src/variables /tmp/variables

USER 0

RUN source /tmp/variables && \
	INSTALL_PKGS="pcre pcre-devel perl-ExtUtils-Embed" && \
	yum install -y --nobest --setopt=tsflags=nodocs $INSTALL_PKGS && \
	rpm -V $INSTALL_PKGS && \
	yum -y clean all --enablerepo='*' && \
	curl -O http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz $(([[ -n $HTTP_PROXY ]] && echo "--proxy $HTTP_PROXY") || ([[ -n $HTTPS_PROXY ]] && echo "--proxy $HTTPS_PROXY")) && \
	curl -O https://codeload.github.com/openresty/headers-more-nginx-module/tar.gz/refs/tags/v0.33 $(([[ -n $HTTP_PROXY ]] && echo "--proxy $HTTP_PROXY") || ([[ -n $HTTPS_PROXY ]] && echo "--proxy $HTTPS_PROXY")) && \
	tar -xf nginx-${NGINX_VERSION}.tar.gz && \
	tar -xf v0.33 && \
	rm -f *.tar.gz v0.33 && \
	cd nginx-${NGINX_VERSION} && \
	eval "./configure --add-dynamic-module=$(pwd)/../headers-more-nginx-module-0.33 ${NGINX_ARGS}" && \
	make modules

FROM registry.redhat.io/rhel8/nginx-118:latest
USER 0

COPY --from=module /opt/app-root/src/nginx-*/objs/ngx_http_headers_more_filter_module.so /usr/lib64/nginx/modules/ngx_http_headers_more_filter_module.so
RUN echo "load_module \"/usr/lib64/nginx/modules/ngx_http_headers_more_filter_module.so\";" > /usr/share/nginx/modules/mod-http-headers-more-filter.conf && \
	sed -i "/.*http {.*/a \ \ \ \ more_clear_headers 'Server';" /etc/nginx/nginx.conf

USER 1001
