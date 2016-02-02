# Vagrant

## Links

- https://www.vagrantup.com
- https://nandovieira.com.br/usando-o-vagrant-como-ambiente-de-desenvolvimento-no-windows
- http://apt.hellobits.com/
- https://www.dev-metal.com/copy-duplicate-vagrant-box/

## Iniciando e subindo uma nova maquina

`meu-projeto$ vagrant init ubuntu/trusty64`

`meu-projeto$ vagrant up`

## Recarregando o vagrant

`meu-projeto$ vagrant reload`

## Empacotando uma máquina para usar depois

`meu-projeto$ vagrant halt`

`meu-projeto$ vagrant package`

Isso vai criar um pacote.box que contém a vm. Você pode renomear e mover ele para onde quiser.


## Criando uma maquina a partir de um empacotamento


`meu-projeto-2$ vagrant init`

No VagrantFile configure a maquina que deseja usar com o nome que você renomeou quando fez o empacotamento e seu caminho

`config.vm.box = "my-zend-framework-box"`

`config.vm.box_url = "http://domain.com/path/to/above.box"` ou  `config.vm.box_url = "file:///d:/folder/package.box"`


Lembre-se de mudar o IP e iniciar sua nova maquina

`config.vm.network :private_network, ip: "192.168.33.101"`

`meu-projeto-2$ vagrant up`