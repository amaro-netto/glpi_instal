# Repositório de Guias de Instalação do GLPI

[![GLPI](https://img.shields.io/badge/Software-GLPI-0158a1?style=flat-square&logo=glpi&logoColor=white)](https://glpi-project.org/)
[![Debian](https://img.shields.io/badge/OS-Debian%2012-A81D33?style=flat-square&logo=debian&logoColor=white)](https://www.debian.org/)
[![Apache](https://img.shields.io/badge/Web%20Server-Apache-DC3545?style=flat-square&logo=apache&logoColor=white)](https://httpd.apache.org/)
[![MariaDB](https://img.shields.io/badge/Database-MariaDB-003545?style=flat-square&logo=mariadb&logoColor=white)](https://mariadb.org/)
[![PHP](https://img.shields.io/badge/Language-PHP-777BB4?style=flat-square&logo=php&logoColor=white)](https://www.php.net/)
[![Docker](https://img.shields.io/badge/Containerization-Docker-2496ED?style=flat-square&logo=docker&logoColor=white)](https://www.docker.com/)
[![Docker Compose](https://img.shields.io/badge/Orchestration-Docker%20Compose-2496ED?style=flat-square&logo=docker&logoColor=white)](https://docs.docker.com/compose/)

Este repositório serve como um hub central para guias detalhados de instalação e configuração do GLPI em diferentes ambientes. Nosso objetivo é fornecer instruções claras e testadas para que usuários e administradores de TI possam implantar o GLPI de forma eficiente e segura.

---

## O que é GLPI?

GLPI (Gestão Livre de Parque Informático) é uma solução de software de código aberto para Gerenciamento de Ativos de TI e Service Desk. Ele permite que organizações:

* **Gerenciem Inventário de TI:** Rastreie hardware, software, licenças e conexões de rede.
* **Gerenciem Tickets (Service Desk):** Ofereça um portal para usuários reportarem incidentes e solicitarem serviços, com recursos de automação e acompanhamento.
* **Gerenciem Projetos:** Ferramentas para acompanhamento de projetos e tarefas relacionadas a TI.
* **Gerenciem Financeiro:** Controle custos e contratos associados aos ativos de TI.
* **Gerenciem Base de Conhecimento:** Crie e mantenha artigos de conhecimento para autoatendimento e resolução rápida de problemas.

Com sua natureza de código aberto, o GLPI é altamente personalizável e extensível através de plugins, tornando-o uma ferramenta poderosa para ITIL (Information Technology Infrastructure Library) e ITSM (IT Service Management).

---

## Tecnologias e Abordagens Cobertas

Neste repositório, você encontrará tutoriais que utilizam as seguintes tecnologias e métodos de implantação:

* **Sistema Operacional:** Debian 12 (Bookworm)
* **Ambiente LAMP:**
    * **Apache HTTP Server:** Servidor web robusto.
    * **MariaDB:** Banco de dados relacional de alto desempenho (alternativa ao MySQL).
    * **PHP:** Linguagem de script do lado do servidor essencial para o GLPI.
* **Conteinerização:**
    * **Docker:** Para empacotar e isolar o GLPI e seus serviços em contêineres leves.
    * **Docker Compose:** Para definir e executar aplicações multi-contêiner Docker.

---

## Sumário

1.  [Pré-requisitos](#pré-requisitos)
    * [Requisitos Mínimos de Hardware](#requisitos-mínimos-de-hardware)
2.  [Topologia de Implantação](#topologia-de-implantação)
3.  [Guias de Instalação](#guias-de-instalação)
4.  [Plugins Recomendados para GLPI](#plugins-recomendados-para-glpi)
5.  [Versões Utilizadas](#versões-utilizadas)
6.  [Autor e Ambiente de Teste](#autor-e-ambiente-de-teste)
7.  [Contato e Suporte](#contato-e-suporte)
8.  [Links Úteis](#links-úteis)

---

## Pré-requisitos

Antes de começar, certifique-se de ter:

* Uma máquina virtual ou servidor físico com **Debian 12** instalado.
* Acesso SSH ao servidor com um usuário que tenha privilégios `sudo`.
* Endereço IP da máquina (ex: `192.168.89.132`).

### Requisitos Mínimos de Hardware

Para uma instalação básica do GLPI, os seguintes requisitos são geralmente recomendados:

* **Processador (CPU):** 2 vCPUs (equivalente a um Intel Core i3 ou AMD Ryzen 3 moderno).
* **Memória RAM:** 4 GB de RAM.
* **Espaço em Disco:** 40 GB de espaço livre em disco (preferencialmente SSD para melhor performance do banco de dados).
* **Conexão de Rede:** Conexão de rede estável e configurada.

Para ambientes de produção com muitos usuários ou grande volume de tickets/inventário, é recomendado aumentar esses recursos.

---

## Topologia de Implantação

Este repositório cobre duas principais topologias para a implantação do GLPI:

### 1. Instalação em Máquina Virtual (VM) / Servidor Físico com Stack LAMP Tradicional

Nesta abordagem, o GLPI é instalado diretamente sobre um servidor Debian 12, com o Apache como servidor web, MariaDB como banco de dados e PHP para processamento do lado do servidor. Esta topologia oferece controle total sobre o ambiente e é ideal para quem prefere uma configuração mais tradicional e otimizada para o hardware subjacente.

* **Componentes:** Debian 12, Apache, MariaDB, PHP, GLPI.
* **Vantagens:** Alto desempenho potencial, controle granular, depuração mais direta dos serviços.
* **Desvantagens:** Menos portabilidade, maior complexidade na gestão de dependências e atualizações de componentes isolados.

### 2. Instalação com Docker Compose

Esta abordagem utiliza contêineres Docker para isolar e orquestrar os serviços do GLPI (aplicação, banco de dados, servidor web se aplicável). O Docker Compose é usado para definir e gerenciar a aplicação multi-contêiner, facilitando a implantação, escalabilidade e portabilidade entre diferentes ambientes.

* **Componentes:** Debian 12 (host), Docker Engine, Docker Compose, contêineres GLPI, contêiner MariaDB, contêiner Apache/Nginx (opcional, ou php-fpm).
* **Vantagens:** Alta portabilidade, isolamento de dependências, fácil escalabilidade, implantação rápida.
* **Desvantagens:** Curva de aprendizado inicial para Docker, pode ter uma pequena sobrecarga de desempenho em comparação com o bare-metal para pequenas instalações.

---

## Guias de Instalação

Navegue pelos nossos guias de instalação detalhados para implantar o GLPI no ambiente de sua preferência:

* **[Instalação do GLPI em Máquina Virtual (Debian 12)](https://github.com/amaro-netto/glpi_install/blob/main/virtual_machine.md)**
    * Um guia passo a passo para instalar o GLPI diretamente em uma VM ou servidor Debian 12 usando o stack LAMP tradicional. Ideal para quem busca controle total sobre o ambiente.
* **[Instalação do GLPI com Docker Compose](https://github.com/amaro-netto/glpi_install/blob/main/docker_composer.md)** (A ser adicionado)
    * Em breve: um guia para implantar o GLPI usando contêineres Docker orquestrados com Docker Compose, facilitando a portabilidade, escalabilidade e gerenciamento de dependências.

---

## Plugins Recomendados para GLPI

A flexibilidade do GLPI é ampliada por sua vasta gama de plugins. Abaixo, uma lista de plugins que usamos e recomendamos para otimizar a experiência com o GLPI:

* **[Ticket Answers](https://github.com/jefersonp22/ticketanswers-v1.1)**: Permite a criação de respostas pré-definidas para tickets, agilizando o atendimento.
* **FusionInventory**: Essencial para coleta automática de inventário (hardware e software) de máquinas na rede.
* **Dashboard**: Aprimora a visualização de dados e métricas com dashboards personalizáveis.
* **Field Creator**: Permite adicionar campos personalizados a diferentes tipos de itens no GLPI.
* **PDF**: Para geração de relatórios e documentos em formato PDF.
* **Reports**: Ferramenta avançada para criação de relatórios complexos e personalizados.
* **Mailgate IMAP/POP**: Para configuração do recebimento de tickets via e-mail.

---

## Versões Utilizadas

Este tutorial foi testado e validado com as seguintes versões das tecnologias:

* **Sistema Operacional:** Debian 12 (Bookworm)
* **GLPI:**
    * Versão Estável: GLPI 10.0.18
    * Versão de Teste: GLPI 11.0.0-beta6
* **Apache2:** 2.4.57 (Debian)
* **MariaDB:** 10.11.x (Debian)
* **PHP:** 8.2.x (Debian)
* **Docker Engine:** Versão recente compatível com Debian 12 (será especificado nos guias Docker)
* **Docker Compose:** Versão recente compatível com Docker Engine (será especificado nos guias Docker)

---

## Autor e Ambiente de Teste

Este tutorial foi desenvolvido e testado por **[Seu Nome/GitHub Username Aqui]** em um ambiente de máquina virtual com as seguintes especificações:

* **Sistema Operacional:** Debian 12 (Bookworm)
* **Ambiente de Virtualização:** VMware Workstation e ESXi 8 
* **Recursos da VM:** 2 vCPUs, 4GB RAM, 40GB HDD (adapte conforme o que você usou)
* **Acesso:** SSH e acesso web via IP local (`192.168.89.132`).

---

## Contato e Suporte

Se você tiver dúvidas, sugestões ou encontrar problemas ao seguir este tutorial, sinta-se à vontade para:

* Abrir uma [Issue neste repositório](https://github.com/amaro-netto/glpi_install/issues).
* Entrar em contato via [ti.amaronetto@gmail.com] ou [https://github.com/amaro-netto].

---

## Links Úteis

* **Documentação Oficial do GLPI:** [https://glpi-project.org/](https://glpi-project.org/)
* **Fórum da Comunidade GLPI:** [https://forum.glpi-project.org/](https://forum.glpi-project.org/)
* **Documentação do Debian:** [https://www.debian.org/doc/](https://www.debian.org/doc/)
* **Documentação do Apache:** [https://httpd.apache.org/docs/](https://httpd.apache.org/docs/)
* **Documentação do MariaDB:** [https://mariadb.com/kb/en/documentation/](https://mariadb.com/kb/en/documentation/)
* **Documentação do PHP:** [https://www.php.net/docs.php](https://www.php.net/docs.php)
* **Documentação do Docker:** [https://docs.docker.com/](https://docs.docker.com/)

---
