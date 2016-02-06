# Configurando firewall com UFW

## Instalando

`apt-get install ufw`

## Ativando as portas

`ufw allow ssh`
`ufw allow www`
`ufw allow https`

## Bloqueando tudo por padrao

`ufw default deny`

## Limitar ssh bloqueia bruteforce

`ufw limit ssh`

## Ativando ufw

`ufw enable`
`ufw logging on`
`ufw logging low`
`ufw reload`

## Listar regras

`ufw status`

## Liberar ou bloquear portas

`ufw allow 3000/tcp`

`ufw deny 3000/tcp`

`ufw reload`

## Remover regras

`ufw status numbered`
`ufw delete 4`
`ufw reload`
