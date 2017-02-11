# SSH

## Criando a chave no windows

`ssh-keygen`

A chave deve ficar em C:\Users\Diogo\.ssh

Adicione no bitbucket a publica e para testar 

`ssh -T git@bitbucket.org`

## Criando a chave no linux

`ssh-keygen -t rsa -b 4096 -C "diogolmenezes@gmail.com"`


# SCP para copiar arquivos

`scp <file to upload> <username>@<hostname>:<destination path>`