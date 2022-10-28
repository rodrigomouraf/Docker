# Docker



[TOC]



* [Motivação](#Motivacao)
* [Link para instalação](#install)
* [Docker Hub](#docker-hub)
* [Alguns Conceitos](#alguns-conceitos)
* [Hello World](#hello-world)
* [Imagens](#image)
* [Containers](#containers)
* [Dockerfile](#dockerfile)
* [Persistência de dados](#persistencia_dados)
* [Rede](#rede)
* [Docker Compose](#docker-compose)

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

## <a name='docker-hub'>Docker hub</a>

https://hub.docker.com/

No docker hub, podemos verificar pacotes para serem instalados, podemos salva nele nossas próprias imagens, documentações, fóruns, entre outros.

## <a name='alguns-conceitos'>Alguns conceitos</a>

Containers dentro do Docker funcionam diretamente como processos dentro do nosso sistema. Enquanto uma máquina virtual terá etapas de virtualização dos sistemas operacionais. Por isso containers são mais leves em relação a uma máquina virtual.

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

## <a name='hello-world'>Hello World</a>

```bash
docker run hello-world
```



## <a name='image'>Imagens</a>

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

### Removendo todas as imagens

```bash
docker rmi $(docker image ls -aq)
```



## <a name='containers'>Containers</a>

### Criando um container

```bash
docker run --name <name_container>
```

### Criando um container com mapeamento de portas

```bash
docker run --name <name_container> -d -p 8080:80 <imagem> 
```

No comando acima o docker vai procurar a imagem localmente, não encontrando vai baixar a imagem e depois vai validar o hash e executar o container.
	-d não trava o terminal na execução.
	-p mapea a porta host 8080 refletida na porta 80 do container, podemos usar P e o próprio container vai gerar a porta host.

### Run

Quando queremos executar um container usamos o comando docker run, mas o que esse comando realmente faz:

Quando rodamos um docker run, o docker procura a imagem localmente, caso não exista essa imagem a baixa e valida sua hash e por último executa o container.

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

### Remover todos os containers

```bash
docker container rm $(docker container ls -aq)
```

### Verificar o mapeamento de portas em relação ao host

```bash
docker port <id || nome container>
```

### Copiando um container

```bash
docker tag <repository:tag> <new_name:tag>
```

### Tamanho virtual

Olhando apenas os containers em uso

```bash
docker ps -s
```

Olhando todos os containers

```bash
docker ps -sa
```

Aqui teremos uma coluna chamada SIZE ela retorna tamanho_camada_rwMB(virtual tamanho_da_imagemMB)

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



## <a name='dockerfile'>Dockerfile</a>

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
ENTRYPOINT ["command 1", "command 2"]
```

ADD 1: Faz cópia de um arquivo, diretório ou download de uma URL de nossa máquina host para dentro de uma imagem. Caso o arquivo esteja sendo passado com extensão tar, ele fará a descompressão automaticamente.

COPY 1: Permite a passagem de arquivos ou diretórios.

CMD 2:  Bem parecida com a instrução RUN. Executa apenas o comando quando criamos o container e não passamos parâmetro para ele. Executa apenas o último CMD encontrado. 

ENTRYPOINT 2: Faz exatamente a mesma coisa que CMD mas seus parâmetros não são sobrescritos.

EXPOSE: Serve como documentação para definir qual é a porta que a aplicação está rodando.

FROM:  Obrigatória, define qual será o ponto de partida da imagem.

RUN: Quando executado o comando de docker build . além de baixar a imagem ele também no processo de criação executará os comandos. O docker consegue reutilizar camadas de outra imagem através do uso de cache.

VOLUME: Cria uma pasta em nosso container que será compartilhada entro o container e o host.

WORKDIR: Define o nosso ambiente de trabalho. Com ela, definimos onde as instruções **CMD, RUN, ENTRYPOINT, ADD e COPY** executarão suas tarefas, além de definir o diretório padrão que será aberto ao executarmos o container.

## <a name='persistencia_dados'>Persistência de dados</a>

Por padrão, todos os arquivos criados dentro de um container são armazenados em uma camada gravável. Isso significa que os dados não são persistidos caso esse container deixe de existir, pode ser difícil retirar os dados do container para outros processos. A camada gravável é altamente acoplada ao host em que o container é executado.

Existem duas formas de fazermos persistência de dados no container mesmo após a interrupção do container, podemos utilizar bind mounts ou volumes.

A maneira de persistência recomendada pelo Docker para ser usada em produção é o volume, pois a área de volume é gerenciada pelo Docker dentro do seu file system. Segue link de artigo explicando um pouco mais sobre file system. https://www.freecodecamp.org/news/file-systems-architecture-explained/.

O Docker também oferece suporte a containers que armazenam arquivos na memória na máquina host, ou seja, quando seu container parar seus dados não continuaram persistidos. No linux tmps mount é usado para executar o armazenamento na memória do sistema do host e no Windows o pipe nomeado será usado para armazenar arquivos na memória do sistema host. E pra que usar se ele não persiste, talvez seja legal caso vc esteja trabalhando com dados sensíveis.

Para saber mais olhar a doc: https://docs.docker.com/storage/

### Bound Mounts

Neste momento iremos criar as persistências passando diretamente nosso host.  

```bash
docker run -it -v <dir_seu_host>:<dir_desejado_no_container> <image>
```

Maneira mais recomendada pelo Docker  por ser mais semântico:

```bash
docker run –it --mount type=bind,source=<dir_seu_host>,target=<dir_desejado_no_container> <image>
```

Para saber mais sobre -v ou --mount: https://docs.docker.com/storage/bind-mounts/

### Volumes

#### Verificando volumes dentro da máquina

```bash
docker volume ls
```

#### Criando Volumes

```bash
docker volume create <nome_volume> 
```

#### Fazendo persistência usando volumes

Podemos utilizar o mount para criar esse exemplo:

```bash
docker run -it --mount source=<nome_volume>,target=<dir_desejado_no_container> <image>
```

Obs: Como estamos criando um container com persistência gerenciada pelo Docker através do volume não precisamos passar a flag type=bind.

No exemplo acima caso o volume especificado não existisse o próprio Docker o criaria. 

#### Removendo volumes não utilizados

```bash
docker volume prune
```

###  Tmpfs

```bash
docker run -it --tmpfs=<dir_desejado_no_container> <image>
```

Também podemos usar o mount

```bash
docker run -it --mount type=tmpfs,destination=<dir_desejado_no_container> <image>
```

## <a name='rede'>Rede</a>

Agora que já aprendemos a fazer persistência de dados nos containers, como podemos fazer com que os conteiners se comuniquem entre si?

Vamos nesse momento criar um container para testarmos e entendermos melhor essa parte de rede.

```bash
docker run --name ubuntu ubuntu
```

Agora que temos nosso container criado vamos o analisar melhor através do comando inspect:

```bash
docker inspect ubuntu
```

No final dos detalhes teremos networks, parte de rede, e dentro de nertwork teremos diversas configurações na parte de bridge.

Verificando as redes existentes

```bash
docker network ls
```

### Configurando a comunicação entre containers

Para que haja a comunicação entre nossos containers devemos criar nossa própria rede.

```bash
docker network create --driver bridge minha-bridge
```

### Depois de termos a nossa rede criada vamos criar nossos containers

```bash
docker run -it --name ubuntu --network minha-bridge ubuntu bash 
```

Dentro do terminal do container ubuntu vamos executar o comando

```bash
apt-get update
apt-get install iputils-ping -y
```

Para o outro container vamos o executar em modo detach, ou seja, ele estará executando em segundo plano e definimos um sleep de 1 dia para que ele não finalize a tarefa logo em seguida de subir o container.

```bash
docker run -d --name pong --network minha-bridge ubuntu sleep 1d 
```

Quando o container ubuntu terminar de rodar as instalações que requisitamos nos passos acima, podemos rodar o comando para testar se a comunicação entre os containers estão rodando com sucesso. Para isso execute dentro do bash do container do ubuntu:

```bash
ping pong
```

### Redes none e host

#### None

Quando utilizamos a rede none, ela remove a interface de rede, estamos dizendo que não haverá qualquer tipo de interface de rede vinculada ao container.

#### Host

Utilizando a rede host, estamos removendo o isolamento entre o container e o sistema, ou seja, será utilizado a mesma interface de rede do nosso host para executar nosso container.

## Docker Compose

O Docker Compose resolve problemas de executar múltiplos containers de uma só vez e de maneira coordenada, evitando executar cada comando de execução individualmente.

Com o docker-compose podemos criar compartilhamento entre redes, volumes entre muitos outras funcionalidades, como hot reload para não precisarmos ficar recriando nossas imagens.

### Rodando um docker-compose

```bash
docker-compose up -d
```

### Removendo um docker-compose

```bash
docker-compose down
```

Obs: Caso tenha dependências ou redes criadas para subir esse composer, no docker-compose down ocorre também a remoção dessas dependências.

### Listando as composições

```bash
docker-compose ps
```

### Arquivo docker-compose

```yaml
version: "3.9"
services:
  <name_service>:
    image: <image>
    container_name: <nome-container>
    networks:
      - <nome-para-sua-rede>

  alurabooks:
    image: <image>
    container_name: <nome-container>
    networks:
      - <nome-para-sua-rede>
    ports:
      - <port-container>:<port-host>

networks:
  compose-bridge:
    driver: <tipo-de-network>
```



Observe que caso queiramos definir um compartilhamento de rede entre os containers, devemos dar os mesmos nomes da rede no parâmetro: <nome-para-sua-rede>, e no lugar do <tipo-de-network> colocar o parâmetro bridge.

### Dependência entre container

Quando temos dependências entre containers e queremos que o docker-composer respeite a ordem de montagem do arquivo utilizamos o parâmetro depends_on. Veja abaixo um exemplo do Alura que demonstra a maneira de utilizar:

```yaml
version: "3.9"
services:
  mongodb:
    image: mongo:4.4.6
    container_name: meu-mongo
    networks:
      - compose-bridge

  alurabooks:
    image: aluradocker/alura-books:1.0
    container_name: alurabooks
    networks:
      - compose-bridge
    ports:
      - 3000:3000
    depends_on:
      - mongodb

networks:
  compose-bridge:
    driver: bridge
```

### Hot reload

Exemplo para modificar uma aplicação sem necessidade de recriar uma imagem.

```yaml
version: '3'
services:
    app:
        build: .
        command: npm start
        restart: always
        volumes:
           - .:/usr/src/app
           - nodemodules:/usr/src/app/node_modules

volumes:
    nodemodules: {}
```





 

