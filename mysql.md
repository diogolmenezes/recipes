# Mysql

Para acessar o console do MySQL, basta executar o comando mysql.

```
$ mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.24 Homebrew

Copyright (c) 2000, 2015, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## Habilitando conexões remotas

Primeiro, você precisará configurar o MySQL para que ele ouça as conexões originadas por outros endereços IP que não 127.0.0.1. Abra o arquivo /etc/mysql/my.cnf e comente a linha que contém a configuração bind-address ou configure ela para
bind-address = 0.0.0.0

Reinicie o mysql

`sudo service mysql restart`

Agora, execute o comando abaixo para permitir que o usuário root possa conectar nessa máquina.

`mysql -u root -p -e "CREATE USER 'root'@'%'; GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;"`

## Criando Dumps

`mysqldump -u root -p[root_password] [database_name] > dumpfilename.sql`

## Recuperando Dumps

Essa senha é colada no -p mesmo.

`mysql -u root -psenha tripolist < dumpfilename.sql`

## Dump para Gzip

`mysql -u root -psenha tripolist | gzip -9 > dumpfilename.gz`

## Agendando backup no cron

- https://www.digitalocean.com/community/tutorials/how-to-backup-mysql-databases-on-an-ubuntu-vps

`vi ~/.my.cnf`

Conteudo

`[client]
host=localhost
user=root
password=senha
database=tripolist`

`chmod 600 ~/.my.cnf`
