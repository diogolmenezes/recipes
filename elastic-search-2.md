# Bancos Relacionais X Elastic Search

De x Para entre bancos relacionais e o ES

Instancia x Index
Tabela    x Type
Schema    x Mapping
Tupla     x Documento
Coluna    x Atributo


# operação

## Listando indices

`$ curl 'localhost:9200/_cat/indices?v' `

## Verificando o schema de um tipo

Caso um mapping nao seja definido para o tipo, o proprio ES vai inferir os tipos e criar. Isso funciona da mesma
forma que o DESC do SQL.

https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html
https://www.elastic.co/guide/en/elasticsearch/reference/current/date.html

`$ curl 'localhost:9200/catalogo/_mapping/pessoas' `

## Desligando as replicas

Caso queria usar apenas um nó sem replicas

```PUT para /catalogo/_settings
{
    "index" : {
        "number_of_replicas" : 0
    }
}```

## POST /index/type

Aqui temos mais uma pequena ressalva em relação aos comandos SQL. A rigor, o comando POST significa crie um documento sob /index/type. Repare que, diferentemente do comando e dos outros comandos, id não é obrigatório. Poderíamos pensar então que o comando POST é uma espécie de insert que utiliza sequences ou um algum tipo de auto incremento. Contudo, também podemos utilizar o comando POST para atualizar um documento existente assim como o comando PUT. A diferença é que POST nos permite atualização parcial de documentos. Por exemplo:

```POST /catalogo/pessoas/1/_update
{
    "doc": {
        "nome": "João Pedro"
    }
}```

Note que, neste caso, o uso de "doc" é obrigatório.

Importante: Uma vez que um documento é criado em uma instância do ElasticSearch, este documento torna-se imutável. No caso de uma atualização a um documento existente, por exemplo como fizemos com o método POST, uma nova versão do documento é criada. Se repararmos bem nas respostas recebidas, notaremos os atributos _created e _version. A resposta para a criação de um novo documento possui _created = true e _version = 1. A resposta para atualizações possui _created = false e _version será a versão anterior do documento acrescida de 1.

## GET

GET /indice/tipo/identificator

```http://localhost:9200/catalogo/pessoas/1```

## HEAD

A operação de HEAD é similar a um select 1 from table where id = 1, e serve para saber se um
documento existe. Retornando um 200 ou um 404.

```$ curl -XHEAD -v http://localhost:9200/catalogo/pessoas/1```

## PUT /index/type/id

Aqui temos uma pequena ressalva em relação aos comandos SQL. A rigor, o comando PUT significa coloque o documento sob o localizador /index/type/id. Repare que id, assim como nos comandos anteriores é obrigatório. Poderíamos pensar então que o comando PUT é uma espécie de:

`insert into TYPE (id, atributo1, atributo2) values (ID, valor1, valor2);`

Contudo, também podemos utilizar o comando PUT para substituir um documento. Neste caso, o comando PUT funciona como uma espécie de:

`update TYPE set atributo1 = valor1, atributo2 = valor2 where id = ID;`

## DELETE

Uma requisição DELETE /index/type/id é Equivalente ao comando:

delete from TYPE where id = ID;

Remove o documento do tipo caso ele exista, ou NOT FOUND 404, caso contrário.

## Contando documentos

```http://localhost:9200/indice/tipo/_count```

## Busca

### Busca no indice sem filtros

GET /indice/tipo/_search

Ainda que o ElasticSearch seja orientado a documentos, é possível retornar apenas uma parte do documento. Para tal, basta adicionar ?_source=atributo 1,atributo 2. Note que podemos ainda utilizar o parâmetro pretty para retornar o documento formatado. Exemplo: GET /catalogo/pessoas/1?pretty&_source=interesses

A busca sem parametros usa o campo _all que é o campo que o elastic search coloca todos os termos do documento juntos para facilitar a busca

GET /indice/tipo/_search?q=futebol

GET /indice/tipo/_search?q=_all:futebol

GET /indice/tipo/_search?q=nome:João&estado=RJ

### Limitando o tamanho da busca e paginação

GET /indice/tipo/_search?q=nome:João&size=10

GET /indice/tipo/_search?q=nome:João&from=10&size=10

Importante: Paginação deve ser usada apenas para pequenos volumes de dados, como alguns poucos milhares. Para volumes maiores, devemos utilizar a abordagem da API scroll.

Quando utilizamos paginação simples, temos que tomar cuidado para não causarmos instabilidade no cluster. Caso solicitemos 10.000 registros, todas as shards participantes na consulta podem retornar esta quantidade. Por exemplo, caso tenhamos 3 shards com 100.000 documentos em cada e pedimos os primeiros 10.000 resultados, o nó coordenador terá de processar 30.000 documentos, executar a ordenação e pegar os primeiros 10.000.

Para detalhes, veja este link:

https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-scroll.html

# Plugins

No Ubuntu, usualmente a pasta dos plugins do ES fica em /usr/share/elasticsearch/bin/plugin

## kopf

Plugin para elastic search que permite fazer consultas rest e ver a saude do cluster.

### Instalação

#### Por Zip

Baixar o zip do kopf e extrair na pasta plugins que no ubuntu fica em /usr/share/elasticsearch/bin/plugins

#### Por Repositório
```
$ cd /usr/share/elasticsearch/bin/plugins
$ ./plugin install lmenezes/elasticsearch-kopf/v2.1.2
$ service elasticsearch restart
```

Para acessar um plugin do ES, basta inserir o nome do plugin na URL como por exemplo:

http://localhost:9200/_plugin/kopf/



# Shards e Réplicas

Shard é um termo que passou a ser muito utilizado quando pensamos em particionamento horizontal de dados. Para colocar de modo mais claro, pense que temos um volume muito grande de dados, tão grande, que é inviável em armazená-lo em um único local. Nosso instinto de bom engenheiro de software nos diz que deveríamos quebrar este grande volume de dados em blocos menores, já que fica mais fácil de operar com blocos menores durante operações como cópia, remoção ou relocação. Cada pedacinho resultante desta quebra é o que chamamos de shard.

Shards são de altíssima importância quando pensamos em escalabilidade horizontal. Podemos quebrar grandes blocos de dados em pedaços menores e distribuí-los entre diferentes máquinas em um cluster. Dado o número de shards, podemos usar uma função de espalhamento (hashing) para definir em qual shard um determinado documento deve ser armazenado. Isso é exatamente o que o ElasticSearch faz (bem, o processo na prática é um pouco mais complexo, mas a ideia é a mesma). A escalabilidade horizontal para o volume de dados que foi comentada no capítulo anterior é implementada com o uso de shards.

Como temos diversas shards, podemos criar cópias, chamadas de réplicas, de uma mesma shard e armazená-las em outras máquinas. Isso funciona como uma espécie de backup dos dados, com uma pequena ressalva: réplicas são atualizadas constantemente e podem ser utilizadas para leitura durante a execução de consultas. Existe dois tipos de shards no ElasticSeach:

- Shard primária (primary shards): é a shard onde as operações de escrita como criação, atualização ou remoção de um documento acontece primeiro.
- Shard réplica (replica shard): é a shard que, uma vez que a operação de escrita tenha sido concluída com sucesso na sua respectiva shard primária, recebe a mesma operação para que ela seja replicada. A operação só será confirmada para o cliente quando todas as réplicas confirmarem a replicação. Logo, quando recebemos o HTTP OK para uma operação de escrita, sabemos que a informação esta segura em todas as réplicas.

Importante: O número de shards é definido no momento da criação do índice e não pode ser alterado. O número de réplicas também é definido no momento da criação do índice, porém pode ser alterado com o passar do tempo. É muito importante escolher bem o número de shards durante a criação do índice. A escolha deste número depende do volume de informações que queremos armazenar nas shards. Em geral, queremos shards com alguns gigabytes

O número de shards de um índice é definido no momento da criação do mesmo, e não pode ser alterado. Por isso é muito importante escolher bem o número de shards durante a criação do índice. Mas este número depende do volume de informações que serão armazenadas nas shards, logo esse volume é dividido pela quantidade de shards desejada, vendo assim o quão grande cada shard será.

Na prática, isso significa que muitas shards auxiliam na hora da escrita, mas o desempenho pode ser afetado na hora da leitura, pois uma busca terá que ler muitas shards para confirmar se as informações foram encontradas ou não, por exemplo. Por isso que depende da frequência em que se escreve e que se lê na shard, mas uma shard não deve exceder o volume de 50 gb.