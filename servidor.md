# Montando um servidor
================================

## Configurando troca de chaves por SSH
================================================================================================

    Logar no servidor e no home do diretório root criar a pasta .ssh caso ela não exista e criar o arquivo authorized_keys dentro dela.

    Dar permissão 700 para o diretório e 600 para o arquivo

        `# chmod 700 ~/.ssh`
        `# chmod 600 ~/.ssh/authorized_keys`

    Editar o arquivo authorized_keys e colocar a chave publica do seu computador, no meu caso, copiei assim

        `$ cat .ssh/id_rsa.pub | pbcopy`

    ou

        `$ cat ~/.ssh/id_rsa.pub | ssh root@162.243.78.30 "mkdir -p ~/.ssh && cat >>  ~/.ssh/authorized_keys"`

    Teste, tem que logar sem senha

        `$ ssh root@162.243.78.30 -vvvv`


## Permitindo que apenas usuarios de um grupo consigam logar na maquina
================================================================================================

    Crie o grupo sshlogin e coloque o usuario root nele.

        `$ addgroup sshlogin`

        `$ adduser root sshlogin`

    Edite o arquivo do ssh e configure apenas esse grupo como permitido

        `vi /etc/ssh/sshd_config`

        `AllowGroups sshlogin`


## Atualizando a distribuição
================================================================================================

    `$ apt-get update`

    `$ apt-get upgrade`

    `$ apt-get autoremove`

    ## Configurando a sincronia de hora

    `$ apt-get install ntp`

    `$ dpkg-reconfigure tzdata`


## Instalando GCC
================================================================================================

    `apt-get install build-essential libgmp3-dev`

## Instalando Mysql
================================================================================================

    `apt-get install mysql-server mysql-client libmysqlclient-dev`

    Configurar seguranca

        `mysql_secure_installation`

    Testando

        `mysql -u root -p`

    Criando um BD

        `mysql> create database tripolist character set utf8;`

    Criando um usuario

        `mysql> grant all on tripolist.* to 'tripolist' identified by 'senha';`

        `mysql> flush privileges;`

## Ruby com rvm
================================================================================================

    - https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-on-ubuntu-14-04-using-rvm

    Rodar o comando abaixo para instalar o RVM

        `gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
        \curl -sSL https://get.rvm.io | bash -s stable`


    Criar RVM wrapper:
        Se você esta usando RVM tem que criar um wrapper para que o usuario www-data consiga enxergar o bundler e o RVM

            rvm alias create tripolist ruby-2.3.0@tripolist

        E depois usar os comandos disponiveis no path

            `ls /usr/local/rvm/wrappers/tripolist`

        como

            `/usr/local/rvm/wrappers/tripolist/bundle exec ...`

        Atualizar o bash

            `source ~/.rvm/scripts/rvm`

        Instalar uma versão do ruby

            `rvm install 2.3.0`

        Criar um gemset para o projeto

            `rvm gemset create tripolist`

        Instale a gem do bundler globalmente

            `rvm @global do gem install bundler`

        ou no seu gemset

            `rvm use "ruby-2.3.0@tripolist" --create`
            `gem install bundler`


## Ruby sem rvm ou rbenv
================================================================================================

    O Nando Vieira mantém um repositório apt contendo o ruby e o nginx

    - http://apt.hellobits.com/

        `$ apt-get remove ruby`
        `$ apt-get -y install ruby-2.3`
        `$ gem install bundler`


## Instalando o NginX
================================================================================================

    `$ apt-get install nginx`

    Ver se esta rodando

        `ps auxwww | grep nginx`


## Instalando NodeJs
================================================================================================

    `apt-get install nodejs`


## Configuracoes para deploy
================================================================================================

    Criar o usuario para deploy

        `adduser --ingroup www-data deploy --disabled-password`
        `su deploy`
        `cd`
        `mkdir .ssh`
        `touch .ssh/authorized_keys`
        `chmod 700 .ssh`
        `chmod 600 .ssh/authorized_keys`

    Configurar a chave ssh nesse arquivo e salvar


## Caminho do projeto
================================================================================================

    Colocar o projeto em /var/www/projeto vou seguir esse padrão


# Instalando o GIT
================================================================================================

    `apt-get install git`


# Configurando o MINA para deploy
================================================================================================

    Dentro do diretorio do projeto local inicialize o mina para que ele possa criar o arquivo deploy.rb, você deve configurar esse arquivo como no projeto tripolist.

        `mina init`

    Depois de configurado rodar o setup para ver se tudo esta legal

        `mina setup`

    E em seguida logar no servidor e configurar os arquivos de database e env que foram criados.

    Lembre que se vc usou o arquivo .env tera que ter a gem 'dotenv-rails' no seu GemFile

        `mina deploy --verbose`

    Se der problema de chave ssh, logue no servidor com o usuario deploy e conecte no seu repositorio git para sincronizar as chaves.

        `su deploy`
        `$ ssh git@bitbucket.org`


## Rodar migracoes pelo Mina
================================================================================================

    `mina rails:db_migrate`


## Rodar o servidor para testar se está funcionando
================================================================================================

    Va para o diretório do projeto

        `cd /var/www/tripolist/current`

    Rode o rails com o binding para o IP da maquina ou para 0.0.0.0

        `bundle exec rails  s  -e production --binding=162.243.91.163`
        `bundle exec rails  s  -e production --binding=0.0.0.0`


## Configurando o Unicorn
================================================================================================

    Inclua a gem do unicorn no gemfile do projeto e cpnfigure o arquivo unicorn.rb dentro do projeto. Tem um exemplo desse arquivo no github server-conf

    Em seguida, faça deploy e teste

        `cd /var/www/tripolist/current`
        `bundle exec unicorn --env production`

    ou usando um arquivo de configuracao

        `bundle exec unicorn --env production -c .config/unicorn.rb`

    ou usando o usuário www-data

        `sudo -u www-data sh -c '/usr/local/rvm/wrappers/tripolist/bundle exec unicorn --env production -c .config/unicorn.rb'`


    Caso você esteja usando unicorn.sock crie o diretiorio /var/run/tripolist e de permissão

        `mkdir /var/run/tripolist`
        `chown www-data. /var/run/tripolist/`


# Configurando NginX
================================================================================================

    Defina as configurações globais, temos exemplos desse arquivo no github server-conf

        `vi /etc/nginx/nginx.conf`

    As configurações por projeto criamos em sites-enabled

        `vi /etc/nginx/sites-enabled/tripolist.conf`

    Testar sintaxe das configuracoes

        `nginx -t`

    Reiniciar o NginX

        `/etc/init.d/nginx restart`


# Configurando os scripts de inicialização UPSTART
================================================================================================

    Criar um arquivo init geral do projeto

        `vim /etc/init/tripolist.conf`

        Conteudo

            description "Taskv application"
            start on runlevel [2345]
            stop on runlevel [016]

            pre-start script
            mkdir -p /var/log/taskv
            chown -R www-data /var/log/taskv

            mkdir -p /var/run/taskv
            chown -R www-data /var/run/taskv
            end script


    Criar um arquivo com o start servidor de aplicação

        `vim /etc/init/tripolist-unicorn.conf`

        Conteudo

            description "tripolist application server"
            start on starting tripolist
            stop on stopping tripolist
            chdir /var/www/tripolist/current
            respawn
            exec sudo -u www-data sh -c '/usr/local/rvm/wrappers/tripolist/bundle exec unicorn --env production -c ./config/unicorn.rb >> /var/log/tripolist/unicorn.log 2>&1'


        OBS.: Se você esta usando RVM tem que criar um wrapper para que o usuario www-data consiga enxergar o bundler e o RVM

            `rvm alias create tripolist ruby-2.3.0@tripolist`

        E depois usar os comandos disponiveis no path

            `ls /usr/local/rvm/wrappers/tripolist`

        como

        `/usr/local/rvm/wrappers/tripolist/bundle exec ...`


    Para usar o upstart

        `start tripolist`
        `status tripolist`
        `stop tripolist`
        `restart tripolist`

    Os logs do upstart ficam em

        `/var/log/upstart

## Monit
================================================================================================

    O Monit serve para monitorar processos e tomar ações em casos de erro. Nesse caso usaremos para monitorar um arquivo
    que atualizaremos sempre que fizermos o deploy com o mina. Quando a data mudar o monit irá reiniciar o unicorn.

    Ver receita do monit

## Elastic Search
================================================================================================

    Full text search, ver receita do elastic search


## Configurando DNS
================================================================================================

- https://www.digitalocean.com/community/tutorials/how-to-set-up-a-host-name-with-digitalocean


## Configurando firewall
================================================================================================

    Usar o UFW para gerenciar o firewall, ver receita o ufw


# Backup de MySql com automysqlbackup
================================================================================================

    Faz backup automatico do mysql, ver receita do automysqlbackup


# LogWatch
================================================================================================

    Manda um relatorio diario do servidor, ver receita do logwatch