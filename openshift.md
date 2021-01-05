# Openshift

## Materiais: 
    
    - Roteiro: https://redhat-sa-brazil.gitbook.io/workshop-openshift/v/v3.6/
    - OpenShift: https://console.paas.rhbrlab.com
    - Oc client:  https://developers.redhat.com/products/openshift/download/
    - Minishift: https://developers.redhat.com/products/cdk/download/
    - Metrics: https://hawkular-metrics.apps.paas.rhbrlab.com/hawkular/metrics
    - Red Hat Container Catalog: https://access.redhat.com/containers/
    - Benchmarking de Performance Mensageria: https://softwaremill.com/mqperf/#summary

# OC client

- https://developers.redhat.com/products/openshift/download/


## Instalar base de imagens

oc create -f https://raw.githubusercontent.com/openshift/origin/master/examples/image-streams/image-streams-rhel7.json

## Login

oc login

## Listar projetos

oc list

## Entrar em um projeto

oc project nome

## Listar pods 

oc get pods

## Deletar pods

oc delete pod nome-do-pod

oc delete pod nome-do-pod --grace-period 0 --force

## Exportar as configurações

oc export --all

## Acessando um pod ( container )

oc rsh nome-do-pod


