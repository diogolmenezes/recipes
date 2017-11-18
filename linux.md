## Listar aplicacoes que mais consimem memoria

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

# Criar atalhos

Crie o arquivo .desktop dessa forma

    [Desktop Entry]
    Type=Application
    Name=Postman
    Exec=/home/diogomenezes/Documentos/Postman/Postman
    Icon=/home/diogomenezes/Documentos/Postman/icon.png
    Categories=Development

Depois salve no desktop ou em 

    /usr/share/applications
    ~/.local/share/applications
