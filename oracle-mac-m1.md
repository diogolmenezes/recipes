1 - Baixar https://download.oracle.com/otn_software/mac/instantclient/198000/instantclient-basic-macos.x64-19.8.0.0.0dbru.zip

2 - mkdir ~/InstantClient

3 - Extrair a pasta do instantclient que vc baixou e mover todo o conteudo dela pra ~/InstantClient

4 - Entrar na pasta ~/InstantClient e criar um link simbolico sudo ln -s ~/InstantClient/libclntsh.dylib /usr/local/lib

5 - Instalar o Node

6 - Instalar o N com sudo npm i -g n

5 - arch -x86_64 sudo n 16

6 - node -p process.arch