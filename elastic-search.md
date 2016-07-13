# Elastic Search
==============================

Instalação:
==============================

    Existem diversas formas de instalar o elastic search:

        https://www.elastic.co/downloads/elasticsearch
        https://github.com/elastic/elasticsearch
        https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-elasticsearch-on-ubuntu-14-04

    O Elastic Search precisa do java instalado:

            sudo add-apt-repository -y ppa:webupd8team/java
            sudo apt-get update
            sudo apt-get -y install oracle-java8-installer

        Verifique a instalação

            java -version


    Eu optei por instalar usando o pacote .deb:

        Baixando

            wget https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/deb/elasticsearch/2.3.4/elasticsearch-2.3.4.deb

        Instalando

            dpkg -i elasticsearch-2.3.4.deb

        Colocando para inicializar sozinho

            sudo update-rc.d elasticsearch defaults 95 10

        Verificando e iniciando o serviço

            /etc/init.d/elasticsearch status
            /etc/init.d/elasticsearch start

        Testando a porta

            curl -X GET 'http://localhost:9200'



Utilização com searchkick:
==============================
    https://github.com/ankane/searchkick

    Indexação:

            User.reindex

    Busca:

            User.search "diogo"


Utilização sem searchkick:
==============================

    - http://www.sitepoint.com/full-text-search-rails-elasticsearch/
    - http://www.codinginthecrease.com/news_article/show/409843?referrer_id=

    Criando os indices:

        No console do Rails, crie os indices.

        `bundle exec rails c production`

        `User.__elasticsearch__.create_index! force: true`
        `Destination.__elasticsearch__.create_index! force: true`
        `Place.__elasticsearch__.create_index! force: true`
        `Country.__elasticsearch__.create_index! force: true`

    Indexe seu conteudo:

        `bundle exec rake environment elasticsearch:import:model CLASS='User' RAILS_ENV='production';bundle exec rake environment elasticsearch:import:model CLASS='Place' RAILS_ENV='production';bundle exec rake environment elasticsearch:import:model CLASS='Destination' RAILS_ENV='production';bundle exec rake environment elasticsearch:import:model CLASS='Country' RAILS_ENV='production'`

    Rodando em outro servidor

        Caso vc queira rodar em outra url vc pode definir um initializer no seu projeto rails

        `Elasticsearch::Model.client = Elasticsearch::Client.new host: 'http://localhost:9200/'`
