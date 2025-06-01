# Usar una imagen base de Nginx.
FROM nginx:1.25-alpine

# Variables de entorno para versiones
ENV MODSECURITY_VERSION=3.0.12
ENV MODSECURITY_CONNECTOR_VERSION=1.0.3
ENV OWASP_CRS_VERSION=3.3.5
ENV NGINX_VERSION=1.25.5 

# Instalar dependencias
RUN apk add --no-cache \
    build-base \
    automake \
    autoconf \
    libtool \
    pcre-dev \
    libxml2-dev \
    curl-dev \
    yajl-dev \
    geoip-dev \
    git \
    apache2-utils \
    linux-headers

# Descargar y compilar libModSecurity
RUN cd /tmp && \
    git clone --depth 1 -b v${MODSECURITY_VERSION} https://github.com/SpiderLabs/ModSecurity && \
    cd ModSecurity && \
    git submodule init && \
    git submodule update && \
    ./build.sh && \
    ./configure --enable-examples=no --disable-doxygen-doc --with-yajl --with-geoip && \
    make && \
    make install && \
    rm -rf /tmp/ModSecurity

# Descargar y compilar el conector de ModSecurity para Nginx
RUN cd /tmp && \
    git clone --depth 1 -b v${MODSECURITY_CONNECTOR_VERSION} https://github.com/SpiderLabs/ModSecurity-nginx.git && \
    rm -rf /tmp/ModSecurity-nginx/.git

# Descargar Nginx (misma versión que la imagen base) para compilar el módulo dinámico
RUN cd /tmp && \
    curl -sSL http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz | tar -C /tmp -xz && \
    cd /tmp/nginx-${NGINX_VERSION} && \
    ./configure \
        --with-compat \
        --add-dynamic-module=/tmp/ModSecurity-nginx && \
    make modules && \
    cp objs/ngx_http_modsecurity_module.so /etc/nginx/modules/ && \
    rm -rf /tmp/nginx-${NGINX_VERSION} /tmp/ModSecurity-nginx

# Crear directorios necesarios para ModSecurity
RUN mkdir -p /etc/nginx/modsecurity.d/rules \
             /var/log/modsecurity \
             /opt/modsecurity/var/data && \
    chown -R nginx:nginx /var/log/modsecurity /opt/modsecurity/var/data

# Descargar OWASP Core Rule Set
RUN cd /etc/nginx/modsecurity.d && \
    git clone --depth 1 -b v${OWASP_CRS_VERSION} https://github.com/coreruleset/coreruleset.git && \
    mv coreruleset owasp-crs && \
    cp owasp-crs/crs-setup.conf.example owasp-crs/crs-setup.conf && \
    cd owasp-crs/rules && \
    for f in *.example; do cp -- "$f" "${f%.example}"; done && \
    cd ../..


# Copiar archivos de configuración
COPY ./modsecurity.d/modsecurity.conf /etc/nginx/modsecurity.d/modsecurity.conf
COPY ./nginx.conf /etc/nginx/nginx.conf

# Exponer puerto
EXPOSE 80 443