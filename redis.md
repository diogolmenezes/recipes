# Redis

Redis é um banco de dados em memória que pode ser utilizado como cache, fila e etc.

## Inserindo strings

```SET resultado:megasena:20-05-2017 10,20,30,40,50,60```

## Recuperando strings

GET resultado:megasena:20-05-2017

## Excluindo strings

DEL resultado:megasena:20-05-2017

## Listando

KEYS *

## Usando * ? ou [] para filtrar

KEYS "resultado*"

KEYS "*megasena*"

KEYS "resultado:megasena:*-05-2017"

KEYS "resultado:megasena:[23]?-05-2017"

## Definindo uma espiração TTL em segundos

EXPIRE resultado:megasena:20-05-2017 10

## Verificando quanto tempo falta para expirar

TTL resultado:megasena:20-05-2017



