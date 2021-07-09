#FROM microsoft/mssql-server-linux
FROM mcr.microsoft.com/mssql/server:2019-latest
ENV ACCEPT_EULA=Y
ENV SA_PASSWORD=P4ssw0rd

ENTRYPOINT /opt/mssql/bin/sqlservr

RUN mkdir -p /opt/scripts

RUN echo "IF NOT EXISTS (SELECT name FROM sys.databases WHERE name = 'demo')" > /opt/scripts/install.sql
RUN echo "BEGIN"                                                              >> /opt/scripts/install.sql
RUN echo "  CREATE DATABASE demo"                                             >> /opt/scripts/install.sql
RUN echo "  ON (NAME=demo_dat,FILENAME=N'/data/demo.mdf')"                    >> /opt/scripts/install.sql
RUN echo "  LOG ON (NAME=demo_log, FILENAME=N'/data/demo.ldf')"               >> /opt/scripts/install.sql
RUN echo "END"                                                                >> /opt/scripts/install.sql
RUN echo "GO"                                                                 >> /opt/scripts/install.sql

RUN echo "IF NOT EXISTS (SELECT name FROM sys.databases WHERE name = 'demo')" > /opt/scripts/load.sql
RUN echo "BEGIN"                                                              >> /opt/scripts/load.sql
RUN echo "  CREATE DATABASE demo"                                             >> /opt/scripts/load.sql
RUN echo "  ON (FILENAME=N'/data/demo.mdf'),"                                 >> /opt/scripts/load.sql
RUN echo "     (FILENAME=N'/data/demo.ldf')"                                  >> /opt/scripts/load.sql
RUN echo "  FOR ATTACH"                                                       >> /opt/scripts/load.sql
RUN echo "END"                                                                >> /opt/scripts/load.sql
RUN echo "GO"                                                                 >> /opt/scripts/load.sql

CMD mkdir /data
