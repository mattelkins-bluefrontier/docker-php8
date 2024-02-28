# From
FROM mariadb
ENV MARIADB_VERSION=latest

# Copy configuration
COPY ./mariadb.cnf /etc/mysql/conf.d/custom.cnf:ro

# Logs
ENV LOG_DIR=/var/log/mariadb
