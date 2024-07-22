# Args
ARG TAG=latest

# From
FROM mysql:${TAG}

# Copy configuration
COPY ./mysql.cnf /etc/mysql/conf.d/custom.cnf:ro

# Logs
ENV LOG_DIR=/var/log/mysql
