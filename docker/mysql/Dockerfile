# From
FROM mysql
ENV MYSQL_VERSION=latest

# Copy configuration
COPY ./mysql.cnf /etc/mysql/conf.d/custom.cnf:ro

# Logs
ENV LOG_DIR=/var/log/mysql
