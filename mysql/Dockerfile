FROM mysql:8.0

# Default credentials
ENV MYSQL_ROOT_PASSWORD=Cloud122
ENV MYSQL_DATABASE=test
ENV MYSQL_USER=admin
ENV MYSQL_PASSWORD=Devops123

EXPOSE 3306

COPY test.sql /docker-entrypoint-initdb.d/

CMD ["mysqld"]
