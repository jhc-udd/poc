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
RUN echo "  CREATE TABLE Movie ("                                             >> /opt/scripts/install.sql
RUN echo "   [ID]  INT Identity (1,1) NOT NULL,"                              >> /opt/scripts/install.sql
RUN echo "   [Genre] nvarchar(MAX) null,"                                     >> /opt/scripts/install.sql
RUN echo "   [Price] decimal(18,2) not null,"                                 >> /opt/scripts/install.sql
RUN echo "   [Rating] nvarchar(MAX) null,"                                    >> /opt/scripts/install.sql
RUN echo "   [ReleaseDate] datetime2 (7) not null,"                           >> /opt/scripts/install.sql
RUN echo "   [Title] nvarchar(MAX) NULL,"                                     >> /opt/scripts/install.sql
RUN echo "   Constraint [PK_Movie] Primary Key Clustered ([ID] Asc)"          >> /opt/scripts/install.sql
RUN echo " )"                                                                 >> /opt/scripts/install.sql
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

RUN echo "#!/bin/bash"                                                                  > /opt/scripts/install_and_test_bd.sh
RUN echo "if test -f '/data/demo.mdf'; then"                                            > /opt/scripts/install_and_test_bd.sh
RUN echo "  /opt/mssql-tools/bin/sqlcmd -U sa -P P4ssw0rd -i /opt/scripts/load.sql"     >> /opt/scripts/install_and_test_bd.sh
RUN echo "else"                                                                         >> /opt/scripts/install_and_test_bd.sh
RUN echo "  /opt/mssql-tools/bin/sqlcmd -U sa -P P4ssw0rd -i /opt/scripts/install.sql"  >> /opt/scripts/install_and_test_bd.sh
RUN echo "fi"                                                                           >> /opt/scripts/install_and_test_bd.sh
RUN echo "/opt/mssql-tools/bin/sqlcmd -U sa -P P4ssw0rd -Q 'SELECT 1'"                  >> /opt/scripts/install_and_test_bd.sh

RUN chmod +x /opt/scripts/install_and_test_bd.sh

CMD mkdir /data
