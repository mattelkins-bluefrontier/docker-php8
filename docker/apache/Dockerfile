FROM httpd:2.4-alpine

ENV TZ=${TZ:-UTC}
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN apk add --update --no-cache tzdata

ENV SERVER_NAME=localhost
ENV WWW_ROOT=/var/www
ENV DOCUMENT_ROOT=${WWW_ROOT}/public_html

RUN mkdir -p ${WWW_ROOT}/logs/apache
ENV APACHE_LOG_DIR=${WWW_ROOT}/logs/apache

#ENV APACHE_RUN_GROUP=www-data
#ENV APACHE_RUN_USER=www-data

WORKDIR ${WWW_ROOT}

COPY ./httpd.conf /usr/local/apache2/conf/httpd.conf
