# LogWatch

Manda um relatorio diario do servidor

## Instalação

`apt-get install logwatch`

##  Configuração

`vim /etc/cron.daily/00logwatch`

Configurar o e-mail de quem receberá o relatório

`/usr/sbin/logwatch --output mail --mailto diogolmenezes@gmail.com --detail high`

