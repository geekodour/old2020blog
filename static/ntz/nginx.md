# Nginx Notes

## HLS and VOD
Differences between *streaming*, *progressive downloads*, *adaptive streaming*

## Installing
Installing Nginx have to be from source if you need to add modules or remove them.
You can write a bash script to have the variables there.

My Sample Installation Script:
```
export BUILD_DIR=~/nginx
export NGINX_DIR=/etc/nginx
export SBIN_DIR=/usr/sbin
export PID_DIR=/var/run
export LOCK_DIR=/var/run
export LOG_DIR=/var/log/nginx
export RUN_DIR=/var/run
export CACHE_DIR=/var/cache
cd ${BUILD_DIR}
./configure \
 --prefix=${NGINX_DIR} \
 --sbin-path=${SBIN_DIR}/nginx \
 --conf-path=${NGINX_DIR}/nginx.conf \
 --pid-path=${PID_DIR}/nginx.pid \
 --lock-path=${LOCK_DIR}/nginx.lock \
 --error-log-path=${LOG_DIR}/error.log \
 --http-log-path=${LOG_DIR}/access.log \
 --http-client-body-temp-path=${CACHE_DIR}/client_body_temp \
 --http-fastcgi-temp-path=${CACHE_DIR}/fastcgi_temp \
 --with-http_gzip_static_module \
 --with-http_stub_status_module \
 --with-http_ssl_module \
 --with-pcre \
 --with-file-aio \
 --with-http_realip_module \
 --without-http_scgi_module \
 --without-http_uwsgi_module \
 --without-http_proxy_module \
 --user=nginx \
 --group=nginx \
```
