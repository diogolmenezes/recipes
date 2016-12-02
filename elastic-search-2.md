# Bancos Relacionais X Elastic Search

De x Para entre bancos relacionais e o ES

Instancia x Index
Tabela    x Type
Schema    x Mapping
Tupla     x Documento
Coluna    x Atributo


# Operação

## Listando indices

`$ curl 'localhost:9200/_cat/indices?v' `

## Excluindo indices

`curl -X "DELETE" http://localhost:9200/nome-indice`

## Criando indices

Caso um indice nao seja explicitamente definido, o ES infere os tipos na hora da criacao do documento.

```
PUT /catalogo_v2
{
  "settings": {
    "index": {
      "number_of_shards": 3,
      "number_of_replicas": 0
    }
  },
  "mappings": {
    "pessoas": {
      "_all": {
        "type": "string",
        "index": "analyzed",
        "analyzer": "portuguese"
      },
      "properties": {
        "cidade": {
          "type": "string",
          "index": "analyzed",
          "analyzer": "portuguese"
        },
        "estado": {
          "type": "string"
        },
        "formação": {
          "type": "string",
          "index": "analyzed",
          "analyzer": "portuguese"
        },
        "interesses": {
          "type": "string",
          "index": "analyzed",
          "analyzer": "portuguese"
        },
        "nome": {
          "type": "string",
          "index": "analyzed",
          "analyzer": "portuguese"
        },
        "país": {
          "type": "string",
          "index": "analyzed",
          "analyzer": "portuguese"
        }
      }
    }
  }
}
```

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

`http://localhost:9200/catalogo/pessoas/1`

## HEAD

A operação de HEAD é similar a um select 1 from table where id = 1, e serve para saber se um
documento existe. Retornando um 200 ou um 404.

`$ curl -XHEAD -v http://localhost:9200/catalogo/pessoas/1`

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

`http://localhost:9200/indice/tipo/_count`

## Busca

### Busca no indice sem filtros

`GET /indice/tipo/_search`

Ainda que o ElasticSearch seja orientado a documentos, é possível retornar apenas uma parte do documento. Para tal, basta adicionar ?_source=atributo 1,atributo 2. Note que podemos ainda utilizar o parâmetro pretty para retornar o documento formatado. Exemplo: GET /catalogo/pessoas/1?pretty&_source=interesses

A busca sem parametros usa o campo _all que é o campo que o elastic search coloca todos os termos do documento juntos para facilitar a busca

`GET /indice/tipo/_search?q=futebol`

`GET /indice/tipo/_search?q=_all:futebol`

`GET /indice/tipo/_search?q=nome:João&estado=RJ`

`GET /indice/tipo/_search?q=João+AND+Menezes`

### Limitando o tamanho da busca e paginação

`GET /indice/tipo/_search?q=nome:João&size=10`

`GET /indice/tipo/_search?q=nome:João&from=10&size=10`

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

`http://localhost:9200/_plugin/kopf/`


# Shards e Réplicas

Shard é um termo que passou a ser muito utilizado quando pensamos em particionamento horizontal de dados. Para colocar de modo mais claro, pense que temos um volume muito grande de dados, tão grande, que é inviável em armazená-lo em um único local. Nosso instinto de bom engenheiro de software nos diz que deveríamos quebrar este grande volume de dados em blocos menores, já que fica mais fácil de operar com blocos menores durante operações como cópia, remoção ou relocação. Cada pedacinho resultante desta quebra é o que chamamos de shard.

Shards são de altíssima importância quando pensamos em escalabilidade horizontal. Podemos quebrar grandes blocos de dados em pedaços menores e distribuí-los entre diferentes máquinas em um cluster. Dado o número de shards, podemos usar uma função de espalhamento (hashing) para definir em qual shard um determinado documento deve ser armazenado. Isso é exatamente o que o ElasticSearch faz (bem, o processo na prática é um pouco mais complexo, mas a ideia é a mesma). A escalabilidade horizontal para o volume de dados que foi comentada no capítulo anterior é implementada com o uso de shards.

Como temos diversas shards, podemos criar cópias, chamadas de réplicas, de uma mesma shard e armazená-las em outras máquinas. Isso funciona como uma espécie de backup dos dados, com uma pequena ressalva: réplicas são atualizadas constantemente e podem ser utilizadas para leitura durante a execução de consultas. Existe dois tipos de shards no ElasticSeach:

- Shard primária (primary shards): é a shard onde as operações de escrita como criação, atualização ou remoção de um documento acontece primeiro.
- Shard réplica (replica shard): é a shard que, uma vez que a operação de escrita tenha sido concluída com sucesso na sua respectiva shard primária, recebe a mesma operação para que ela seja replicada. A operação só será confirmada para o cliente quando todas as réplicas confirmarem a replicação. Logo, quando recebemos o HTTP OK para uma operação de escrita, sabemos que a informação esta segura em todas as réplicas.

Importante: O número de shards é definido no momento da criação do índice e não pode ser alterado. O número de réplicas também é definido no momento da criação do índice, porém pode ser alterado com o passar do tempo. É muito importante escolher bem o número de shards durante a criação do índice. A escolha deste número depende do volume de informações que queremos armazenar nas shards. Em geral, queremos shards com alguns gigabytes

O número de shards de um índice é definido no momento da criação do mesmo, e não pode ser alterado. Por isso é muito importante escolher bem o número de shards durante a criação do índice. Mas este número depende do volume de informações que serão armazenadas nas shards, logo esse volume é dividido pela quantidade de shards desejada, vendo assim o quão grande cada shard será.

Na prática, isso significa que muitas shards auxiliam na hora da escrita, mas o desempenho pode ser afetado na hora da leitura, pois uma busca terá que ler muitas shards para confirmar se as informações foram encontradas ou não, por exemplo. Por isso que depende da frequência em que se escreve e que se lê na shard, mas uma shard não deve exceder o volume de 50 gb.

# Analyzers

Analyzers são algoritmos fundamentais para o que chamamos de full-text search, ou busca de texto cheio. Vale destacar um conceito fundamental em relação a busca de texto cheio. Quando fazemos buscas exatas o resultado é binário, ou seja, "sim" caso o termo procurado exista da maneira que foi informado ou "não", caso contrário. No caso de busca de texto cheio, a ideia é diferente. Ao invés de perguntarmos "Este documento possui exatamente os termos utilizados na busca", estamos interessados em "O quão bem este documento casa com os termos da busca".

Vamos ver alguns exemplos. No primeiro exemplo, queremos procurar pelo termo "EUA" mas estamos satisfeitos com documentos com os termos "Estados Unidos da América" ou "Estados Unidos". No segundo exemplo, queremos procurar pelo termo "musica", porém estamos satisfeitos se encontrarmos documentos com os termos "bossa nova", "rock", "pagode" e "samba", pois eles se relacionam com música.

Analyzers são utilizados para processar nossos documentos e construir uma estrutura comum no mundo de buscas chamada de índice invertido. De maneira simplista, podemos pensar nos analyzers como algoritmos que processam o texto e geram entradas relevantes com os termos do documento, possivelmente com sinônimos, no índice invertido. Estas entradas possuem ponteiros para o documento. Os termos consultados também passam pelos mesmos algoritmos e os termos processados são utilizados para fazer a busca contra o índice invertido. Este processo ficará bem claro em alguns instantes.

## Analyzers existentes

ElasticSearch possui diversos analyzers pré-definidos que podem ser associados à atributos durante a criação do mapping para o tipo. Destacamos 4 analyzers que devemos conhecer.

- Analyzer padrão: Este é o analyzer padrão usado pelo ElasticSearch e em geral funciona bem independente do idioma. Ele funciona quebrando o texto em palavras removendo pontuações e passando todo conteúdo para letras minúsculas. Números existentes no texto são mantidos. Por exemplo: "Eu nasci a 10 mil (sim, 10 mil) anos atrás" gera as seguintes entradas "eu", "nasci", "a", "10", "mil", "sim", "10", "mil", "anos", "atrás".
- Analyzer simples: Quebra o texto em tudo o que não seja uma letra e passando todo o texto para letras minúsculas. Como números não são letras, eles não geram entradas. E.g.: "Eu nasci a 10 mil (sim, 10 mil) anos atrás" gera as seguintes entradas "eu", "nasci", "a", "mil", "sim", "mil", "anos", "atrás".
- Analyzer de espaço em branco: Quebra o texto por espaços em branco. Não há alteração na caixa das letras. Por exemplo: "Eu nasci a 10 mil (sim, 10 mil) anos atrás" gera as seguintes entradas "Eu", "nasci", "a", "10", "mil", "(sim", "10", "mil)", "anos", "atrás".
- Analyzers específicos para idiomas: São analyzers que quebram o texto assim como o analyzer padrão, porém são capazes de aplicar peculiaridades do idioma e melhorar a geração das entradas para um idioma em específico. Técnicas como singularização dos termos, remoção de palavras que não possuem relevância para o resultado, como palavras comuns do idioma e uso da palavra na sua forma mais raíz (conhecido como stemming), são aplicadas.

Como passo adicional, analyzers ainda podem ser customizados caso necessário. Para mais detalhes veja https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-analyzers.html

O mais legal é que existe uma API chamada _analyze que nos permite observar como um texto será analisado.

https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-analyzers.html

### Testando analyzers

`/_analyze?analyzer=standard&text=Eu+Nasci+a+10+mil+(sim,+10+mil)+anos+atras`

`/_analyze?analyzer=simple&text=Eu+Nasci+a+10+mil+(sim,+10+mil)+anos+atras`

`/_analyze?analyzer=whitespace&text=Eu+Nasci+a+10+mil+(sim,+10+mil)+anos+atras`

`/_analyze?analyzer=portuguese&text=Eu+Nasci+a+10+mil+(sim,+10+mil)+anos+atras`

https://www.elastic.co/guide/en/elasticsearch/guide/current/inverted-index.html

# Sinonimos

Sinônimos podem substituir tokens ou adicionar tokens ao índice invertido e são implementados via analyzers customizados.

## Criando

Lembrando que as palavras so sinonimo devem ser as retornadas peelo analyser portuguese, sessa forma serão encontradas melhor na busca, podendo pesquisar, acento, plural etc.

PUT /indice_com_sinonimo_2

```
{
  "settings": {
    "index": {
      "number_of_shards": 3,
      "number_of_replicas": 0
    },
    "analysis": {
      "filter": {
         "portuguese_stop": {
          "type":       "stop",
          "stopwords":  "_portuguese_"
        },
        "portuguese_stemmer": {
          "type": "stemmer",
          "language": "light_portuguese"
        },
        "filtro_de_sinonimos": {
          "type": "synonym",
          "synonyms": [
            "futebol => futebol,society",
            "society => society,futebol",
            "volei,voleibol,volleyball",
            "esport => esport,futebol,society,volei,basquet",
            "exat => exat,matematic,fisic,computaca",
            "arte => arte,pintur,teatr,music,cinem"
          ]
        }
      },
      "analyzer": {
        "sinonimos": {
          "tokenizer": "standard",
          "filter": [
            "lowercase",
            "portuguese_stop",
            "portuguese_stemmer",
            "filtro_de_sinonimos"
          ]
        }
      }
    }
  },
  "mappings": {
    "pessoas": {
      "_all": {
        "type": "string",
        "index": "analyzed",
        "analyzer": "portuguese"
      },
      "properties": {
        "cidade": {
          "type": "string",
          "index": "analyzed",
          "analyzer": "portuguese"
        },
        "estado": {
          "type": "string"
        },
        "formação": {
          "type": "string",
          "index": "analyzed",
          "analyzer": "portuguese"
        },
        "interesses": {
          "type": "string",
          "index": "analyzed",
          "analyzer": "portuguese",
          "search_analyzer": "sinonimos"
        },
        "nome": {
          "type": "string",
          "index": "analyzed",
          "analyzer": "portuguese"
        },
        "país": {
          "type": "string",
          "index": "analyzed",
          "analyzer": "portuguese"
        }
      }
    }
  }
}
```

## Arquivo de sinonimos

Como vimos neste capítulo, podemos definir sinônimos diretamente no atributo synonyms do filtro que definimos. Podemos também fazer uso do atributo synonyms_path para indicar o arquivo de onde os sinônimos serão lidos. O caminho do arquivo deve ser ou relativo ao diretório de configuração (config) do Elasticsearch ou um caminho absoluto para o arquivo.

https://www.elastic.co/guide/en/elasticsearch/reference/current/analysis-synonym-tokenfilter.html

## Testando

`/catalogo_com_sinonimo/_analyze?analyzer=esporte`

## Fuzziness e erros de digitação

É razoável afirmar que todos nós, uma vez na vida, já recebemos sugestões qual fazemos busca no Google como "Você quis dizer…?". E em geral, quando recebemos esta sugestão, cometemos algum erro de digitação. ElasticSearch também dá suporte a tal tipo de sugestão através da busca Fuzzy.

Na prática, este tipo de busca não é usada para retornar documentos automaticamente, mas sim para dar sugestões aos usuários do que eles realmente estariam procurando.

https://www.elastic.co/guide/en/elasticsearch/guide/current/fuzzy-matching.html

# Multi-campos

Como parte da definição de um atributo no mapeamento de um índice, basta adicionar os campos extras a serem gerados para um atributo e sua configuração. Veja o exemplo a seguir para o atribute nome:

```
"nome": {
    "type": "string",
    "fields": {
        "original": {
            "type": "string",
            "index": "not_analyzed"
        }
    },
    "index": "analyzed",
    "analyzer": "portuguese"
}
```

O ElasticSearch irá criar um campo chamado nome.original e manterá uma cópia do valor para o atributo nome, sem analisá-lo. Vale notar que original poderia ser qualquer nome válido de campo. É comum o uso nomes como raw ou plain.

# Indice completo para exemplo

```
PUT /pessoas
{
  "settings": {
    "index": {
      "number_of_shards": 3,
      "number_of_replicas": 0
    },
    "analysis": {
      "filter": {
         "portuguese_stop": {
          "type":       "stop",
          "stopwords":  "_portuguese_"
        },
        "portuguese_stemmer": {
          "type": "stemmer",
          "language": "light_portuguese"
        },
        "filtro_de_sinonimos": {
          "type": "synonym",
          "synonyms": [
            "futebol => futebol,society",
            "society => society,futebol",
            "volei,voleibol,volleyball",
            "esport => esport,futebol,society,volei,basquet",
            "exat => exat,matematic,fisic,computaca",
            "arte => arte,pintur,teatr,music,cinem"
          ]
        }
      },
      "analyzer": {
        "sinonimos": {
          "tokenizer": "standard",
          "filter": [
            "lowercase",
            "portuguese_stop",
            "portuguese_stemmer",
            "filtro_de_sinonimos"
          ]
        }
      }
    }
  },
  "mappings": {
    "registros": {
      "_all": {
        "type": "string",
        "index": "analyzed",
        "analyzer": "portuguese"
      },
      "properties": {
        "cidade": {
          "type": "string",
          "fields": {
            "original": {
              "type": "string",
              "index": "not_analyzed"
            }
          },
          "index": "analyzed",
          "analyzer": "portuguese"
        },
        "estado": {
          "type": "string",
          "index": "not_analyzed"
        },
        "formação": {
          "type": "string",
          "fields": {
            "original": {
              "type": "string",
              "index": "not_analyzed"
            }
          },
          "index": "analyzed",
          "analyzer": "portuguese"
        },
        "interesses": {
          "type": "string",
          "index": "analyzed",
          "analyzer": "portuguese",
          "search_analyzer": "sinonimos"
        },
        "nome": {
          "type": "string",
          "fields": {
            "original": {
              "type": "string",
              "index": "not_analyzed"
            }
          },
          "index": "analyzed",
          "analyzer": "portuguese"
        },
        "país": {
          "type": "string",
          "fields": {
            "original": {
              "type": "string",
              "index": "not_analyzed"
            }
          },
          "index": "analyzed",
          "analyzer": "portuguese"
        }
      }
    }
  }
}
```

# Bulk insert de dados

ElasticSearch possui a API _bulk para permite carregamento de dados em massa. A API é tão flexível que acaba se tornando um pouco confusa, pois ela permite que um mesmo request possua comandos para criar, atualizar e remover documentos em diferentes índices.

Por questão de simplicidade, vamos focar no que precisamos para atingir nossos objetivos. A _bulk API é suportada no método POST e a usaremos da seguinte maneira:

POST para /indice/tipo/_bulk

```
{"create": {}}
{"nome": "Diogo Menezes", "idade": 31}
{"create": {}}
{"nome": "Maria da Silva", "idade": 20}
```


https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html