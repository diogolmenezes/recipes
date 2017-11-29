# Visão geral

ab é uma ferramenta simplificada de teste de performance HTTP.

Mostra quantos requests por segundo sua aplicação é capaz de servir.

- https://httpd.apache.org/docs/2.4/programs/ab.html

## Instalação CentOS

```bash
yum provides /usr/bin/ab
yum install httpd-tools
```

## Instalação Ubuntu

```bash
apt-get install apache2-utils
```

## Rodando

Exemplo de POST com autenticação com 1 milhão de requests e 200 usuários simultáneos.

```bash
ab -p send.json -T application/json -H 'Authorization: Bearer b452e84ba6a2cfe468e4546602f7d923263fa64f' -n 1000000 -c 100 http://localhost:8094/token/send
```


