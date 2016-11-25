# MongoDb

# IDE

- https://www.mongodb.com/products/compass
- https://robomongo.org/

## Logar

```shell
mongo -u "usuario" -p "senha" --authenticationDatabase "admin" host:27017
```

## Status do servidor

```javascript
db.serverStatus()
```

## Informações do host

```javascript
db.hostInfo()
```

## Numero de conexões

```javascript
var status = db.serverStatus()
status.connections
```

# Replicações

## Status de todas as replicações

```javascript
rs.status();
```

## Status da replica a partir do primario

```javascript
rs.printReplicationInfo();
```

## Status da replica a partir dos secundarios
    
```javascript
rs.printSlaveReplicationInfo();
```

## Muda o primario

Faz o primario virar secundário

```javascript
rs.stepDown()
```

## Devolve as configurações do replicaset

```javascript
rs.conf();
```

## Conectar em um slave

```javascript
mongodb://host:27017/banco?readPreference=secondary
```