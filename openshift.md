# Openshift

# OC client

- https://developers.redhat.com/products/openshift/download/


## Instalar base de imagens

oc create -f https://raw.githubusercontent.com/openshift/origin/master/examples/image-streams/image-streams-rhel7.json

## Subir um cluster

oc cluster up

## Descer um cluster

oc cluster down

## Listar pods 

oc get pods

## Deletar pods

oc delete pods nome-do-pod

## Exportar as configurações

oc export --all

## Acessando um pod ( container )

oc rsh nome-do-pod


