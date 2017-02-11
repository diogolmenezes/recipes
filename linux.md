## Listar aplicacoes que mais consimem memoria

`ps aux --sort -rss | head`

## Memoria livre

`free -m` ou `free -h


## Sudo sem senha

`$ sudo visudo`

colocar:

`usuario   ALL=NOPASSWD: ALL`

## Trocar editor padrão no ubuntu

`$ sudo update-alternatives --config editor`