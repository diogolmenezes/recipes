# Linux

Dicas e comandos  do linux

# PenDrive

sudo yum install ntfs-3g

## Yum

Procura por um pacote que possua o comando

yum provides /usr/bin/ab

Lista de repositorios

yum repolist

Repositorios ficam em 

/etc/yum.repos.d

## Descubrir que processo está usando a porta

Ve o PID do processo que está escutando a porta 3000

`fuser 3000/tcp`

Mata o processo que está escutando a porta 3000

`fuser 3000/tcp -k`

## Docker sem precisar de root

```bash
sudo groupadd docker
sudo gpasswd -a $USER docker
newgrp docker
```

## Listar aplicacoes que mais usam memoria

`ps aux --sort -rss | head`

## Memoria livre

`free -m` ou `free -h`

## Partiçoes

`df -kh`

## Tamanho de um diretorio

`cd /`
`du -ah`

## Sudo sem senha

`$ sudo visudo`

colocar:

`usuario   ALL=NOPASSWD: ALL`

## Trocar editor padrão no ubuntu

`$ sudo update-alternatives --config editor`

## Criar atalhos

Crie o arquivo .desktop dessa forma

    [Desktop Entry]
    Type=Application
    Name=Postman
    Exec=/home/diogomenezes/Documentos/Postman/Postman
    Icon=/home/diogomenezes/Documentos/Postman/icon.png
    Categories=Development

Depois salve no desktop ou em:

    /usr/share/applications

    ~/.local/share/applications

Ao salvar em /usr/share/applications a aplicação passa a ficar disponivel para ser incluida no favoritos do CentOs. Lembre de colocar o owner do .dektop como root e o grupo também.