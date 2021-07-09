#FROM microsoft/mssql-server-linux
FROM mcr.microsoft.com/mssql/server:2019-latest
ENV ACCEPT_EULA=Y
ENV SA_PASSWORD=P4ssw0rd

ENTRYPOINT /opt/mssql/bin/sqlservr

RUN mkdir -p /opt/scripts

RUN echo "IF NOT EXISTS (SELECT name FROM sys.databases WHERE name = 'demo') "\
         "BEGIN" \
         "CREATE DATABASE demo" \ 
         "ON (NAME=demo_dat,FILENAME=N'/data/demo.mdf')"  \
         "LOG ON (NAME=demo_log, FILENAME=N'/data/demo.ldf')" \
         "END;" \
         "GO" > /opt/scripts/install.sql

RUN echo "IF NOT EXISTS (SELECT name FROM sys.databases WHERE name = 'demo') "\
         "BEGIN" \
         "CREATE DATABASE demo ON (FILENAME=N'/data/demo.mdf'), (FILENAME=N'/data/demo.ldf') FOR ATTACH;" \
         "END;" \
         "GO" > /opt/scripts/load.sql

CMD mkdir /data
