# **Exercícios Docker**

> [!IMPORTANT]
> Nunca deixar na versão `latest`, por ter aplicações que só funcionaram em uma versão específica, e como o `latest` sempre busca a última versão disponível, pode causar uma Quebra de compatibilidade (se você colocar somente o nome da distribuição sem tag, irá buscar a última versão disponível também).

> [!NOTE]
> Estudar como utilizar um usuário não root em um container.

### 🟢 **Fácil**

1. **Rodando um container básico**
    - Execute um container usando a imagem do **Nginx** e acesse a página padrão no navegador.
    - 🔹 _Exemplo de aplicação:_ Use a [landing page do TailwindCSS](https://github.com/tailwindtoolbox/Landing-Page "https://github.com/tailwindtoolbox/landing-page") como site estático dentro do container.

flavor:
- Documentação: https://hub.docker.com/_/nginx
- docker pull nginx:1.27
- Criar um container com a imagem e acessar a porta no navegador.
- docker run --name nome-container -d -p 8080:80 nome-imagem


![a](png/148)

![b](png/345)

2. **Criando e rodando um container interativo**
    - Inicie um container **Ubuntu** e interaja com o terminal dele.
    - 🔹 _Exemplo de aplicação:_ Teste um script Bash que imprime logs do sistema ou instala pacotes de forma interativa.

flavor:
- documentação: https://hub.docker.com/_/ubuntu
- docker pull ubuntu:noble
- docker run -dti --name novo_nome-container nome_da_imagem
- docker exec -ti nome_do_caontainer bash

![c](png/2250)

- Dentro do container atualizar a maquina (apt update && apt upgrade)
- Baixar o nano (apt install nano)

nano exec.sh:

```
#!/bin/bash

apt update
apt upgrade -y
apt autoremove -y

echo "Atualização concluída!"
```

- Dar permissão de execução para o .sh (chmod +x nomedo.sh)
- Executar ele: ./nomedo.sh

![d](png/3316)

3. **Listando e removendo containers**
    - Liste todos os containers em execução e parados, pare um container em execução e remova um container específico.
    - 🔹 _Exemplo de aplicação:_ Gerenciar containers de testes criados para verificar configurações ou dependências.

flavor:
- docker ps -a (lista os containers parados e os em execução)
- docker stop nome-do-container
- docker rm nome-do-container

![e](png/634)


4. **Criando um Dockerfile para uma aplicação simples em Python**
    - Crie um `Dockerfile` para uma aplicação **Flask** que retorna uma mensagem ao acessar um endpoint.
    - 🔹 _Exemplo de aplicação:_ Use a API de exemplo [Flask Restful API Starter](https://github.com/gothinkster/flask-realworld-example-app "https://github.com/gothinkster/flask-realworld-example-app") para criar um endpoint de teste.

flavor:
- Primeiro criei uma pasta para separar os conteúdos das imagens (images)
- Criei uma pasta que conterá o Dockerfile (ubuntu-python)

arquivo do Dockerfile:

```
FROM ubuntu:noble

RUN apt update && apt upgrade -y && apt install -y python3 && apt install nano -y && apt clean

COPY app.py /opt/app.py

CMD python3 /opt/app.py
```

- Dentro da mesma pasta criei o arquivo app.py

nano app.py:

```
nome = input("Qual é o seu nome: ")
print(nome)
```

- Criei a imagem: **docker build . -t nome-da-imagem** (o . seria o caminho do dockerfile, porém como estou dentro da pasta isso não é necessário)

![f](png/932)

- docker run -dti --name nome-container nome-imagem
- docker exec -ti nome-container python3 /opt/app.py

![g](png/5303)
---

### 🟡 **Médio**

5. **Criando e utilizando volumes para persistência de dados**
    - Execute um container **MySQL** e configure um volume para armazenar os dados do banco de forma persistente.
    - 🔹 _Exemplo de aplicação:_ Use o sistema de login e cadastro do [Laravel Breeze](https://github.com/laravel/breeze "https://github.com/laravel/breeze"), que usa MySQL.

flavor:
Documentação mysql: https://hub.docker.com/_/mysql
Primeiro criar um volume para guardar as informações do mysql:
- docker volume create nome-do-volume
- docker volume ls (vai listar os volumes)
- docker run -e MYSQL_ROOT_PASSWORD=0311 -dti -p 3306-3306 --name nome-container --mount type=volume,src=mysql,dst=/var/lib/mysql mysql:9.2

```
- -e MYSQL_ROOT_PASSWORD=0311 > você ta indicando a senha
- --name mysql1  > voce está dando um nome ao container
- -d  > falando que vai funcionar background
- -p 3306-3306  > está especificando a porta de entrada e saida do container
-  type=volume,src=mysql,dst=/var/lib/mysql mysql > está passando o volume do so e logo após a pasta que está os arquivos do mysql.
- mysql:9.2 > passando o nome da imagem do container que será utilizada
```

Entrar dentro do container_1 utilizando o bash (mysql1):
- docker exec -ti nome_do_caontainer bash

Entrar dentro do mysql:
-  mysql -u root -p --protocol=tcp --port=3306

![h](png/1.png)

comandos mysql utilizados:

```
show databases;
-- cria a database fazenda
create database fazenda;
-- especifica a database que será utilizada
use fazenda;

-- cria a tabela horta
CREATE TABLE horta (id INTEGER PRIMARY KEY,name TEXT NOT NULL,qtd INTEGER NOT NULL);

-- preenche os valores na tabela horta
INSERT INTO horta VALUES (1, 'Batata', 5);
INSERT INTO horta VALUES (2, 'Banana', 12);
```

![i](png/2.png)

Agora dentro de uma segunda vm com o mysql (vulgo mysql2)

![j](png/3.png)

Comandos utilizados no segundo container (mysql2)

```
show databases;
-- Mostra os valores dos alimentos com quantidade maior que 5
SELECT * FROM horta WHERE qtd >= 5;
```

![k](png/4.png)
a
6. **Criando e rodando um container multi-stage**
    - Utilize um **multi-stage build** para otimizar uma aplicação **Go**, reduzindo o tamanho da imagem final.
    - 🔹 _Exemplo de aplicação:_ Compile e rode a API do [Go Fiber Example](https://github.com/gofiber/recipes/tree/main/docker-multistage-build "https://github.com/gofiber/recipes/tree/main/docker-multistage-build") dentro do container.

flavor:
Entrar no docker hub e baixar a imagem do golang e uma versão minima do linux(alpine)
- Documentação golang: https://hub.docker.com/_/golang
- Documentação alpine: https://hub.docker.com/_/alpine

 Criar uma pasta para um container multi-stage utilizando go & alpine
- cd images 
- mkdir go
- cd go

No SO:
- docker pull golang:1.24
- docker pull alpine:3.21

nano app.go
```
package main
import(
  "fmt"
)
func main(){
fmt.Println("Qual é o seun nome: ?")
var name string
fmt.Scanln(&name)
fmt.Printf("Oi, %s! Eu sou a linguagem Go", name)
}
```

nano dockerfile:
```
FROM golang:1.24 as exec

COPY app.go /go/src/app/

ENV GO111MODULE=auto

WORKDIR /go/src/app/

RUN go build -o app.go .

FROM alpine:3.21

WORKDIR /appexec

COPY --from=exec /go/src/app /appexec

RUN chmod -R 755 /appexec

ENTRYPOINT ./app.go
```

Criar a imagem baseado no dockerfile:
- docker image build -t nome-imagem .
![l](png/5.png)

Iniciar o container:
- docker run -ti --name nome-container nome-imagem
![m](png/6.png)

7. **Construindo uma rede Docker para comunicação entre containers**
    - Crie uma rede Docker personalizada e faça dois containers, um **Node.js** e um **MongoDB**, se comunicarem.
    - 🔹 _Exemplo de aplicação:_ Utilize o projeto [MEAN Todos](https://github.com/luanphandinh/mean-todo "https://github.com/luanphandinh/mean-todo") para criar um app de tarefas usando Node.js + MongoDB.

flavor:

Criar uma rede:
- docker network create nome_da_rede
- docker network ls  (lista as redes disponiveis)

Baixar as imagens node.js e mongodb:
- Documentação: https://hub.docker.com/_/node
- Documentação: https://hub.docker.com/r/mongodb/mongodb-community-server

- docker pull node
- docker pull mongodb/mongodb-community-server

![n](png/7.png)

Ao criar os containers especificar a rede que irá utilizar:
- docker run -dti --name nod --network nodmon node
- docker run --name mon -d -p 27017:27017 --network nodmon mongodb/mongodb-community-server:$MONGODB_VERSION
- docker run -dti --name nome-container --network nome_da_rede nome-imagem

![o](png/8.png)

- docker network inspect nomerede (mostra quais containers estão na rede especifica)
![p](png/9.png)

Entrar no container:
- docker exec -ti nome_do_caontainer bash

Dentro dos dois containers:
```
apt update
apt get-install -y iputils-ping
```

Por fim é só pingar e ver o resultado:

container com o node.js ip: 172.18.0.2
container com o mongodb ip:  172.18.0.3

![q](png/10.png)

8. **Criando um compose file para rodar uma aplicação com banco de dados**
    - Utilize **Docker Compose** para configurar uma aplicação **Django** com um banco de dados **PostgreSQL**.
    - 🔹 _Exemplo de aplicação:_ Use o projeto [Django Polls App](https://github.com/databases-io/django-polls "https://github.com/databases-io/django-polls") para criar uma pesquisa de opinião integrada ao banco.

flavor:
- Criar um volume para guardar os dados do banco (docker volume create nome-volume)
- Criar uma pasta para guardar futuros `compose` (compose)
- Criar uma pasta especifica para o desafio 8 (exe8)
- Criar um arquivo `docker-compose.yml`

nano `docker-compose.yml`: 
```
services:
  post:
    image: postgres:14.17
    environment:
      POSTGRES_PASSWORD: "Senha123"
      POSTGRES_DB: "fazenda"
    ports:
      - "5432:5432"
    volumes:
      - postgresql:/var/lib/postgresql/data
    networks:
      - pesca

  django:
    image: django:onbuild
    ports:
      - 8000:8000
    networks:
      - pesca

networks:
  pesca:
    driver: bridge

volumes:
  postgresql:
```

Agora fora do nano e dentro da pasta do nosso arquivo docker-compose

- docker-compose up -d

Para apagar eles:
- docker-compose down

![r](png/88.png)

---

### 🔴 **Difícil**

9. **Criando uma imagem personalizada com um servidor web e arquivos estáticos**
    - Construa uma imagem baseada no **Nginx** ou **Apache**, adicionando um site HTML/CSS estático.
    - 🔹 _Exemplo de aplicação:_ Utilize a [landing page do Creative Tim](https://github.com/creativetimofficial/material-kit "https://github.com/creativetimofficial/material-kit") para criar uma página moderna hospedada no container.


Documentação debian: https://hub.docker.com/_/debian
Documentação apache: https://hub.docker.com/_/httpd

- docker pull httpd:2.4
- docker pull debian:12

No SO:
sudo apt install wget (para colocarmos o arquivo do site em um .tar)

Criar uma pasta que contenha a criação da imagem:
- cd /images
- mkdir debian-apache
- cd /debian-apache
- mkdir site
- cd site

Dentro da pasta site:
- Criar um index.html básico
- Criar um style.css básico

Index.html:
```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Meu Site Simples</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <header>
        <h1>Bem-vindo ao Meu Site Simples</h1>
    </header>
    <main>
        <p>Este é um exemplo de uma página HTML/CSS simples.</p>
    </main>
    <footer>
        <p>&copy; 2023 Meu Site Simples</p>
    </footer>
</body>
</html>
```

Style.css:
```
body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f4f4f4;
    color: #333;
}

header {
    background-color: #333;
    color: #fff;
    padding: 20px;
    text-align: center;
}

main {
    padding: 20px;
    text-align: center;
}

footer {
    background-color: #333;
    color: #fff;
    text-align: center;
    padding: 10px;
    position: fixed;
    bottom: 0;
    width: 100%;
}
```


- tar -czf site.tar ./
- cp site.tar ../
- rm --Rf site

![zz](png/11.png)

Dentro da pasta debian-apache:
- criar um dockerfile

dockerfile:
```
FROM debian:12

RUN apt-get update && apt-get install -y apache2 && apt-get clean

ENV APACHE_LOCK_DIR="var/lock"
ENV APACHE_PID_FILE="var/run/apache2.pid"
ENV APACHE_RUN_USER="www-data"
ENV APACHE_RUN_GROUP="www-data"
ENV APACHE_LOG_DIR="/var/log/apache2"

ADD site.tar /var/www/html

LABEL description = "Apache webserver 1.0"

VOLUME /var/www/html

EXPOSE 80

ENTRYPOINT ["/usr/sbin/apachectl"]

CMD ["-D","FOREGROUND"]
```

Criar a imagem:
- docker image build -t nome-imagem .

Executar o container e ser feliz:
- docker run -dti -p 80:80 --name nome-container nome-imagem

![s](png/final.png)
