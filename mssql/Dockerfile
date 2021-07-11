#FROM microsoft/mssql-server-linux
FROM mcr.microsoft.com/mssql/server:2019-latest
ENV ACCEPT_EULA=Y
ENV SA_PASSWORD=P4ssw0rd

#USER root
#RUN apt-get update && apt-get install -y vim curl

RUN mkdir -p /opt/scripts

RUN echo "IF NOT EXISTS (SELECT name FROM sys.databases WHERE name = 'demo')" > /opt/scripts/install.sql
RUN echo "  CREATE DATABASE demo"                                             >> /opt/scripts/install.sql
RUN echo "  ON (NAME=demo_dat,FILENAME=N'/data/demo.mdf')"                    >> /opt/scripts/install.sql
RUN echo "  LOG ON (NAME=demo_log, FILENAME=N'/data/demo.ldf')"               >> /opt/scripts/install.sql
RUN echo "GO"                                                                 >> /opt/scripts/install.sql

RUN echo "IF NOT EXISTS (SELECT name FROM sysobjects WHERE name='cars' AND xtype='U')" > /opt/scripts/create.sql
RUN echo "  CREATE TABLE Movie ("                                                      >> /opt/scripts/create.sql
RUN echo "   [ID]  INT Identity (1,1) NOT NULL,"                                       >> /opt/scripts/create.sql
RUN echo "   [Genre] nvarchar(MAX) null,"                                              >> /opt/scripts/create.sql
RUN echo "   [Price] decimal(18,2) not null,"                                          >> /opt/scripts/create.sql
RUN echo "   [Rating] nvarchar(MAX) null,"                                             >> /opt/scripts/create.sql
RUN echo "   [ReleaseDate] datetime2 (7) not null,"                                    >> /opt/scripts/create.sql
RUN echo "   [Title] nvarchar(MAX) NULL,"                                              >> /opt/scripts/create.sql
RUN echo "   Constraint [PK_Movie] Primary Key Clustered ([ID] Asc)"                   >> /opt/scripts/create.sql
RUN echo "  )"                                                                         >> /opt/scripts/create.sql
RUN echo "GO"                                                                          >> /opt/scripts/create.sql

RUN echo "IF NOT EXISTS (SELECT name FROM sys.databases WHERE name = 'demo')" > /opt/scripts/load.sql
RUN echo "  CREATE DATABASE demo"                                             >> /opt/scripts/load.sql
RUN echo "  ON (FILENAME=N'/data/demo.mdf'),"                                 >> /opt/scripts/load.sql
RUN echo "     (FILENAME=N'/data/demo.ldf')"                                  >> /opt/scripts/load.sql
RUN echo "  FOR ATTACH"                                                       >> /opt/scripts/load.sql
RUN echo "GO"                                                                 >> /opt/scripts/load.sql

RUN echo "#!/bin/bash -x"                                                                      > /opt/scripts/install_and_test_bd.sh
RUN echo "if test -f '/data/demo.mdf'; then"                                                   >> /opt/scripts/install_and_test_bd.sh
RUN echo "   echo 'loading db from file'"                                                      >> /opt/scripts/install_and_test_bd.sh
RUN echo "  /opt/mssql-tools/bin/sqlcmd -U sa -P P4ssw0rd -i /opt/scripts/load.sql"            >> /opt/scripts/install_and_test_bd.sh
RUN echo "else"                                                                                >> /opt/scripts/install_and_test_bd.sh
RUN echo "   echo 'creating bd'"                                                               >> /opt/scripts/install_and_test_bd.sh
RUN echo "  /opt/mssql-tools/bin/sqlcmd -U sa -P P4ssw0rd -i /opt/scripts/install.sql"         >> /opt/scripts/install_and_test_bd.sh
RUN echo "  /opt/mssql-tools/bin/sqlcmd -U sa -P P4ssw0rd -d demo -i /opt/scripts/create.sql"  >> /opt/scripts/install_and_test_bd.sh
RUN echo "fi"                                                                                  >> /opt/scripts/install_and_test_bd.sh
RUN echo "/opt/mssql-tools/bin/sqlcmd -U sa -P P4ssw0rd -Q 'SELECT 1'"                         >> /opt/scripts/install_and_test_bd.sh

RUN chmod +x /opt/scripts/install_and_test_bd.sh

CMD mkdir /data

ENTRYPOINT /opt/mssql/bin/sqlservr

