# Redis

Redis é um banco de dados em memória que pode ser utilizado como cache, fila e etc.

- https://redis.io/
- https://redis.io/commands

## Testar online

- http://try.redis.io/

## Redis on docker

- https://hub.docker.com/_/redis/

## Client de Redis rodando em  Docker REBROW

`docker run --name redis-client -p 5001:5001 --link redis -d  marian/rebrow`

## Listando

### Usando * ? ou [] para filtrar

`KEYS *`

`KEYS "resultado*"`

`KEYS "*megasena*"`

`KEYS "resultado:megasena:*-05-2017"`

`KEYS "resultado:megasena:[23]?-05-2017"`


## Expiração

### Definindo uma espiração TTL em segundos

`EXPIRE sessao:usuario:1234 60`

### Verificando quanto tempo falta para expirar

`TTL sessao:usuario:1234`

## Tipos

### Verificando o tipo de uma chave

`TYPE resultado:megasena:20-05-2017`

## Strings

### Inserindo strings

`SET resultado:megasena:20-05-2017 10,20,30,40,50,60`

### Inserindo varias strings

`MSET resultado:megasena:20-05-2017 10,20,30,40,50,60 resultado:megasena:21-05-2017 11,33,30,40,50,62`

### Recuperando strings

`GET resultado:megasena:20-05-2017`

### Excluindo strings

`DEL resultado:megasena:20-05-2017`

## Hashs

Interessante quando queremos agrupar valores

### Inserindo hashs

`HSET sessao:usuario:1234 "nome" "Diogo"`

`HSET sessao:usuario:1234 "notificacoes" "5"`

### Inserindo varios hashs

`HMSET sessao:usuario:1234 "nome" "Diogo" "notificacoes" 5`

### Recuperando hashs

Retorna apenas o nome

`HGET sessao:usuario:1234 "nome"`

Retorna todas as informações do usuario

`HGETALL sessao:usuario:1234 "nome"`

### Excluindo hash

`HDEL sessao:usuario:1234 "nome"`

## Incrementos

### Fazendo incrementos de forma atomica

Adicionando 1 a chave de forma atomica.

`INCR pagina:/contato:views:10-05-2017`

Adicionando 15 a chave de forma atomica.

`INCRBY compras:10-05-2017:valor 15`

`HINCRBY jogador:1 pontos 15`

Adicionando 20.30 a chave de forma atomica.

`INCRBYFLOAT compras:10-05-2017:valor 20.30`

`HINCRBYFLOAT jogador:1 dinheiro 20.30`

### Fazendo decrementos de forma atomica

Removendo 1 a chave de forma atomica.

`DECR pagina:/contato:views:10-05-2017`

Removendo 15 a chave de forma atomica.

`DECRBY compras:10-05-2017:valor 15`

`HDECRBY jogador:1 pontos 15`


Removendo 20.30 a chave de forma atomica.

`DECRBYFLOAT compras:10-05-2017:valor 20.30`

`HDECRBYFLOAT jogador:1 pontos 20.30`

## Bits

### Inserindo Bits

Interessante para guardar boleanos, flags de usuarios por exemplo

Indicando que o usuario 23, 24, 30 acessou

`SETBIT acesso:20-05-2017 23 1`

`SETBIT acesso:20-05-2017 24 1`

`SETBIT acesso:20-05-2017 30 1`

### Recuperando Bits

Verificando se o usuario 23, 90 acessaram

`GETBIT acesso:20-05-2017 23`

`GETBIT acesso:20-05-2017 90`

### Contrando numero de Bits

Contando quantos acessos tivemos no dia

`BITCOUNT acesso:20-05-2017`

## Listas e filas

Listas são mais usadas para listagens de noticas mais recentes por exemplo. Já as filas para processamentos em background.

### Inserindo em Listas

No inicio Left

`LPUSH lista_noticias "noticia"`

### Tamanho da lista_noticias

`LLEN lista_noticias`

### Listando elementos da lista_noticias

Lista os 3 primeiros elementos da lista

`LRANGE lista_noticias 0 2`

### Reduzindo uma lista com TRIM

Mantendo apenas as 3 notícias mais recentes

`LTRIM lista_noticias 0 2`

### Pega um elemento da lista

Retorna o primeiro elemento

`LINDEX lista_noticias 0`

### Inserindo em filas

Como é uma estrutura de fila é importante sempre incluirmos no final e excluirmos no inicio, assim o mais antigo da fila é sempre tratado primeiro.

`RPUSH fila:confirmacao_email "diogolmenezes@gmail.com"`

### Tirando da fila garantindo atomicidade

Tira e devolve o primeiro elemento da lista em uma só operação

`LPOP fila:confirmacao_email`

Tira e devolve o ultimo elemento da lista em uma só operação

`RPOP fila:confirmacao_email`

Aguarda por 30 segundos tentando dar um POP dessa forma não precisamos ficar chamando o POP varias vezes quando a lista esta vazia

`BLPOP fila:confirmacao_email 30`

Aguarda indeterminadamente ate alguem chegar

`BLPOP fila:confirmacao_email 0`

## Conjuntos sem ordem

Podemos por exemplo criar conjuntos de amigos

### Inserindo em um conjunto

`SADD amigos:diogo "joao" "maria" "ana"`

### Removendo do conjunto

`SREM amigos:diogo maria`

### Tamanho ou cardinalidde do conjunto

`SCARD amigos:diogo`

### Conteudo do conjunto

`SMEMBERS amigos:diogo`

### Verificando se elemento esta em um conjunto

`SISMEMBER amigos:diogo ana`

### Interseção de conjuntos

Amigos em comum do diogo com a beatriz

`SADD amigos:diogo   "joao" "maria" "ana"`

`SADD amigos:beatriz "joao" "otavio" "ana"`

`SINTER amigos:diogo amigos:beatriz`

### Diferença de conjuntos

Populando os amigos

`SADD amigos:diogo   "joao" "maria" "ana"`

`SADD amigos:beatriz "joao" "otavio" "ana"`

Amigos do diogo que a beatriz não conhece

`SDIFF amigos:diogo amigos:beatriz`

Amigos da beatriz que o diogo não conhece

`SDIFF amigos:beatriz amigos:diogo`

## Conjuntos com ordem

### Incluindo no conjunto ordenado

`ZADD pontuacoes 20540 diogo`

`ZADD pontuacoes 1540 joao`

`ZADD pontuacoes 28540 luana`

### Tamanho ou cardinalidde do conjunto ordenado

`ZCARD pontuacoes`

### Conteudo do conjunto ordenado

3 mais bem colocados

`ZREVRANGE pontuacoes 0 3 WITHSCORES`

3 mais mal colocados

`ZRANGE pontuacoes 0 3`