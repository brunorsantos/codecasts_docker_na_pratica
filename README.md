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

### Imagens e Containers
