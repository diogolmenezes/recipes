# MongoDb - Query

Exemplos de consultas no mongo

## Ordenação

Use 1 para ASC e -1 para DESC

`{ $sort: { statusCadastro: 1, dataAlteracao: -1 } },`


## Select + In

`select ... from ... where cpf in ('00000000000', '11111111111')`

```db.getCollection('colecao').find({ 
   cpf:  { $in: ['00000000000', '11111111111'] }
})```

# Agrupamentos

## Group + Having

`select ... from ... group by cpf having count > 1`

```db.getCollection('colecao').aggregate([
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
])```

## 2 grupos aninhados

Agrupando por CPF depois por status. Contando total de cpfs por status sem replicação.

```db.getCollection('colecao').aggregate([
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
])```