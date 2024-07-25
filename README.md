
# Utilizando o Oracle em qualquer distro Linux via Docker


## Autores

- [@iagomauricioo](https://github.com/iagomauricioo)


## Instalação

Instale o docker na sua máquina

```bash
  sudo pacman -S docker
```

Instale o docker-compose

```bash
  sudo pacman -S docker-compose
```

Instale o DBeaver
```bash
  sudo pacman -S dbeaver
```


## Docker

Crie um arquivo com o nome `docker-compose.yml`


## Variáveis de Ambiente

Para rodar esse projeto, você vai precisar adicionar as seguintes variáveis de ambiente no seu `.env` ou na aba `environment` dentro do arquivo `docker-compose.yml`, eu estarei utilizando a segunda opção para um melhor entendimento.

`ORACLE_PASSWORD` (sua senha para o usuário system)

`APP_USER` (seu usuário)

`APP_USER_PASSWORD` (a senha para o seu usuário)


## Uso/Exemplos

Copie esse código abaixo e cole no seu arquivo `docker-compose.yml`

```yml
services:
  oracle-xe:
    image: gvenzl/oracle-xe:21-slim
    container_name: oracle-xe
    ports:
      - 1521:1521
      - 5500:5500
    environment:
      - ORACLE_PASSWORD=passwordforsystem
      - APP_USER=youruser
      - APP_USER_PASSWORD=yourpassword
    volumes:
      - oracle-data:/opt/oracle/oradata
    healthcheck:
      test: ["CMD-SHELL", "echo 'SELECT 1 FROM dual;' | sqlplus -s system/$ORACLE_PASSWORD@localhost:1521/XEPDB1"]
      interval: 30s
      timeout: 10s
      retries: 10

volumes:
  oracle-data:

```

Para subir o container, abra o terminal do seu computador, navegue até onde se encontra a pasta que contém o arquivo `docker-compose.yml` e digite:
```bash
  docker compose up -d
```

### Explicando o arquivo .yml

#### Serviços

```yml
  services:
    oracle-xe:
``` 
Define os serviços que serão executados. Neste caso, temos apenas um serviço chamado oracle-xe

#### Configuração do Serviço Oracle XE

```yml
  image: gvenz/oracle-xe:21-slim

``` 
Especifica a imagem Docker a ser usada para este serviço. Estamos usando a imagem gvenzl/oracle-xe:21-slim, que é uma versão compacta do Oracle XE 21c.

#### Nome do container

```yml
  container_name: oracle-xe
```
Define o nome do contêiner. Isso facilita a referência ao contêiner em comandos Docker e scripts.

#### Mapeamento das portas
```yml
  ports:
    - 1521:1521
    - 5500:5500
```

Mapeia as portas do contêiner para as portas da máquina host. O Oracle XE usa a porta 1521 para conexões SQL*Net e a porta 5500 para o Oracle Enterprise Manager.

#### Variáveis de ambiente

```yml
  environment:
    - ORACLE_PASSWORD=yourpassword
    - APP_USER=youruser
    - APP_USER_PASSWORD=yourpassword2
```
Define variáveis de ambiente que são passadas para o contêiner. Essas variáveis configuram o Oracle XE:

ORACLE_PASSWORD define a senha para o usuário SYS e SYSTEM.
APP_USER e APP_USER_PASSWORD criam um usuário de aplicação (youruser) com a senha yourpassword2.

#### Volumes
```yml
  volumes:
    - oracle-data:/opt/oracle/oradata
```
Configura volumes para persistência de dados. O volume oracle-data mapeia para o diretório /opt/oracle/oradata dentro do contêiner, onde os dados do Oracle são armazenados. Isso garante que os dados persistam mesmo se o contêiner for removido.

#### Verificação
```yml
  healthcheck:
    test: ["CMD-SHELL", "echo 'SELECT 1 FROM dual;' | sqlplus -s system/$ORACLE_PASSWORD@localhost:1521/XEPDB1"]
    interval: 30s
    timeout: 10s
    retries: 10
```
Define uma verificação de saúde (healthcheck) para o contêiner. Ele executa um comando SQL simples (SELECT 1 FROM dual;) para garantir que o banco de dados está operacional.

## Conectando com o DBeaver

![image](https://github.com/user-attachments/assets/0679ebe3-9476-4547-bb5c-8dad78fdcc93)

Selecione a opção 'Oracle' e aperte em `next`.

![image](https://github.com/user-attachments/assets/f63c037f-0674-479a-9a38-27488dbf922c)

Nos campos abaixo, você colocará os seguintes dados:

`Host`: localhost <br>
`Port`: 1521 (padrão Oracle) <br>
`Database`: XE <br>
`Nome de usuário`: system <br>
`Senha`: suaSenha

Em seguida, clique em `Testar conexão`.

Tudo certo, seu banco de dados Oracle estará rodando em um container Docker.


