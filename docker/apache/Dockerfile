FROM httpd:2.4-alpine

ENV TZ=${TZ:-UTC}
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN apk add --update --no-cache tzdata

ENV WWW_ROOT=/var/www
ENV DOCUMENT_ROOT=${WWW_ROOT}/public_html

ENV LOG_DIR=/var/log/apache

COPY ./httpd.conf /usr/local/apache2/conf/httpd.conf
COPY ./httpd-vhosts.* /usr/local/apache2/conf/extra/httpd-vhosts.conf
