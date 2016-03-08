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

## Provisionamento

É possivel criar um script com tudo que deve ser instalado na maquina, assim vc pode versionar seu vagrant file para poder levantar uma maquina sempre igual no vagrant up

VagrantFile

`Vagrant.configure(2) do |config|  
  config.vm.box = "ubuntu/trusty32"
  config.vm.provision :shell, path: "vagrant-bootstrap.sh"
  # resolve o problema de ficar parado esperando o network driver https://github.com/mitchellh/vagrant/issues/3860
  config.vm.provider "virtualbox" do |vb|    
    ### Change network card to PCnet-FAST III
    vb.customize ["modifyvm", :id, "--nictype1", "Am79C973"]
    vb.customize ["modifyvm", :id, "--nictype2", "Am79C973"]
  end
end`

Cria esse arquivo vagrant-bootstrap.sh no mesmo diretorio que o vagrantfile contendo seu script de instalação das coisas.

```#! /usr/bin/env bash
echo "Iniciando o provisionamento da maquina..."
echo 'deb [arch=amd64] http://apt.hellobits.com/ trusty main' | sudo tee /etc/apt/sources.list.d/hellobits.list > /dev/null 2>&1
wget -q -O - http://apt.hellobits.com/hellobits.key | sudo apt-key add - > /dev/null 2>&1
sudo apt-get -qq update
echo "Instalando o ruby, essentials e git..."
sudo apt-get -y remove ruby > /dev/null 2>&1
sudo apt-get -y install build-essential libgmp3-dev ruby-2.3 git > /dev/null 2>&1
echo "Instalando o mysql..."
echo "mysql-server mysql-server/root_password password 123456" | debconf-set-selections
echo "mysql-server mysql-server/root_password_again password 123456" | debconf-set-selections
sudo apt-get -y install mysql-server-5.5 mysql-client libmysqlclient-dev > /dev/null 2>&1
echo "Instalando o rails..."
gem install --no-rdoc --no-ri rails
echo "Provisionamento concluido com sucesso!"```

Vc pode recarragar o provisionamento com 

`vagrant reload --provision`


https://www.vagrantup.com/docs/provisioning/
https://www.vagrantup.com/docs/provisioning/shell.html
https://gist.github.com/rrosiek/8190550

## Rodando maquinas 64bits no windows

Talvez vc precise atuvar o Vt-x da virtualização

basta desativar o hyper v

abrir o cmd como administrador e rodar: dism.exe /Online /Disable-Feature:Microsoft-Hyper-V

depois reiniciar

http://druss.co/2015/06/fix-vt-x-is-not-available-verr_vmx_no_vmx-in-virtualbox/