# codecasts_docker_na_pratica

Repositório com resumo do curso de docker do codecast

### Extra1 (Funcionamento no windows)

Deve ser habilitato o hyper-v para funcionamento no windows.
[Link](https://www.lambda3.com.br/2013/01/fazendo-virtualbox-e-hyper-v-coexistirem-no-windows-8/
) para deixar windows alternando com e sem o hyper-v na inicialização

### Extra2 (Funcionamento no git bash)

Para executar o um container de forma interativa no gitbash: [Link](https://www.lambda3.com.br/2013/01/fazendo-virtualbox-e-hyper-v-coexistirem-no-windows-8/
)
Alem disto os caminhos devem ser utilizados como barras duplas.
```sh
winpty docker run -it --rm debian:jessie //bin/bash
```

## Olá Docker

Executando o primeiro container

```sh
$ docker run -it --rm debian:jessie /bin/bash
```
docker run - Executa um container
-it - Executa de forma iterativa(Vamos precisar inputar na execução)
/bin/bash - È o processo a ser executado dentro do container(neste caso o terminal)
debian:jessie - Nome da imagem de tag da imagem
--rm - No fim da execucao o docker remove o container

O comando baixará a imagem caso a mesma já não esteja baixada.

O comando abaixo (fora da container claro), mostrará os containers em execução.

```sh
$ docker ps
```

Para fechar o container

```sh
$ exit
```

## Imagens e Containers

No [hub](https://hub.docker.com/explore) do docker são exibidos a lista de imagens publicadas.

Para listar as imagens disponiveis localmente
```sh
$ docker images
```

Caso queria baixar a imagem sem executar o container pode ser usar docker pull

```sh
$ docker pull ghost:latest
```

Para acessar via porta, é necessário mapear como o parametro -p na execucao do container. ([Porta do computador]:[Porta do container])

```sh
$ docker run ghost -it --rm -p 2368:2368
```

## Criando Nossa Primeira Imagem

Pode se criar uma imagem a partir de um estado de um container.
Por exemplo, pode ser executar um container baseado em uma imagem do ubuntu, instalar alguma aplicação e depois criar uma outra imagem a partir dele.
Para isto basta executar:

```sh
$ docker commit [id] codecasts/ffmpeg
```
Será criado localmente uma imagem com o nome codecasts/ffmpeg

Para compartilhar diretórios com o container (montar volumes) deve ser passar o parametro -v com [diretorio local]:[diretorio do container]

```sh
$ docker run codecasts/ffmpeg -it --rm -v /home//hernanidev/videos/edited:/videos /bin/bash
```

## Entendendo o Dockerfile - Parte 1

Dockerfile é um arquivo de definicao para construção de uma imagem.
Essa definicao é feita por passos.

Com o aquivo Dockerfile criado deve se executar docker build para se criar a imagem.
```sh
$ docker build -t codecasts/webserver .
```
Considerando o '.' como o contexo (local do arquivo Dockerfile)

Exemplo de arquivo Dockerfile

```Dockerfile
FROM ubuntu 16.4

MAINTAINER Nome do Mantenedor <email@gmail.com>

RUN apt-get update -y
RUN apt-get install -y apache2 php libapache2-mod-php
```

Desta forma podemos executar o container a partir da imagem criada com(executando o bash):

```sh
$ docker run -it codecasts/webserver /bin/bash
```

## Entendendo o Dockerfile - Parte 2

Ao inciar a execução do container é necessario executar comandos para iniciar o apache. Para isto criamos um arquivo shell script para ser executado.

Arquivo start.sh
```sh
#!/bin/bash
source /etc/apache2/envvars

apache2 -D FORGROUND -f /etc/apache2/apache2.conf
```

No arquivo Dockerfile, com 'ADD', incluímos o arquivo no container

```Dockerfile
FROM ubuntu 16.4

MAINTAINER Nome do Mantenedor <email@gmail.com>

RUN apt-get update -y
RUN apt-get install -y apache2 php libapache2-mod-php

ADD start.sh /root/start.sh

RUN chmod 777 /root/start.sh

EXPOSE 80

CMD ["/root/start.sh"]
```

O comando "CMD" executa o comando após o inicio da execução do container (assim como o comando passado como parametro no docker run)

O comando expose é utilizado para expor a porta(utlizado para orquestração de containers)

Assim podemos executar o container baseado em essa imagem com:

```sh
$ docker run -it codecasts/webserver -p 80:8080
```

Tendo a imagem criada como base, podemos tambem  criar outra imagem via Dockerfile usando ela:

```Dockerfile
FROM codecasts/webserver

MAINTAINER Nome do Mantenedor <email@gmail.com>

ADD . /var/www/html
```

Copiando por exemplo um arquivo php como phpinfo() para o diretorio root do apache.

## Entendendo o Dockerfile - Parte 3

Boas praticas ao utlizar filas de comandos e apt-get no Dockerfile, são enfileirar os comandos assim como limpar o cache e listas do apt-get:


```Dockerfile
FROM ubuntu 16.4

MAINTAINER Nome do Mantenedor <email@gmail.com>

RUN apt-get update -y && \
    apt-get install -y apache2 php libapache2-mod-php && \
    apt-get clean
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ADD start.sh /root/start.sh

RUN chmod 777 /root/start.sh

EXPOSE 80

CMD ["/root/start.sh"]
```


Sendo '&&' para enfileirar comandos e \ para quebra de linha 
