# Monit

- https://mmonit.com/monit/
- https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-monit

O Monit serve para monitorar processos e tomar ações em casos de erro.

## Instalação

`apt-get install monit`

## Configuracao

`vim etc/monit/monitrc`

## Habilitando o Status

Edite o arquivo /etc/monit/monitrc e descomente as linhas abaixo

 set httpd port 2812
    use address localhost  # only accept connection from localhost
    allow localhost        # allow localhost to connect to the server and
    allow admin:monit

`sudo monit reload`

`monit status`

## Habilitando acesso externo

set httpd port 2812 and
   use address 162.243.91.163  # only accept connection from localhost IP do droplet
   allow 0.0.0.0/0.0.0.0       # allow localhost to connect to the server and
   allow admin:senhaqualquer      # require user 'admin' with password 'monit'


Se tiver firewall habilitado lembre de liberar a porta 2812

`monit reload`

## Configuracao por projeto

`vim /etc/monit/config.d/tripolist.conf`

## Configuração para monitorar o arquivo restart e reiniciar a aplicacao

Colocar dentro de /etc/monit/config.d/tripolist.conf

check file restart.txt with path /var/www/tripolist/tmp/restart.txt
group tripolist
if changed timestamp then restart

check process tripolist-app
with pidfile /var/run/tripolist/unicorn.pid
group tripolist
start program = "/sbin/start tripolist-app"
stop program = "/sbin/stop tripolist-app"

## Restart do monit

`service monit restart`

## Log do monit

Você pode acompanhar se ele está funcionando através do log

`/var/log/monit.log`
