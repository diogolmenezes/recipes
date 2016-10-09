# Docker

http://docker.com/

## Iniciando ou parando

$ sudo docker start container
$ sudo docker stop container


## Listando imagens

$ sudo docker images

## Listando Containers

$ sudo docker ps -a

## Removendo

$ sudo docker rm container

## Acessando o container

$ sudo docker exec -it container bash


## Logs

Para ver os logs, escritos no output do container

$ sudo docker logs -f container

