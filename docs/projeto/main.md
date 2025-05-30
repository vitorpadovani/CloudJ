# Projeto Computação em Nuvem 2025.1
## Etapa 1

### 1.1 - Descrição do projeto
Este projeto consiste em uma API RESTful desenvolvida com FastAPI que realiza scraping de dados de criptomoedas, de uma fonte externa, e permite o acesso a essas informações por meio de endpoints documentados. A aplicação é containerizada com Docker, permitindo fácil execução e portabilidade.

O foco principal é viabilizar e verificar se será possível fazer a coleta e disponibilização de dados através de uma API que cadastra dados de usuários, com deploy simples e documentação acessível.

#### Integrantes do grupo
- Nome: Daniel Djanikian
- Nome: Vitor Padovani

#### Repositorio
- Link: [https://github.com/vitorpadovani/cloud_projeto](https://github.com/vitorpadovani/cloud_projeto)

### 1.2 - Scrap realizado
O scraping foi realizado na página [CoinMarketCap](https://coinmarketcap.com/), que fornece informações detalhadas sobre criptomoedas, incluindo preço, volume de negociação, capitalização de mercado e outras métricas relevantes. A coleta de dados foi feita utilizando a biblioteca BeautifulSoup para extrair as informações desejadas da página HTML. Além disso, foram utilizados os métodos `requests.Session()` para realizar as requisições HTTP e `BeautifulSoup` para parsear o conteúdo HTML obtido.

O scraping é realizado em um intervalo de alguns segundos entre as requisições, para evitar sobrecarga no servidor e respeitar as políticas de uso do site. Os dados coletados incluem o nome da criptomoeda e seu preço atual de mercado, sendo que no site, o preço é atualizado em tempo real, possibilitando a coleta de dados atualizados.

Para que seja possível realizar o scraping, é necessário que o usuário esteja cadastrado no sistema, o que é feito através de um endpoint específico. Após o cadastro, o usuário pode acessar os dados coletados por meio de outro endpoint, que retorna as informações em formato JSON.

### 1.3 - API RESTful
A API RESTful foi desenvolvida utilizando o framework FastAPI, que permite a criação de APIs de forma rápida e eficiente. A API possui os seguintes endpoints:

- `POST /registrar`: Cadastra um novo usuário no sistema.
- `POST /login`: Realiza o login de um usuário já cadastrado.
- `GET /consultar`: Retorna a lista das 10 criptomoedas mais valiosas, com nome e preço atual (em doláres) caso o usuário esteja autenticado.

Para cada endpoint, foram implementadas as seguintes funcionalidades:

- **Registrar**: O usuário deve fornecer um nome, email e senha. Todos são registrados na base de dados e retornará um token de autenticação JWT, em caso o cadastro seja realizado com sucesso.
- **Login**: O usuário deve fornecer email e senha. Caso as credenciais estejam corretas, um token JWT é retornado.
- **Consultar**: O usuário deve fornecer o token JWT obtido no cadastro ou login (sendo o mesmo em ambos). Caso o token seja válido, a API retorna uma lista de criptomoedas coletadas.

### 2.1 - Dockerização
A aplicação foi containerizada utilizando o Docker, permitindo que a API seja executada em qualquer ambiente que suporte Docker. O Dockerfile foi configurado para instalar as dependências necessárias e expor a porta 8000, onde a API está disponível.

Além disso, foi criado um arquivo `compose.yml`, que nele é utilizado apenas imagens do Docker Hub para facilitar a execução da aplicação, permitindo que o usuário inicie todos os serviços necessários com um único comando. Esse arquivo junta duas aplicações: a Aplicação FastAPI e o banco de dados Postgres, que armazena os dados dos usuários. Portanto ele se conecta com o banco de dados e realiza as operações de CRUD necessárias para o funcionamento da API.

Utilizando o Docker Desktop, é possível visualizar os containers em execução, monitorar logs e verificar criação dos usuários. Isso facilita o desenvolvimento e a manutenção da aplicação.

### 2.2 - Organização do projeto

api/
  Dockerfile
  app/
    main.py
  requirements.txt
compose.yaml
.env
.gitignore
README.md

### 2.3 - Arquivos chaves
- **compose.yaml**:
```bash
version: '3.9'

services:

  api:
    image: minha-api
    restart: always
    ports:  
      - 8000:8000
    depends_on:
      - db
    environment:
      DATABASE_URL: ${DATABASE_URL:-postgresql://usuario:cloudteste123@db:5432/meu_banco}
    command: uvicorn app.main:app --host 0.0.0.0 --port 8000

  db:
    image: postgres
    hostname: db
    restart: always
    environment:
      POSTGRES_USER: ${MEUUSUARIO:-usuario}
      POSTGRES_PASSWORD: ${MINHASENHADB:-cloudteste123}
      POSTGRES_DB: ${POSTGRES_BANCO:-meu_banco}
```

**OBS**: O arquivo `.env` contém as variáveis de ambiente utilizadas no `compose.yaml`, como `DATABASE_URL`, `MEUUSUARIO`, `MINHASENHADB` e `POSTGRES_BANCO`. Essas variáveis são utilizadas para configurar o banco de dados Postgres e a conexão com a API. Para um ambiente de produção, é importante garantir que essas variáveis estejam configuradas corretamente e que as senhas sejam mantidas em segredo, porém aqui no projeto, elas estão expostas para facilitar o entendimento do funcionamento do projeto.

### 2.4 - Docker Hub

O projeto foi publicado no Docker Hub, permitindo que qualquer pessoa possa baixar e executar a aplicação facilmente. O repositório contém as imagens necessárias para executar a aplicação, facilitando o compartilhamento e a colaboração entre desenvolvedores.

Esse processo de publicação no Docker Hub foi realizado através da criação de uma conta no Docker Hub, login na conta através do terminal, construção da imagem com o comando `docker tag minha-api vitorpadova/projeto_cloud` e o push da imagem para o repositório com o comando `docker push vitorpadova/projeto_cloud`.

O repositório do Docker Hub pode ser acessado através do seguinte link: [https://hub.docker.com/r/vitorpadova/projeto_cloud](https://hub.docker.com/r/vitorpadova/projeto_cloud).


### 3.1 - Execução do projeto

Para executar o projeto, siga os seguintes passos:
1. Certifique-se de ter o Docker instalado em sua máquina.

2. Clone o repositório do projeto:
```bash
git clone https://github.com/vitorpadovani/cloud_projeto.git
cd cloud_projeto
```

3. Crie um ambiente virtual e ative-o (opcional, mas recomendado):
```bash
python -m venv venv
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate  # Windows
```

4. Instale as dependências do projeto:
```bash
pip install -r api/requirements.txt # Caso esteja na raiz do projeto
```

5. Inicie os containers utilizando o Docker Compose:
```bash
docker-compose up -d
```
Isso iniciará os containers da API e do banco de dados em segundo plano.

6. Acesse a API em `http://localhost:8000/docs` para visualizar a documentação gerada automaticamente pelo FastAPI.
7. Para testar os endpoints, você pode usar ferramentas do próprio `/docs`. A documentação da API também fornece exemplos de como fazer as requisições.

8. Para parar os containers, utilize o seguinte comando:
```bash
docker-compose down
```
Isso encerrará os containers e liberará os recursos utilizados.

### 3.2 - Testes
Os testes das funcionalidades da API foram realizados justamente utilizando a documentação gerada pelo FastAPI, que não fornece uma interface mas é bom para testar os endpoints. Os testes foram realizados com sucesso, validando o funcionamento correto da API.

Os testes realizados foram:

- Cadastro de usuário: Verificamos se o usuário foi cadastrado corretamente e se o token JWT foi gerado.

![Tela do Teste Registro](img/projeto1-1.png)
/// caption
Teste Registro de Usuário com sucesso
///

- Cadastro de usuário já existente: Verificamos se o cadastro retornou erro 409 quando o usuário já estava cadastrado.

![Tela do Teste Registro já existente](img/projeto1-2.png)
/// caption
Teste Cadastro de Usuário já existente com erro
///


- Login de usuário: Verificamos se o login foi realizado corretamente e se o token JWT foi gerado.

![Tela do Teste Login](img/projeto1-3.png)
/// caption
Teste Login de Usuário com sucesso
///

- Login de usuário incorreto: Verificamos se o login retornou erro 401 quando o usuário não estava cadastrado ou se errou o email/senha.

![Tela do Teste Login incorreto](img/projeto1-4.png)
/// caption
Teste Login de Usuário incorreto com erro
///

- Consulta de criptomoedas: Verificamos se a consulta retornou as 10 criptomoedas mais valiosas com sucesso, utilizando o token JWT gerado no cadastro ou login.

![Tela do Teste Consulta](img/projeto1-5.png)
/// caption
Teste Consulta de Criptomoedas com sucesso
///

- Consulta de criptomoedas sem autenticação: Verificamos se a consulta retornou erro 403 quando o usuário não estava autenticado.

![Tela do Teste Consulta sem autenticação](img/projeto1-6.png)
/// caption
Teste Consulta de Criptomoedas sem autenticação
///

### 3.3 - Video de execução da aplicação

O video a seguir mostra a execução da aplicação, com um cadastro de um usuário. Além disso, também mostra a execução desse usuário entrando na Base de dados e o Docker, portanto, a visualização dos containers em execução.

- [Link do video](https://youtu.be/Zixz8pPbRB4)

### 4.1 - Conclusão
O projeto foi desenvolvido com sucesso, atendendo aos requisitos propostos. A API foi implementada utilizando o FastAPI, permitindo a criação de endpoints para cadastro e consulta de criptomoedas. O scraping foi realizado com sucesso, coletando dados atualizados do site CoinMarketCap.
A aplicação foi containerizada com Docker, facilitando a execução e portabilidade. Os testes realizados validaram o funcionamento correto da API, garantindo que todas as funcionalidades estão operando conforme o esperado.
Por fim, o projeto foi publicado no Docker Hub, permitindo que qualquer pessoa possa baixar e executar a aplicação facilmente.

Portanto conseguimos desenvolver conceitos como por exemplo:
- Containerização local com Docker Compose
- Criação de uma API RESTful com FastAPI
- Scraping de dados de uma fonte externa
- Armazenamento de dados em um banco de dados Postgres
- Autenticação de usuários com JWT
- Documentação da API com Swagger
- Testes de funcionalidades da API utilizando a documentação gerada pelo FastAPI

### 4.2 - Referências
- [Site da Disciplina](https://insper.github.io/computacao-nuvem/)
- [FastAPI](https://fastapi.tiangolo.com/)
- [Docker](https://www.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Postgres](https://hub.docker.com/_/postgres)
- [Video Tutorial Docker Hub](https://www.youtube.com/watch?v=iqqDU2crIEQ&t=1002s)
- [CoinMarketCap](https://coinmarketcap.com/)


## Etapa 2

### 1.1 - Descrição do projeto

Essa segunda etapa do projeto consiste em realizar o deploy da aplicação desenvolvida na Etapa 1 em um serviço de nuvem, utilizando o AWS Lightsail. O objetivo é disponibilizar a API para acesso público, permitindo que usuários possam interagir com a aplicação através da internet. Portanto foi necessário aprender sobre o serviço AWS Lightsail, que oferece uma maneira simples e econômica de hospedar aplicações na nuvem. Durante o projeto serão abordados os temas de criação de banco de dados, configuração de container, deploy da aplicação e gastos com os serviços utilizados.

### 1.2 - Permissões e criação da conta

Para utilizar o AWS Lightsail, foi necessário criar uma conta na AWS, através do IAM, com permissões de administrador. Após a criação da conta, foi possível acessar o console do AWS Lightsail.

![Tela Permissões User](img/projeto2-permissoes.png)
/// caption
Tela de Permissões do Usuário no AWS IAM
///

### 2.1 - Criação do container

Inicialmente, foi necessário criar um container no AWS Lightsail para hospedar a aplicação. O serviço desse container foi criado em Virginia, Estados Unidos, (us-east-1), que é uma das regiões disponíveis no AWS Lightsail. A criação do container foi feita através do console do AWS Lightsail, onde foram configurados os parâmetros necessários. Para esse container utilizamos a capacidade de 1 GB de Memória RAM, 0.25 vCPU de processamento, sendo esse um power Micro, além disso definimos que o container teria uma escala de 1 node, o que significa que teríamos apenas um container rodando. 

Isso nos resultaria em um custo de $10 USD por mês (sendo que o valor não é cobrado pelos primeiros 3 meses, devido ao período de testes gratuito do AWS Lightsail).

![Tela Criação Container](img/projeto2-criacaoContainer.png)
/// caption
Tela de Criação do Container no AWS Lightsail
///

![Tela Container](img/projeto2-container.png)
/// caption
Tela de Container rodando no AWS Lightsail
///


### 2.2 - Criação do banco de dados

Para que a aplicação fosse realizada com sucesso, foi necessário criar um banco de dados no AWS Lightsail. O banco de dados utilizado foi o PostgreSQL (17.5), que é compatível com a aplicação desenvolvida. A criação do banco de dados foi feita através do console do AWS Lightsail, onde foram configurados os parâmetros necessários, como nome do banco, usuário e senha. Este banco de dados foi criado com as configurações de 1 GB de Memória, 2 vCPUs de processamento, 40 GB SSD de armazenamento e 100 GB de transferência de dados. Também foi configurado na mesma região do container, ou seja, em Virginia Zona A (us-east-1a).

Nos resultou em um custo de $15 USD por mês (sendo que o valor não é cobrado pelos primeiros 3 meses, devido ao período de testes gratuito do AWS Lightsail).

![Tela Criação Banco de Dados](img/projeto2-criacaoBanco1.png)
/// caption
Tela de Criação do Banco de Dados (Região e Tipo de Banco)
///

![Tela Criação Banco de Dados](img/projeto2-criacaoBanco2.png)
/// caption
Tela de Criação do Banco de Dados (Plano de Preço e Configurações)
///

![Tela DB](img/projeto2-db.png)
/// caption
Tela da Base de Dados disponível no AWS Lightsail
///

### 2.3 - Deploy da aplicação

Para realizar o deploy da aplicação no AWS Lightsail, foi necessário configurar o container criado anteriormente. Essa etapa envolveu a integração do container com o banco de dados PostgreSQL, garantindo que a aplicação pudesse se conectar ao banco e realizar as operações necessárias.

Iniciamos o processo deixando o banco de dados como "Public Mode", o que significa que ele pode ser acessado de fora do AWS Lightsail, permitindo que a aplicação se conecte a ele. Em seguida, foi necessário configurar as variáveis de ambiente no container, como a imagem que definida sendo a `vitorpadova/projeto_cloud:latest`, além de outras variáveis como `DATABASE_URL`, `MEUUSUARIO`, `MINHASENHADB` e `POSTGRES_BANCO`, para que a aplicação pudesse se conectar ao banco de dados corretamente.

Sendo assim, a variável `DATABASE_URL` foi configurada com o seguinte formato:

```bash
postgresql://MEUUSUARIO:MINHASENHADB@POSTGRES_BANCO:portaBanco/master
```

Onde:

- `MEUUSUARIO`: é o usuário do banco de dados criado no AWS Lightsail.

- `MINHASENHADB`: é a senha do usuário do banco de dados.

- `POSTGRES_BANCO`: é a URL (endpoint) do banco de dados fornecida pelo AWS Lightsail.

- `portaBanco`: é a porta do banco de dados (geralmente 5432 para PostgreSQL).

- `master`: é o nome do banco de dados que será utilizado pela aplicação (master database name).

#### 2.3.1 Correção no projeto

Para conclusão do deploy, foi necessário realizar uma correção no projeto, pois não havíamos feito o /healthcheck no Dockerfile. Portanto, foi necessário adicionar o seguinte comando no Dockerfile:

```bash
@app.get("/health_check", status_code=200)
def health_check():
    return {"health_check": gethostname()}
```

Isso cria um endpoint `/health_check` que retorna o nome do host da aplicação, permitindo que o AWS Lightsail verifique o estado da aplicação. Após essa correção, foi possível realizar o deploy da aplicação com sucesso.

### 3.1 - Verificação do deploy

Após o deploy da aplicação no Lightsail, foi fornecido um domínio para acessar a aplicação. Com isso, foi possível acessar a documentação da API gerada pelo FastAPI, onde é possível visualizar os endpoints disponíveis e realizar testes diretamente pela interface.

#### 3.1.1 Link da API

- Link: [https://fastapi-service.nz5y2v08qbr6m.us-east-1.cs.amazonlightsail.com/docs](https://fastapi-service.nz5y2v08qbr6m.us-east-1.cs.amazonlightsail.com/docs)

### 3.2 - Testes da API

Com a API acessível, foram realizados testes nos endpoints disponíveis, utilizando a documentação gerada pelo FastAPI. Os testes foram realizados com sucesso, validando o funcionamento correto da API no ambiente de produção.

Foi possível visualizar a criação de usuários através de um script de teste que printa os 5 usuários cadastrados, o que mostrou que a aplicação está funcionando e a base de dados está sendo acessada corretamente. O script utilizado para testar a API foi o seguinte:

```python
import psycopg2

conn = psycopg2.connect(
    host="seu-endpoint",
    port=5432,
    user="seu-usuario",
    password="sua-senha",
    dbname="seu-banco"
)

cur = conn.cursor()
cur.execute("SELECT * FROM usuarios LIMIT 5;")
rows = cur.fetchall()

for row in rows:
    print(row)

cur.close()
conn.close()
```
OBS: É necessário substituir `seu-endpoint`, `seu-usuario`, `sua-senha` e `seu-banco` pelos valores correspondentes ao seu banco de dados no AWS Lightsail. 

#### 3.2.1 Vídeo de execução da aplicação

- Link do vídeo: [https://youtu.be/4t3TXGocu9g](https://youtu.be/4t3TXGocu9g)


### 4.1 - Gastos com os serviços utilizados

Os gastos com os serviços utilizados no AWS Lightsail foram os seguintes:

- **Container**: $10 USD por mês

- **Banco de Dados**: $15 USD por mês 

OBS: Esses valores são referentes ao plano de preços escolhido e podem variar dependendo do uso e da região escolhida. Além disso, o AWS Lightsail oferece um período de testes gratuito de 3 meses, onde não há cobrança pelos serviços utilizados.

Portanto, o custo total mensal para manter a aplicação no AWS Lightsail seria de $25 USD, considerando os serviços de container e banco de dados.

O gasto também é calculado por impostos e GB transferidos, que são cobrados de acordo com o uso da aplicação. Portanto, é importante monitorar esses valores para evitar surpresas na fatura mensal.

Além disso é possível utilizar o AWS Cost Explorer para monitorar os gastos e otimizar os recursos utilizados, garantindo que a aplicação esteja rodando de forma eficiente e econômica.

![Tela Gastos](img/projeto2-gastos.png)
/// caption
Tela de Gastos do AWS Lightsail
///

A previsão para os próximos meses é de que os gastos permaneçam dentro do esperado, considerando o uso da aplicação e os serviços contratados, que seria de $25 USD mensais. 

### 5.1 - Conclusão

O projeto foi finalizado com êxito, com a aplicação desenvolvida na Etapa 1 sendo implantada em um ambiente de nuvem utilizando o AWS Lightsail. A API está disponível publicamente, permitindo que qualquer usuário acesse e utilize suas funcionalidades pela internet.

A solução foi completamente containerizada e configurada para se comunicar com um banco de dados PostgreSQL, assegurando o armazenamento seguro e eficiente das informações dos usuários. Todos os testes realizados no ambiente de produção confirmaram que a API funciona corretamente, com todas as funcionalidades operando como planejado.

Durante esse processo, foi possível adquirir conhecimentos práticos sobre o AWS Lightsail, uma plataforma que oferece uma opção simplificada e econômica para hospedagem em nuvem. A configuração tanto do container quanto do banco de dados foi realizada de forma ágil, permitindo colocar a aplicação no ar rapidamente.

Dessa forma, foi possível consolidar diversos aprendizados importantes, como:

- Realizar o deploy de uma aplicação em um serviço de nuvem utilizando o AWS Lightsail;

- Configurar containers e bancos de dados dentro da plataforma;

- Estabelecer a integração entre a aplicação e o banco de dados PostgreSQL;

- Validar, por meio de testes, o correto funcionamento da API no ambiente de produção;

- Acompanhar e controlar os custos relacionados aos serviços contratados na AWS Lightsail.

### 5.2 - Referências
- [Site da Disciplina](https://insper.github.io/computacao-nuvem/)
- [AWS Lightsail](https://aws.amazon.com/pt/lightsail/)
- [AWS IAM](https://aws.amazon.com/pt/iam/)
- [AWS Cost Explorer](https://aws.amazon.com/pt/aws-cost-management/aws-cost-explorer/)
