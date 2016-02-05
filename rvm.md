# RVM

## Instalação

`gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
\curl -sSL https://get.rvm.io | bash -s stable`

## Instalando RUby

`rvm install 2.3.0`

## Listando

`rvm list`

## criando gemset

`rvm gemset create gemset_name    # create a gemset`
`rvm ruby_version@gemset_name  # specify Ruby version and our new gemset`
`gem install rails -v rails_version   # install specific Rails version

## Desistalando

`rvm implode`

`rm -rf ~/.rvm`

`rm -rf /etc/rvmrc`

`rm -rf ~/rvmrc`

## Usando ruby do sistema

` rvm system`

## usando gemset

`rvm use 2.3.0@tripolist`

## criando alias para usar RVM com outros usuarios alem do root

- https://rvm.io/deployment/init-d

Se você esta usando RVM tem que criar um wrapper para que o usuario www-data consiga enxergar o bundler e o RVM

`rvm alias create tripolist ruby-2.3.0@tripolist

E depois usar os comandos disponiveis no path

`ls /usr/local/rvm/wrappers/tripolist`

como

`/usr/local/rvm/wrappers/tripolist/bundle exec ...`




