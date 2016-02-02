# Montando uma máquina linux para desenvolvimento ruby/rails	

# Links Úteis

- https://nandovieira.com.br/configurando-ruby-rails-mysql-postgresql-git-no-ubuntu

## Atualizando o sistema

`$ apt-get update`

`$ apt-get upgrade`

## Instalando as dependências

`$ sudo apt-get install -y build-essential autoconf bison libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm-dev`

## Instalando o Git

`$ sudo apt-get install -y git`

`$ git config --global user.name 'Diogo Menezes'`

`$ git config --global user.email diogolmenezes@gmail.com`

`$ git config -l`

## Ruby com rbenv

- Ver o artigo https://nandovieira.com.br/configurando-ruby-rails-mysql-postgresql-git-no-ubuntu

## Ruby sem rbenv

O Nando Vieira mantém um repositório apt contendo o ruby e o nginx

- http://apt.hellobits.com/

`$ apt-get remove ruby`

`$ apt-get -y install ruby-2.3`

## Instalando o Mysql

`sudo apt-get install -y mysql-client mysql-server libmysqlclient-dev`

Para aprender mais sobre MySQL veja a recipe de mysql

## Rails

`$ sudo gem install rails --no-ri --no-rdoc`

## NodeJs
	
`$ sudo apt-get install -y nodejs`


## Criando diretório de projetos

`$ cd /vagrant`

`$ mkdir projetos`


## Criando uma app rails de exemplo

Caso queira usar SQLITE

`$ apt-get install -y libsqlite3-dev`

`$ rails new app`

Mysql

`$ rails new myapp -d mysql`


Se estiver usando Vagrant, lembre-se que você deve liberar os serviços para serem acessíveis de outros endereços além de 127.0.0.1, pois o binding é normalmente feito para este endereço IP. No caso do Rails, você deve iniciá-lo como no comando abaixo:

`rails server -b 0.0.0.0`


