# Docker

http://docker.com/

## Iniciando a partir de uma imagem

O parametro -d roda em background o container

$ docker run --name meu_container ubuntu -d

## Iniciando containers descartáveis

$ docker run --rm -it ubuntu bash

## Iniciando ou parando

$ docker start container
$ docker stop container

## Listando imagens

$ docker images

## Removendo imagens

$ docker rmi imagem

## Removendo todas as imagens

$ docker image prune

## Salvando uma imagem

$ docker save -o imagem.tar imagem

## Carregando uma imagem Salvar

$ docker load --input imagem.tar

## Compartilhando portas

Compartilha a porta do container no host. -p host: container

$ docker run ubuntu/apache -p 8080:80

## Listando Containers

$ docker ps -a

## Listando volumes

$ docker volume ls

## Criando volumes

Mapeia o diretorio atual para o /tmp do container.

$ docker run -it -v $(pwd):/tmp ubuntu 

## Removendo

$ docker rm container

## Removendo todos os containers

O Prune remove os inativos

$ docker container prune

ou 

$ docker rm $(docker ps -qa)

## Acessando o container

$ docker exec -it container bash

ou 

$ docker start -a -i container

## Inspecionando o container

Retorna informações sobre o container, inclusive seu IP (GateWay)

$ docker inspect container

Saber o IP permite que você por exemplo faça um ping de um container para o outro, ou então acesse um determinado serviço que esteja rodando 
no outro container, como por exemplo um mysql.

Vamos supor que subimos um container de mysql

$ docker run --name banco -e MYSQL_ROOT_PASSWORD=test123 -p 3306:3306 mysql -d

E que após inspecioner vimos que o IP do container mysql é 123.123.123.123. Podemos poingar ou acessar esse container através de um outro container.

$ docker run -rm -it ubuntu bash

root@ubuntu# ping 123.123.123.123
root@ubuntu# apt-get install -y mysql-client
root@ubuntu# mysql -h 123.123.123.123 -u root -p

## Comitando uma nova imagem

Podemos criar um container baseado em uma imagem inicial, fazer algumas modificações e comitar essas mudanças em uma nova imagem

$ docker run --name container ubuntu

$ docker exec -it container bash

root@container# apt-get update && apt-get install -y apache2
root@container# exit

$ docker commit -m "Instalação do apache" container ubuntu/apache

## Logs

Para ver os logs, escritos no output do container

$ docker logs -f container

# DockerFile

Arquivo que indica como uma imagem será criada

## Build

Transforma o DockerFile em uma imagem

Crie um DockerFile

    # Imagem a partir da imagem ubuntu
    FROM ubuntu

    # Instalando o apache
    RUN apt-get update && apt-get install -y apache2 
    
    # Copiando os arquivos da aplicação, o comando ADD copia o conteudo da pasta /app para o servidor, já o comando COPY copya o diretorio /app para o sevidor
    ADD app/ /var/www/html

    # Expondo a porta 80
    EXPOSE 80

    # iniciando o apache 
    CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

Processe o dockerfile para criar o container

    $ docker build -t ubuntu/apache .

# Docker Compose

O Compose nada mais é que um complemento para ajudar o docker a criar containers

## Subindo containers com o compose

Criar um docker compose com as intruções para suvir o ambiente. No caso desse exemplo,
subiremos um  container de banco e outro de aplicação. Salvar como docker-compose.yml

    nome_container_banco:
        image: mysql
        volumes:
            -  ~/database_mysql:/var/lib/mysql
        environment:
            - MYSQL_ROOT_PASSWORD=test123

    nome_container_blog:
        image: wordpress
        environment:
            - WORDPRESS_DB_PASSWORD=test123
        links:
            - nome_container_banco:apelido_nome_container_banco
        ports:
            - 80:80

Subindo o compose criado:

    $ docker-compose up -d

## Listando os processos criados como compose

$ docker-compose ps

## Removendo os containers com compose

$ docker-compose rm