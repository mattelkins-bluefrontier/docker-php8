# From
FROM mariadb
ENV VERSION=latest

# Copy configuration
COPY ./mariadb.cnf /etc/mysql/conf.d/custom.cnf:ro

# Credentials
ENV MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD:-root}
ENV MYSQL_USER=${MYSQL_USER:-mariadb}
ENV MYSQL_PASSWORD=${MYSQL_PASSWORD:-mariadb}

# Logs
ENV LOG_DIR=/var/www/logs/mariadb-${VERSION}
