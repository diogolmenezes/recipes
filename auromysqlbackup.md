# Backup de MySql com automysqlbackup

- https://www.digitalocean.com/community/tutorials/how-to-backup-mysql-databases-on-an-ubuntu-vps

# Instalação

`sudo apt-get install automysqlbackup`


# Rodando

`sudo automysqlbackup`

# Configuração

`sudo vim /etc/default/automysqlbackup`


# Backups

Por padrao pega os dados de login de /etc/mysql/debian.cnf

O local padrao dos backups é

`ls /var/lib/automysqlbackup`

O Ubuntu instala uma tarefa cron para que esse backup rode todo dia.
