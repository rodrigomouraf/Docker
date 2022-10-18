# Docker



[TOC]



* [Motivação](#Motivacao)
* [Link para instalação](#install)
* [Docker Hub](#docker-hub)
* [Alguns Conceitos](#alguns-conceitos)
* [Hello World](#hello-world)
* [Docker Run](#docker-run)
* [Imagens](#image)
* [Containers](#containers)
* [Dockerfile](#dockerfile)

## <a name='motivacao'></a>Motivação

Atualmente temos sistemas que rodam diversas aplicações, para rodar um determinado sistema precisamos ter um banco de dados MySQL, um banco noSQL Redis, usamos Python para automatizações e GoLang para fazer a parte da API, para o front temos um aplicação em React e também algumas outras aplicações que conversam com o sistema principal em .net e outra em Laravel, mas e agora, como rodar isso tudo na minha máquina?

Um dos problemas que também podemos ter é conflitos entre portas, porque o Laravel e os bancos de dados podem estar sendo servidos pela porta 80.

Mas e se eu quiser fazer um upgrade de versão do meu .net, ele estava rodando na versão framework e eu quero atualizar para o .net core.

Como controlar o consumo das minhas aplicações, pois eu quero que meu GoLang utilize 2000 milicores de um servidor com 4000 milicores e 200 MB de memória?

Podemos resolver o problema comprando uma máquina para cada aplicação não é mesmo, mas isso vai consumir muito recurso.

Podemos então utilizar máquinas virtuais, onde teremos nosso hardware bem defindo, nosso sistema operacional e afins. E teremos também uma camada do hypervisor, que faz um meio campo entre virtualizar SO dentro de outro.

Mas será que é preciso fazer isso? Será que é preciso sempre nessas situações virtualizar o SO?

## <a name='install'></a>Link para instalação

https://docs.docker.com/engine/install/

## <a name='docker-hub'></a>Docker hub

https://hub.docker.com/

No docker hub, podemos verificar pacotes para serem instalados, documentações, fóruns, entre outros.

## <a name='alguns-conceitos'></a>Alguns conceitos

Conteriners dentro do Docker funcionam diretamente como processos dentro do nosso sistema. Enquanto uma máquina virtual terá etapas de virtualização dos sistemas operacionais. Por isso containers são mais leves em relação a uma máquina virtual.

### Namespace

Os containers são mantidos isoladamente durante o processo de execução em nosso SO utilizando namespaces, que garantem que cada container tenha capacidade de se isolar em determinados níveis.

### Níveis de isolamento por namespace

#### Cgroups

Garante que consigamos definir tanto de maneira automática como manual como os consumos serão feitos dentro de nosso sistema operacional.

#### IPC

Intercomunicação entre um dos processos da nossa máquina.

#### MNT

File System.

#### NET

Garante o isolamento de rede por intermédio de uma interface de rede, para cada um dos containers.

####  PID

Isolamento ao nível de processo dentro de cada container.

#### UTS

Faz compartilhamento e isolamento, entre o host do nosso Kernel da máquina que está escutando o container.

## <a name='hello-world'></a>Hello World

```bash
docker run hello-world
```

## <a name='docker-run'></a>Docker Run

Quando queremos executar um container usamos o comando docker run, mas o que esse comando realmente faz:

Quando rodamos um docker run, o docker procura a imagem localmente, caso não exista essa imagem a baixa e valida sua hash e por último executa o container.

## <a name='image'></a>Imagens

Imagens são receitas para criar containers, não é nada mais que um conjunto de camadas, as imagens não podem ser alteradas depois que são criadas pois elas são read only. Mas como podemos interagir e criar arquivos dentro quando usamos containers? simples a camada superior de um container tem uma camada adicional de read e write xD.

Agora podemos responder o porque dos containers serem tão leves. Se analisarmos as informações acima, além de eles serem um processo do nosso sistema, ele consegue reaproveitar as camadas das imagens que já temos instaladas, adicionando apenas uma nova camada read/write para utilização, e o tamanho dessa nova camada vai ser proporcional a imagem de escrita.

### Analisando imagens

```bash
docker inspect <id || nome imagem>
```

### Verificando as camadas da nossa imagem

```bash
docker history <id || nome imagem>
```

### Instalando uma imagem localmente

```bash
docker pull <image>
```

### Listando as imagens

```bash
docker image ls
```

### Removendo imagens

```bash
docker rmi <nome || id da imagem>
```

Obs: para remover uma imagem, a mesma não pode estar sendo utilizada por um container.

## <a name='containers'></a>Containers

### Criando um container com mapeamento de portas

```bash
docker run -d -p 8080:80 <imagem> 
```

No comando acima o docker vai procurar a imagem localmente, não encontrando vai baixar a imagem e depois vai validar o hash e executar o container.
	-d não trava o terminal na execução.
	-p mapea a porta host 8080 refletida na porta 80 do container, podemos usar P e o próprio container vai gerar a porta host.

### Listar todos os containers

```bash
docker ps -a || docker container ls -a
```

### Listar container que estão em execução

```bash
docker ps || docker container ls
```

### Parando um container

```bash
docker stop <id || nome container>
```

### Parando todos os containers em execução

```bash
docker stop $(docker container ls -q)
```

### Executando um container já existente

```bash
docker start <id || nome container>
```

### Remover o container 

```bash
docker rm <id || nome container>
```

remove o container mesmo em execução:

```bash
docker rm -f <id || nome container> 
```

### Verificar o mapeamento de portas em relação ao host
```bash
docker port <id || nome container>
```

### Copiando um container

```bash
docker tag <repository:tag> <new_name:tag>
```

### Modo Iterativo

para executar o docker em modo iterativo:

```bash
docker exec -it <imagem> bash
```

Nesse momento aparecerá a linha de comando. Essa linha de comando do terminal esta localizada dentro do container, ou seja, vc está dentro do próprio container da aplicação.

Ex: Digamos que queremos criar um banco postgres e queremos analisar os database que ele contém.

Criando um container:
```bash
docker run --name pg -e POSTGRES_USER=root -e POSTGRES_PASSWORD=toor -p 5432:5432 -d postgres
```


Obs: O parâmetro -d é para o docker rodar em background não travando o terminal.

```bash
docker exec -it pg bash
```

Obs: i é do modo iterativo e o t é de tty, terminal padrão desse container.

Acessando o container pg baseado na imagem postgres

```bash
psql -U root
```

Listando os databases

```bash
\l;
```

Entrando em algum database requerido

```bash
\c name_database;
```

Listando as tables do database

```bash
\dt;
```

## <a name='dockerfile'></a>Dockerfile

Abaixo segue um resumo do artigo do Alura: https://www.alura.com.br/artigos/desvendando-o-dockerfile

### Criando uma imagem através de um Dockerfile

```bash
docker build -t nome_aplicacao .
```

### Algumas instruções Dockerfile

```dockerfile
FROM <image>         ->  Obrigatória -> Define qual será o ponto de partida da imagem
WORKDIR /verificar   ->
ARG PORT_BUILD=6000
ENV PORT=$PORT_BUILD
EXPOSE $PORT_BUILD
COPY . .
RUN ["command 1", "command 2", "command 3"] 
RUN ["command 4"]
ENTRYPOINT verificar
```

ADD 1: Faz cópia de um arquivo, diretório ou download de uma URL de nossa máquina host para dentro de uma imagem. Caso o arquivo esteja sendo passado com extensão tar, ele fará a descompressão automaticamente.

COPY 1: Permite a passagem de arquivos ou diretórios.

CMD 2:  Bem parecida com a intrução RUN. Executa apenas o comando quando criamo o container e não passamos parâmetro para ele. Executa apenas o último CMD encontrado. 

ENTRYPOINT 2: Faz exatamente a mesma coisa que CMD mas seus parâmetros não são sobrescritos.

EXPOSE: Serve como documentação para definir qual é a porta que a aplicação está rodando.

FROM:  Obrigatória, define qual será o ponto de partida da imagem.

RUN: Quando executado o commando de docker build . além de baixar a imagem ele também no processo de criação executará os comandos. O docker consegue reutilizar camadas de outra imagem através do uso de cache.

VOLUME: Cria uma pasta em nosso container que será compartilhada entro o container e o host.

WORKDIR: Define o nosso ambiente de trabalho. Com ela, definimos onde as instruções **CMD, RUN, ENTRYPOINT, ADD e COPY** executarão suas tarefas, além de definir o diretório padrão que será aberto ao executarmos o container.



