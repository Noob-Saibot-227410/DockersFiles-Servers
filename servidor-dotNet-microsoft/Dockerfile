# Define a imagem base
FROM mcr.microsoft.com/dotnet/sdk:latest

# Limpa o cache dos pacotes
RUN apt-get clean

# Instala o wget, curl, gnupg e o apt-transport-https
RUN apt-get update && apt-get install -y curl wget gnupg apt-transport-https

# Importa a chave GPG do repositório do Visual Studio Code
RUN wget -q https://packages.microsoft.com/keys/microsoft.asc -O- | gpg --dearmor > microsoft.asc.gpg \
    && mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/

# Configura o repositório do Visual Studio Code
RUN echo "deb [arch=amd64 signed-by=/etc/apt/trusted.gpg.d/microsoft.asc.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list

# Atualiza o sistema e instala o Visual Studio Code
RUN apt-get update && apt-get install -y code

# Define o diretório de trabalho
WORKDIR /desktop-app

# Copia o arquivo de projeto para o diretório de trabalho
# COPY . .

# Restaura as dependências do projeto
# RUN dotnet restore

# Build do projeto
# RUN dotnet build

# Define a porta de debug
EXPOSE 5000

# Define o comando de inicialização do projeto
CMD ["dotnet", "run"]
