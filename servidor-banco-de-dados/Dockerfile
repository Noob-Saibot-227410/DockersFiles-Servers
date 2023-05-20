# Imagem base
FROM ubuntu:latest

# Instalação das dependências
RUN apt-get update && apt-get install -y \
    curl \
    gnupg \
    apt-transport-https \
    lsb-release \
    wget

# Definição explícita da região
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "tzdata tzdata/Areas select America" > /tmp/preseed.txt && \
    echo "tzdata tzdata/Zones/America select New_York" >> /tmp/preseed.txt && \
    debconf-set-selections /tmp/preseed.txt && \
    apt-get install -y tzdata && \
    rm /tmp/preseed.txt

# Instalação do PostgreSQL
RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" >> /etc/apt/sources.list.d/pgdg.list && \
    curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - && \
    apt-get update && apt-get install -y postgresql

# Instalação do MySQL
RUN apt-get install -y mysql-server

# Instalação do SQL Server
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - && \
    curl https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/prod.list > /etc/apt/sources.list.d/mssql-release.list && \
    apt-get update && ACCEPT_EULA=Y apt-get install -y msodbcsql17 mssql-tools && \
    echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> /root/.bashrc

# Instalação do MariaDB
RUN apt-get install -y mariadb-server

# Instalação do DBeaver
RUN wget -O dbeaver.tar.gz https://dbeaver.io/files/dbeaver-ce-latest-linux.gtk.x86_64.tar.gz && \
    tar -xzf dbeaver.tar.gz -C /opt && \
    rm dbeaver.tar.gz

# Variáveis de ambiente
ENV PATH="/opt/dbeaver:${PATH}"

# Configuração e inicialização dos bancos de dados
RUN service postgresql start && \
    su - postgres -c "psql -c \"CREATE USER admin WITH PASSWORD 'admin';\"" && \
    su - postgres -c "psql -c \"CREATE DATABASE mydatabase OWNER admin;\""

RUN service mysql start && \
    echo "UPDATE mysql.user SET plugin = 'mysql_native_password', authentication_string = PASSWORD('admin') WHERE User = 'root';" | mysql -u root && \
    echo "FLUSH PRIVILEGES;" | mysql -u root

RUN /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P 'admin' -Q "CREATE LOGIN [admin] WITH PASSWORD=N'admin', DEFAULT_DATABASE=[master], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF;"

RUN service mysql start && \
    echo "CREATE USER admin IDENTIFIED BY 'admin';" | mysql -u root && \
    echo "GRANT ALL PRIVILEGES ON *.* TO 'admin'@'localhost';" | mysql -u root && \
    echo "FLUSH PRIVILEGES;" | mysql -u root

# Portas expostas
EXPOSE 5432 3306 1433

# Comando de inicialização
CMD ["/bin/bash"]
