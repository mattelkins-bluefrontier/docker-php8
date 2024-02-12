# From
FROM mysql
ENV VERSION=latest

# Copy configuration
COPY ./mysql.cnf /etc/mysql/conf.d/custom.cnf:ro

# Credentials
ENV MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD:-root}
ENV MYSQL_USER=${MYSQL_USER:-mysql}
ENV MYSQL_PASSWORD=${MYSQL_PASSWORD:-mysql}

# Logs
ENV LOG_DIR=/var/www/logs/mysql-${VERSION}
