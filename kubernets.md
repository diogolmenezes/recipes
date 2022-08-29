
# Kubernets

    Plataforma de orquestracao de containers criada pelo Google.

    - alta disponibilidade
    - maior escalabilidade e performance
    - disaster recovery - backup e restore 


    
    
    Pelo menos 1 nó master com alguns nós  embaixo.
    Casa worker tem um node agent chamado kubelet que possibilita que os nós se falem 

    MASTER - Nó principal onde ficam expostas as apis e os servicos de controle do cluster, deve preferenciamente existir mais de um master no cluster, pois
    o cluster perde acesso aos workres uma vez que o master não está respondendo.

    WORKERS - Instancias, que funcionam compondo o parque do cluster
    
    PODS - É a menor unidade, cada aplicacao deve estar em um pod diferente mas um pod pode ter mais de um container dentro.

    SERVICES - Serverm para substituir o IP dos pods, dessa forma quando um pod cai e outro é criado no seu lugar, a comunicação continua a mesma pois o service abstrai esse endereço IP, funcionando ao mesmo tempo como um loadbalancer.

    DEPLOYMENT - Template para criacao de pods


    https://www.youtube.com/watch?v=VnvRFRk_51k&ab_channel=TechWorldwithNana 

    https://www.youtube.com/watch?v=Krpb44XR0bk&ab_channel=TechWorldwithNana

# EKS

    É o serviço gerenciado de Kubernets da AWS. Você cria o cluster, cria os work nodes que sao instancias EC2 que servem ao cluster depois basta enviar os
    pods para o kubernets.e

    https://www.youtube.com/watch?v=p6xDCz00TxU&ab_channel=TechWorldwithNana