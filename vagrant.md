# Vagrant

## Links

- https://www.vagrantup.com
- https://nandovieira.com.br/usando-o-vagrant-como-ambiente-de-desenvolvimento-no-windows
- http://apt.hellobits.com/
- https://www.dev-metal.com/copy-duplicate-vagrant-box/

## Iniciando e subindo uma nova maquina

`meu-projeto$ vagrant init ubuntu/trusty64`

`meu-projeto$ vagrant up`

## Diretorio sincronizado

O vagrant por padrao compartilha os dados do diretório /Vagrant

`/vagrant`

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

## Desligando as VMS direto pelo VirtualBox

Em caso de pane do vagrant, você pode desligar a VM pelo virtual box

`VBoxManage list runningvms`

`VBoxManage controlvm <name|uuid> savestate`

`VBoxManage controlvm <name|uuid> poweroff`

## Rodando o rails de dentro do vagrant

Além de liberar a porta 3000, você deve iniciar o rails server sem binding.

`config.vm.network "forwarded_port", guest: 3000, host: 3000`

` rails s -b 0.0.0.0`
