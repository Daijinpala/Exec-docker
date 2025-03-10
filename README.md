# **Exercícios Docker**

### 🟢 **Fácil**

1. **Rodando um container básico**
    - Execute um container usando a imagem do **Nginx** e acesse a página padrão no navegador.
    - 🔹 _Exemplo de aplicação:_ Use a [landing page do TailwindCSS](https://github.com/tailwindtoolbox/Landing-Page) como site estático dentro do container.

    **Flavor:**
    - Documentação: [Nginx Docker Hub](https://hub.docker.com/_/nginx)
    
    - Comandos:
      ```bash
      docker pull nginx
      docker run --name nome-container -d -p 8080:80 nome-imagem
      ```
      
    - Acesse `http://localhost:8080` no navegador.

    ![facil_1.png](png/Pasted%20image%2020250310131148.png)
    ![facil_1.1.png](png/Pasted%20image%2020250310131345.png)

2. **Criando e rodando um container interativo**
    - Inicie um container **Ubuntu** e interaja com o terminal dele.
    - 🔹 _Exemplo de aplicação:_ Teste um script Bash que imprime logs do sistema ou instala pacotes de forma interativa.

    **Flavor:**
    - Documentação: [Ubuntu Docker Hub](https://hub.docker.com/_/ubuntu)
    - Comandos:
      ```bash
      docker pull ubuntu
      docker run -dti --name meu-container nome_da_imagem
      docker exec -ti nome_do_container bash
      ```
    - Dentro do container:
      ```bash
      apt update && apt upgrade -y
      apt install nano -y
      ```

    **Script `exec.sh`:**
    ```bash
    #!/bin/bash

    apt update
    apt upgrade -y
    apt autoremove -y

    echo "Atualização concluída!"
    ```
    - Permissão de execução:
      ```bash
      chmod +x exec.sh
      ./exec.sh
      ```

    ![facil_2.png](png/Pasted%20image%2020250310132250.png)
    ![facil_2.1.png](png/Pasted%20image%2020250310133316.png)

3. **Listando e removendo containers**
    - Liste todos os containers em execução e parados, pare um container em execução e remova um container específico.
    - 🔹 _Exemplo de aplicação:_ Gerenciar containers de testes criados para verificar configurações ou dependências.

    **Flavor:**
    - Comandos:
      ```bash
      docker ps -a
      docker stop nome-do-container
      docker rm nome-do-container
      ```

    ![facil_3.png](png/Pasted%20image%2020250310133634.png)

4. **Criando um Dockerfile para uma aplicação simples em Python**
    - Crie um `Dockerfile` para uma aplicação **Flask** que retorna uma mensagem ao acessar um endpoint.
    - 🔹 _Exemplo de aplicação:_ Use a API de exemplo [Flask Restful API Starter](https://github.com/gothinkster/flask-realworld-example-app) para criar um endpoint de teste.

- Primeiro criei uma pasta para separar os conteúdos das imagens (images)
- Criei uma pasta que conterá o Dockerfile (ubuntu-python)
- Dentro da mesma pasta criei o arquivo app.py

    **Dockerfile:**
    ```dockerfile
    FROM ubuntu

    RUN apt update && apt upgrade -y && apt install -y python3 && apt install nano -y && apt clean

    COPY app.py /opt/app.py

    CMD python3 /opt/app.py
    ```

    **Script `app.py`:**
    ```python
    nome = input("Qual é o seu nome: ")
    print(nome)
    ```

    **Comandos:**
    ```bash
    docker build . -t minha-imagem-python
    docker run -dti --name meu-container-python minha-imagem-python
    docker exec -ti meu-container-python python3 /opt/app.py
    ```

    ![facil_4.png](png/Pasted%20image%2020250310144932.png)
    ![facil_4.1.png](png/Pasted%20image%2020250310145303.png)

---

### 🟡 **Médio**

5. **Criando e utilizando volumes para persistência de dados**
    - Execute um container **MySQL** e configure um volume para armazenar os dados do banco de forma persistente.
    - 🔹 _Exemplo de aplicação:_ Use o sistema de login e cadastro do [Laravel Breeze](https://github.com/laravel/breeze), que usa MySQL.

Primeiro criar um volume para guardar as informações do mysql:
 - docker volume create nome-do-volume
 - docker volume ls (vai listar os volumes)
 - docker run -e MYSQL_ROOT_PASSWORD=0311 -dti -p 3306-3306 --name nome-container --mount type=volume,src=mysql,dst=/var/lib/mysql mysql

     **Explicação `docker run`:**
    ```bash
     -e MYSQL_ROOT_PASSWORD=0311 > você ta indicando a senha
    --name mysql1  > voce está dando um nome ao container
    -d  > falando que vai funcionar background
    -p 3306-3306  > está especificando a porta de entrada e saida do container
    type=volume,src=mysql,dst=/var/lib/mysql mysql > está passando o volume do so e logo após a pasta que está os arquivos do mysql.
    mysql > passando o nome da imagem do container que será utilizada
    ```

Entrar dentro do container_1 utilizando o bash (mysql1):
- docker exec -ti nome_do_caontainer bash

Entrar dentro do mysql:
-  mysql -u root -p --protocol=tcp --port=3306

[medio_1.png](png/1.png)

**comandos `mysql` utilizados:**

    ```mysql
    - mostra todas as databases
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

[medio_1.2.png](png/2.png)

Agora dentro de uma segunda vm com o mysql (vulgo mysql2)

[medio_1.3.png](png/3.png)

**Comandos utilizados no segundo container (mysql2):**
```
show databases;
-- Mostra os valores dos alimentos com quantidade maior que 5
SELECT * FROM horta WHERE qtd >= 5;
```

[medio_1.4.png](png/4.png)

6. **Criando e rodando um container multi-stage**
    - Utilize um **multi-stage build** para otimizar uma aplicação **Go**, reduzindo o tamanho da imagem final.
    - 🔹 _Exemplo de aplicação:_ Compile e rode a API do [Go Fiber Example](https://github.com/gofiber/recipes/tree/main/docker-multistage-build) dentro do container.

7. **Construindo uma rede Docker para comunicação entre containers**
    - Crie uma rede Docker personalizada e faça dois containers, um **Node.js** e um **MongoDB**, se comunicarem.
    - 🔹 _Exemplo de aplicação:_ Utilize o projeto [MEAN Todos](https://github.com/luanphandinh/mean-todo) para criar um app de tarefas usando Node.js + MongoDB.

8. **Criando um compose file para rodar uma aplicação com banco de dados**
    - Utilize **Docker Compose** para configurar uma aplicação **Django** com um banco de dados **PostgreSQL**.
    - 🔹 _Exemplo de aplicação:_ Use o projeto [Django Polls App](https://github.com/databases-io/django-polls) para criar uma pesquisa de opinião integrada ao banco.

---

### 🔴 **Difícil**

9. **Criando uma imagem personalizada com um servidor web e arquivos estáticos**
    - Construa uma imagem baseada no **Nginx** ou **Apache**, adicionando um site HTML/CSS estático.
    - 🔹 _Exemplo de aplicação:_ Utilize a [landing page do Creative Tim](https://github.com/creativetimofficial/material-kit) para criar uma página moderna hospedada no container.
