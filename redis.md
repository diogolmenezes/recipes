# Redis

Redis é um banco de dados em memória que pode ser utilizado como cache, fila e etc.

## Inserindo strings

`SET resultado:megasena:20-05-2017 10,20,30,40,50,60`

## Inserindo varias strings

`MSET resultado:megasena:20-05-2017 10,20,30,40,50,60 resultado:megasena:21-05-2017 11,33,30,40,50,62`

## Recuperando strings

`GET resultado:megasena:20-05-2017`

## Excluindo strings

`DEL resultado:megasena:20-05-2017`

## Listando

`KEYS *`

## Usando * ? ou [] para filtrar

`KEYS "resultado*"`

`KEYS "*megasena*"`

`KEYS "resultado:megasena:*-05-2017"`

`KEYS "resultado:megasena:[23]?-05-2017"`

## Inserindo hashs

`HSET sessao:usuario:1234 "nome" "Diogo"`

`HSET sessao:usuario:1234 "notificacoes" "5"`

## Inserindo varios hashs

`HMSET sessao:usuario:1234 "nome" "Diogo" "notificacoes" 5`

## Recuperando hashs

`HGET sessao:usuario:1234 "nome"`

## Excluindo hash 

`HDEL sessao:usuario:1234 "nome"`

## Definindo uma espiração TTL em segundos

`EXPIRE sessao:usuario:1234 60`

## Verificando quanto tempo falta para expirar

`TTL sessao:usuario:1234`

## Fazendo incrementos de forma atomica

Adicionando 1 a chave de forma atomica.

`INCR pagina:/contato:views:10-05-2017`

Adicionando 15 a chave de forma atomica.

`INCRBY compras:10-05-2017:valor 15`

Adicionando 20.30 a chave de forma atomica.

`INCRBYFLOAT compras:10-05-2017:valor 20.30`

## Fazendo decrementos de forma atomica

Removendo 1 a chave de forma atomica.

`DECR pagina:/contato:views:10-05-2017`

Removendo 15 a chave de forma atomica.

`DECRBY compras:10-05-2017:valor 15`


Removendo 20.30 a chave de forma atomica.

`DECRBYFLOAT compras:10-05-2017:valor 20.30`



