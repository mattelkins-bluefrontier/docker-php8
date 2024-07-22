# Args
ARG TAG=latest

# From
FROM mariadb:${TAG}

# Copy configuration
COPY ./mariadb.cnf /etc/mysql/conf.d/custom.cnf:ro

# Logs
ENV LOG_DIR=/var/log/mariadb
