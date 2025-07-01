# Instalação do GLPI com Docker Compose

Este Documento detalha como configurar e executar o GLPI (Gestionnaire Libre de Parc Informatique) usando Docker Compose, baseado nos arquivos `docker-compose.yml` e `.env` fornecidos. Esta configuração utiliza uma imagem específica do GLPI e um banco de dados MariaDB.

## Pré-requisitos

Antes de começar, certifique-se de ter os seguintes softwares instalados em seu sistema:

* **Docker:** Para a virtualização de contêineres.
    * [Guia de Instalação do Docker](https://docs.docker.com/get-docker/)
* **Docker Compose:** Para orquestrar múltiplos contêineres Docker.
    * Geralmente, vem junto com a instalação do Docker Desktop ou pode ser instalado separadamente.

## Estrutura do Projeto

Vamos organizar os arquivos em um diretório dedicado.

```
glpi-docker/
├── .env
└── docker-compose.yml
```

## Passo 1: Criar o Diretório do Projeto

Primeiro, crie um diretório para o seu projeto GLPI e navegue até ele no terminal:

```bash
mkdir glpi-docker
cd glpi-docker
```

## Passo 2: Criar o Arquivo `.env`

O arquivo `.env` conterá variáveis de ambiente sensíveis e de configuração. Crie um arquivo chamado `.env` (sem extensão) dentro do diretório `glpi-docker` e adicione o seguinte conteúdo:

```env
# Variáveis do banco de dados
MYSQL_ROOT_PASSWORD=SUASENHA_ROOT_DO_MYSQL # Altere para uma senha forte
MYSQL_DATABASE=glpidb
MYSQL_USER=glpi
MYSQL_PASSWORD=SUASENHA_DO_GLPI # Altere para uma senha forte

# Configuração do GLPI
TIMEZONE=America/Sao_Paulo
```

> [!WARNING]
> Substitua `SUASENHA_ROOT_DO_MYSQL` e `SUASENHA_DO_GLPI` por senhas fortes e exclusivas. Este arquivo não deve ser versionado em repositórios públicos.

## Passo 3: Criar o Arquivo `docker-compose.yml`

Este arquivo define os serviços Docker (`glpi` e `db`), suas configurações, volumes persistentes e como eles se comunicam. Crie um arquivo chamado `docker-compose.yml` dentro do diretório `glpi-docker` e adicione o seguinte conteúdo:

```yaml
version: '3.8'  # Versão do Docker Compose, compatível com recursos modernos

services:
  glpi:  # Serviço do GLPI (frontend web)
    image: elestio4test/glpi:latest  # Imagem Docker do GLPI mantida pela Elestio
    container_name: glpi-app  # Nome do container (opcional, facilita identificar no Docker)
    restart: unless-stopped  # Reinicia automaticamente, exceto se for parado manualmente

    ports:
      - "8080:80"  # Mapeia a porta 80 do container para a porta 8080 do host (acesso via navegador)

    environment:
      TIMEZONE: ${TIMEZONE}  # Define o fuso horário, usando variável do arquivo .env

    volumes:
      - /DATA/GLPI/html:/var/www/html  # Volume persistente com os arquivos da aplicação
      - /DATA/GLPI/plugins:/var/www/html/plugins  # Volume persistente apenas para plugins

    depends_on:
      - db  # Aguarda o banco de dados estar disponível antes de iniciar o GLPI

    networks:
      - glpi-net  # Usa rede interna personalizada para comunicação com o banco de dados

  db:  # Serviço do banco de dados MariaDB
    image: mariadb:10.5  # Imagem oficial MariaDB (versão compatível com GLPI)
    container_name: glpi-db  # Nome do container do banco
    restart: unless-stopped  # Reinício automático padrão

    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}  # Senha do usuário root (do .env)
      MYSQL_DATABASE: ${MYSQL_DATABASE}  # Nome do banco a ser criado (do .env)
      MYSQL_USER: ${MYSQL_USER}  # Usuário que o GLPI usará (do .env)
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}  # Senha do usuário GLPI (do .env)

    volumes:
      - db_data:/var/lib/mysql  # Volume persistente dos dados do banco

    networks:
      - glpi-net  # Mesmo rede interna para se comunicar com o GLPI

volumes:
  db_data:  # Volume nomeado que armazena os dados do banco de forma persistente

networks:
  glpi-net:  # Rede privada para os serviços do GLPI e banco de dados
    driver: bridge  # Usa o driver padrão "bridge"
```

## Passo 4: Iniciar os Contêineres Docker

Com os arquivos `.env` e `docker-compose.yml` criados no mesmo diretório, navegue até o diretório `glpi-docker` no seu terminal e execute o seguinte comando para iniciar os serviços:

```bash
docker compose up -d
```

* `docker compose up`: Inicia os serviços definidos no `docker-compose.yml`.
* `-d`: Executa os contêineres em segundo plano (modo "detached").

Aguarde alguns minutos para que as imagens sejam baixadas (se for a primeira vez) e os contêineres iniciem. Você pode verificar o status dos contêineres com:

```bash
docker compose ps
```

## Passo 5: Acessar o GLPI e Concluir a Instalação

Após os contêineres estarem em execução, abra seu navegador web e acesse o GLPI no endereço:

```
http://localhost:8080
```

Você será redirecionado para o assistente de instalação do GLPI. Siga os passos na tela:

1.  **Escolha o idioma.**
2.  **Aceite a licença.**
3.  **Verificação de compatibilidade:** O Docker se encarregará da maioria dos pré-requisitos, então você deve ver tudo em ordem.
4.  **Configuração da Conexão com o Banco de Dados:**
    * **Servidor MySQL/MariaDB:** `db` (este é o nome do serviço do banco de dados no seu `docker-compose.yml`).
    * **Usuário MySQL/MariaDB:** `glpi` (do seu `.env`).
    * **Senha MySQL/MariaDB:** A senha que você definiu para `MYSQL_PASSWORD` no seu `.env`.
    * **Nome do Banco de Dados GLPI:** `glpidb` (do seu `.env`).

    Clique em "Testar a conexão". Se a conexão for bem-sucedida, prossiga.
5.  **Criação/Atualização do Banco de Dados:** Escolha "Instalar" para que o GLPI crie o esquema do banco de dados necessário.
6.  **Configuração Padrão:** O GLPI realizará algumas configurações iniciais automaticamente.
7.  **Finalização:** Uma tela confirmará que a instalação foi concluída com sucesso. Anote os usuários e senhas padrão do GLPI para o primeiro acesso (geralmente `glpi/glpi`, `tech/tech`, `post-only/post-only`, `normal/normal`).
8.  **Remover o arquivo `install.php`:** Por segurança, é recomendado remover o arquivo de instalação após a conclusão. Execute o comando:

    ```bash
    docker compose exec glpi-app rm /var/www/html/install/install.php
    ```

    Este comando executa a remoção dentro do contêiner `glpi-app`.

## Comandos Úteis do Docker Compose

* **Parar os contêineres (sem remover dados):**
    ```bash
    docker compose down
    ```
* **Remover os contêineres, redes e volumes (APAGA OS DADOS PERSISTENTES!):**
    ```bash
    docker compose down -v
    ```
* **Visualizar logs dos serviços:**
    ```bash
    docker compose logs -f
    ```
    (Adicione o nome do serviço para logs específicos, ex: `docker compose logs -f glpi-app`)
* **Atualizar a imagem do GLPI:**
    ```bash
    docker compose pull glpi
    docker compose up -d
    ```
    Isso puxará a versão mais recente da imagem `elestio4test/glpi:latest` e recriará o contêiner `glpi-app`. Certifique-se de fazer backup de seus dados antes de grandes atualizações.
