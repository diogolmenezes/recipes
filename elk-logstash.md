# ELK

## Imagem 
    
    https://hub.docker.com/r/sebp/elk/

## Documentação
    
    http://elk-docker.readthedocs.io/#usage
    https://github.com/spujadas/elk-docker

## Subindo a imagem com volume externo
    
    `sudo docker run -p 5601:5601 -p 9200:9200  -p 5044:5044 -p 5000:5000 -v elk-data:/var/lib/elasticsearch --name elk sebp/elk`

## Prefixo para indice do kibana
    
    filebeat*


# FileBeat

## Instalação

    Na maquina da aplicação instalar o fileBeat que vai ser responsavel por mandar os logs para o logstash ou diretamente para o elastic search

    curl -L -O https://download.elastic.co/beats/filebeat/filebeat-1.3.1-x86_64.rpm
    sudo rpm -vi filebeat-1.3.1-x86_64.rpm

## Arquivo de configuração

    /etc/filebeat/filebeat.yml

## Testar configuração
    
    `$ ./filebeat -configtest -e`

## Iniciando o filebeat
    
    sudo /etc/init.d/filebeat start

## Iniciando o filebeat como debug

    cd /bin 
    sudo filebeat.sh -e 

## Documentação
    
    https://www.elastic.co/products/beats/filebeat
    https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-getting-started.html