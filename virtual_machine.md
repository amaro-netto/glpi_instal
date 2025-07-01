# Documentação de Instalação do GLPI no Debian 12

Esta documentação fornece um guia completo e detalhado para a instalação do GLPI (versões 10.x e de teste 11.x) em um ambiente Debian 12, cobrindo desde a configuração dos pré-requisitos (servidor web, banco de dados, PHP) até a finalização e segurança pós-instalação. **Este guia assume e detalha primeiramente uma instalação para uso local**.

---

## Sumário

1.  [Pré-requisitos](#pré-requisitos)
2.  [Instalação do LAMP (Linux, Apache, MariaDB, PHP)](#instalação-do-lamp-linux-apache-mariadb-php)
3.  [Configuração do Banco de Dados MariaDB para o GLPI](#configuração-do-banco-de-dados-mariadb-para-o-glpi)
4.  [Instalação do GLPI (Versão Estável 10.0.18 ou Versão de Teste 11.0.0-beta6)](#instalação-do-glpi-versão-estável-10018-ou-versão-de-teste-1100-beta6)
    * [Instalação de uma Nova Instância GLPI (Versão Estável 10.0.18)](#instalação-de-uma-nova-instância-glpi-versão-estável-10018)
    * [Instalação da Versão de Teste GLPI 11.0.0-beta6](#instalação-da-versão-de-teste-glpi-1100-beta6)
5.  [Configuração do Apache para o GLPI](#configuração-do-apache-para-o-glpi)
    * [Acesso Via Rede Local/Internet](#acesso-via-rede-localinternet)
6.  [Instalação Via Navegador](#instalação-via-navegador)
7.  [Resolução de Problemas Comuns](#resolução-de-problemas-comuns)
    * [Erro: "The web server seems to be misconfigured."](#erro-the-web-server-seems-to-be-misconfigured)
    * [Erro: Extensão PHP `bcmath` ausente](#erro-extensão-php-bcmath-ausente)
    * [Problemas de Permissão ao Copiar Arquivos](#problemas-de-permissão-ao-copiar-arquivos)
8.  [Finalização e Segurança Pós-Instalação](#finalização-e-segurança-pós-instalação)

---

## Pré-requisitos

Antes de começar, certifique-se de ter:

* Uma máquina virtual ou servidor físico com **Debian 12** instalado.
* Acesso SSH ao servidor com um usuário que tenha privilégios `sudo`.
* Endereço IP da máquina (ex: `192.168.89.132`).

---

## Instalação do LAMP (Linux, Apache, MariaDB, PHP)

Esta seção detalha a instalação dos componentes essenciais do ambiente de servidor web (Apache, MariaDB, PHP).

```bash
# 1. Atualizar a lista de pacotes e o sistema
sudo apt update
sudo apt upgrade -y

# 2. Instalar o Apache (Servidor Web HTTP)
sudo apt install apache2 -y

# 3. Instalar o MariaDB (Servidor de Banco de Dados)
sudo apt install mariadb-server mariadb-client -y

# 4. Configurar a segurança inicial do MariaDB (definir senha root, remover usuários anônimos, etc.)
sudo mysql_secure_installation

# 5. Instalar o PHP e as extensões necessárias para o GLPI
# As extensões são cruciais para o funcionamento correto do GLPI.
sudo apt install php libapache2-mod-php php-mysql php-gd php-curl php-intl php-mbstring php-xml php-zip php-json php-ldap php-apcu php-cli php-bcmath -y
```

---

## Configuração do Banco de Dados MariaDB para o GLPI

Crie um banco de dados e um usuário dedicado para o GLPI no MariaDB, seguindo as melhores práticas de segurança.

```bash
# Acesse o console do MariaDB como usuário root
sudo mysql -u root -p
```
Quando solicitado, digite a senha do `root` do MariaDB que você definiu durante o `mysql_secure_installation`.

Dentro do console do MariaDB, execute os seguintes comandos SQL:

```sql
CREATE DATABASE glpidb;
CREATE USER 'glpiuser'@'localhost' IDENTIFIED BY 'sua_senha_segura';
GRANT ALL PRIVILEGES ON glpidb.* TO 'glpiuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
> [!WARNING]
> Substitua `sua_senha_segura` por uma senha forte e única para o seu usuário `glpiuser`.

---

## Instalação do GLPI (Versão Estável 10.0.18 ou Versão de Teste 11.0.0-beta6)

Esta seção aborda a instalação de diferentes versões do GLPI. Escolha a que se aplica ao seu caso.

### Instalação de uma Nova Instância GLPI (Versão Estável 10.0.18)

Se esta é uma nova instalação ou você deseja substituir uma instalação existente por uma versão estável.

```bash
# 1. (Opcional, mas recomendado para nova instalação) Remover qualquer instalação GLPI anterior
# Use com cautela, pois isso apagará todos os dados da instalação anterior.
sudo rm -rf /var/www/html/glpi

# 2. Baixar o GLPI 10.0.18
cd /tmp
wget [https://github.com/glpi-project/glpi/releases/download/10.0.18/glpi-10.0.18.tgz](https://github.com/glpi-project/glpi/releases/download/10.0.18/glpi-10.0.18.tgz)

# 3. Extrair o GLPI para o diretório web do Apache
sudo tar -xzf glpi-10.0.18.tgz -C /var/www/html/

# 4. Renomear o diretório extraído para 'glpi' (simplifica o acesso)
sudo mv /var/www/html/glpi-10.0.18 /var/www/html/glpi

# 5. Ajustar Permissões para o GLPI
# O servidor web (www-data) precisa de permissões adequadas para os arquivos e diretórios do GLPI.
sudo chown -R www-data:www-data /var/www/html/glpi
sudo chmod -R 775 /var/www/html/glpi/files
sudo chmod -R 775 /var/www/html/glpi/config
sudo chmod -R 775 /var/www/html/glpi/marketplace
sudo chmod -R 775 /var/www/html/glpi/plugins
sudo chmod -R 775 /var/www/html/glpi/log
```

### Instalação da Versão de Teste GLPI 11.0.0-beta6

Se você está interessado em testar uma versão beta. **Não recomendado para ambientes de produção.**

```bash
# 1. (Opcional, mas recomendado para nova instalação) Remover qualquer instalação GLPI anterior
sudo rm -rf /var/www/html/glpi

# 2. Baixar a versão beta do GLPI 11.0.0-beta6
cd /tmp
wget [https://github.com/glpi-project/glpi/releases/download/11.0.0-beta6/glpi-11.0.0-beta6.tgz](https://github.com/glpi-project/glpi/releases/download/11.0.0-beta6/glpi-11.0.0-beta6.tgz)

# 3. Extrair o GLPI para o diretório web do Apache
sudo tar -xzf glpi-11.0.0-beta6.tgz -C /var/www/html/

# 4. Renomear o diretório extraído para 'glpi'
sudo mv /var/www/html/glpi-11.0.0-beta6 /var/www/html/glpi

# 5. Ajustar Permissões para o GLPI (mesmas permissões da versão estável)
sudo chown -R www-data:www-data /var/www/html/glpi
sudo chmod -R 775 /var/www/html/glpi/files
sudo chmod -R 775 /var/www/html/glpi/config
sudo chmod -R 775 /var/www/html/glpi/marketplace
sudo chmod -R 775 /var/www/html/glpi/plugins
sudo chmod -R 775 /var/www/html/glpi/log
```

---

## Configuração do Apache para o GLPI

Configure o Apache para servir o GLPI corretamente, incluindo o redirecionamento para o diretório `public`.

```bash
# 1. Criar ou editar o arquivo de configuração do Virtual Host do GLPI
sudo nano /etc/apache2/sites-available/glpi.conf
```
Cole o seguinte conteúdo no arquivo:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/glpi/public
    ServerName 192.168.89.132 # Altere para o IP ou nome de domínio da sua máquina

    <Directory /var/www/html/glpi/public>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>

    # Habilita o módulo de reescrita de URLs e redireciona todas as requisições para o index.php
    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.*)$ /index.php/$1 [L]

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Salve e feche o arquivo (pressione `Ctrl+O`, depois `Enter`, e `Ctrl+X`).

```bash
# 2. Ativar o site do GLPI e o módulo rewrite do Apache
sudo a2ensite glpi.conf
sudo a2enmod rewrite

# 3. Reiniciar o serviço Apache para aplicar as mudanças
sudo systemctl restart apache2
```

### Acesso Via Rede Local/Internet

As configurações acima permitem o acesso ao GLPI via o IP especificado (`192.168.89.132`). Para torná-lo acessível na rede local ou internet, considere os seguintes pontos:

* **`ServerName`:** Se você possui um nome de domínio (ex: `meuglpi.com.br`), altere `ServerName 192.168.89.132` para `ServerName meuglpi.com.br` no arquivo `/etc/apache2/sites-available/glpi.conf`.
* **DNS:** Certifique-se de que seu nome de domínio ou IP interno esteja corretamente resolvido via DNS (interno ou externo, conforme o caso).
* **Firewall (UFW):** Se você tiver um firewall ativo (como o UFW no Debian), precisará permitir o tráfego HTTP (porta 80) e HTTPS (porta 443, se você configurar SSL).

    ```bash
    sudo ufw allow 'Apache'    # Para HTTP
    sudo ufw allow 'Apache Full' # Para HTTP e HTTPS (se você configurar SSL mais tarde)
    sudo ufw enable            # Habilitar o firewall se não estiver habilitado
    ```
* **Roteador/NAT (para acesso externo):** Se o GLPI estiver em uma rede interna e você quiser acessá-lo da internet, precisará configurar o redirecionamento de portas (NAT - Network Address Translation) no seu roteador para a porta 80 (e 443 para HTTPS) para o IP interno do seu servidor Debian.
* **SSL/HTTPS:** Para acesso seguro (especialmente via internet), é **altamente recomendado** configurar SSL/TLS (HTTPS). Isso envolve obter um certificado SSL (ex: Let's Encrypt) e configurar o Apache para usar HTTPS.

---

## Instalação Via Navegador

Com tudo configurado no servidor, você pode finalizar a instalação do GLPI pelo seu navegador.

Abra seu navegador e vá para o IP da sua máquina:

`http://192.168.89.132`

Você será guiado pelo processo de instalação do GLPI:

* **Escolha do idioma:** Selecione o seu idioma preferido.
* **Verificação de compatibilidade:** O instalador verificará se todos os requisitos foram atendidos.
* **Conexão ao banco de dados:** Informe os dados do banco de dados que você criou no Passo 2 (`localhost`, `glpiuser`, `sua_senha_muito_segura`).
* **Seleção do banco de dados:** Escolha `glpidb` na lista.
* O GLPI irá criar as tabelas e importar os dados iniciais.
* Na tela final, você verá as **credenciais padrão** (ex: `glpi`/`glpi`, `tech`/`tech`). Anote-as.

---

## Resolução de Problemas Comuns

Durante o processo de instalação, você pode encontrar alguns erros comuns. Aqui estão as soluções.

### Erro: "The web server seems to be misconfigured."

Este erro ocorre quando o Apache não está configurado para apontar para o diretório `public` do GLPI ou não está redirecionando corretamente as requisições para `index.php`.

**Solução:**

Verifique e corrija seu arquivo de configuração do Apache (`/etc/apache2/sites-available/glpi.conf`) para garantir que o `DocumentRoot` esteja apontando para `/var/www/html/glpi/public` e que o `RewriteEngine` esteja habilitado com as regras corretas.

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/glpi/public
    ServerName 192.168.89.132 # Seu IP ou nome de domínio

    <Directory /var/www/html/glpi/public>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>

    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^(.*)$ /index.php/$1 [L]

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Após a correção, reinicie o Apache:
```bash
sudo systemctl restart apache2
```

### Erro: Extensão PHP `bcmath` ausente

O GLPI exige a extensão `bcmath` do PHP para cálculos numéricos precisos.

**Solução:**

Instale a extensão e reinicie o Apache:
```bash
sudo apt install php-bcmath -y
sudo systemctl restart apache2
```

### Problemas de Permissão ao Copiar Arquivos

Se você não conseguir copiar pastas ou arquivos para `/var/www/html/glpi/plugins/` (ou outros diretórios do GLPI) devido a restrições de permissão.

**Solução:**

Sempre use `sudo` ao copiar ou mover arquivos para diretórios que pertencem a usuários do sistema (como `root` ou `www-data`). Após copiar, **sempre ajuste as permissões e o proprietário** para o usuário `www-data` do Apache.

Exemplo de cópia e ajuste de permissões:

```bash
# 1. Copiar a pasta (ex: 'meuplugin' do seu diretório home)
sudo cp -r ~/meuplugin /var/www/html/glpi/plugins/

# 2. Ajustar o proprietário e as permissões recursivamente
sudo chown -R www-data:www-data /var/www/html/glpi/plugins/meuplugin
sudo chmod -R 775 /var/www/html/glpi/plugins/meuplugin
```
Se preferir ter seu usuário no grupo `www-data` para manipulação sem `sudo` (não recomendado para produção):
```bash
sudo usermod -aG www-data $USER # Saia e logue novamente após este comando
```

---

## Finalização e Segurança Pós-Instalação

Após a instalação bem-sucedida do GLPI via navegador:

1.  **Remova o arquivo de instalação:** Por segurança, é fundamental remover o script de instalação para evitar que seja acessado indevidamente.

    ```bash
    sudo rm /var/www/html/glpi/install/install.php
    ```

2.  **Altere as Senhas Padrão:** Faça login no GLPI com os usuários padrão (`glpi`, `tech`, `normal`, `post-only`) e altere suas senhas imediatamente para senhas fortes e únicas.

3.  **Explore o GLPI:** Comece a configurar usuários, entidades, tickets e inventário conforme suas necessidades.

