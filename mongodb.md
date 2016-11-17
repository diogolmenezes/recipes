# MongoDb

## Logar

mongo -u "usuario" -p "senha" --authenticationDatabase "admin" host:27017

## Numero de conexões

    var status = db.serverStatus()
    status.connections

# Replicações

## Status de todas as replicações

    rs.status();

## Status da replica a partir do primario

    rs.printReplicationInfo();

## Status da replica a partir dos secundarios
    
    rs.printSlaveReplicationInfo();

## Muda o primario

Faz o primario virar secundário

    rs.stepDown()

## Devolve as configurações do replicaset

    rs.conf();