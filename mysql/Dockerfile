FROM mysql

ENV MYSQL_RANDOM_ROOT_PASSWORD yes
ADD jsp_backup.sql /docker-entrypoint-initdb.d

EXPOSE 3306