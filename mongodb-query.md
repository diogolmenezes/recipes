# MongoDb - Query

Exemplos de consultas no mongo

## Queries sem os objetos do mongoose (+performance)

- http://www.tothenew.com/blog/high-performance-find-query-using-lean-in-mongoose-2/
- http://mongoosejs.com/docs/api.html#query_Query-lean

Documentos retornados de consultas com a opção lean habilitada são objetos javascript simples, não documentos Mongoose. 
Eles não têm nenhum método save, getters / setters ou outra magia de mongoose aplicada.

```javascript
db.alunos.find({}).lean()
```


## Criando colecoes

```javascript
db.createCollection("alunos")
```

## Inserindo

```javascript
db.alunos.insert({
    nome: 'Diogo',
    data_nascimento: new Date(1985, 4, 2),
    habilidades: [
        {
            nome: "ingles",
            nivel: "avancado"
        },
        {
            nome: "corrida",
            nivel: "iniciante"
        }
    ]
})
```

## Removendo

```javascript
db.alunos.remove({
    _id: ObjectId('9803982983298ssdklj')
})
```

```javascript
db.alunos.remove({
    nome: 'Diogo'
})
```

## Listando

```javascript
db.alunos.find({})
```

```javascript
db.alunos.find({ nome: 'Diogo' })
```

## Pretty

Deixa o output da query mais amigavel

```javascript
db.alunos.find().pretty()
```

## Ordenação

Use 1 para ASC e -1 para DESC

```javascript
{ $sort: { statusCadastro: 1, dataAlteracao: -1 } }
```


## Select + In

```sql
select ... from ... where cpf in ('00000000000', '11111111111')
```

```javascript
db.getCollection('colecao').find({ 
   cpf:  { $in: ['00000000000', '11111111111'] }
})
```

# Distinct

```javascript
db.getCollection('colecao').distinct('cpf')
```

## Distinct com count

```javascript
db.getCollection('colecao').distinct('cpf', {
    $or: [{ statusCadastro: 1}, { statusCadastro: 2}],
    dataAlteracao: { $gte: ISODate('2015-12-31'), $lte: ISODate('2016-12-31') } 
}).length
```


# Agrupamentos

## Group + Having

```sql
select ... from ... group by cpf having count > 1
```

```javascript
db.getCollection('colecao').aggregate([
    { $sort: { statusCadastro: 1, dataAlteracao: 1 } },
    {
        $group: {
            _id: '$cpf',
            count: { $sum: 1 }
        }
    },
    { 
        $match: {
            count: { $gt: 1 }
        }
    }
])
```

## 2 grupos aninhados

Agrupando por CPF depois por status. Contando total de cpfs por status sem replicação.

```javascript
db.getCollection('colecao').aggregate([
    { $sort: { statusCadastro: 1, dataAlteracao: 1 } },
    {
        $group: {
            _id: '$cpf',
            status: { $last: '$statusCadastro' },
        }
    },
    {
        $group: {
            _id: '$status',
            count: { $sum: 1 }
        }
    }
])
```