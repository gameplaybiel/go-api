# Rest API em Go

O projeto "Go API" é uma API REST para gerenciamento de produtos, desenvolvida com Go e o framework Gin. Ele utiliza PostgreSQL como banco de dados, com o ambiente containerizado usando Docker. A estrutura do projeto segue princípios da Arquitetura Limpa (Clean Architecture), separando as responsabilidades em camadas de `controller`, `usecase` e `repository`.

## Tecnologias:
* Golang
* Gin
* PostgreSQL
* Docker
* Go-sql-driver/pq

## Pré-requisitos

*   Go (versão 1.21 ou superior)
*   Docker e Docker Compose

## Como Executar

1.  **Clone o repositório:**
    ```bash
    git clone https://github.com/seu-usuario/go-api.git
    cd go-api
    ```

2.  **Inicie o banco de dados com Docker:**
    Execute o comando abaixo para iniciar o container do PostgreSQL em segundo plano.
    ```bash
    docker-compose up -d
    ```
    *Nota: As credenciais do banco de dados estão no arquivo `docker-compose.yml` e `db/conn.go`. Para um ambiente de produção, é recomendado usar variáveis de ambiente.*

3.  **Execute a aplicação:**
    O servidor da API iniciará na porta `8000`.
    ```bash
    go run cmd/main.go
    ```

## Endpoints da API

*   **`GET /products`**: Retorna uma lista de todos os produtos.
*   **`GET /product/:productId`**: Retorna um produto específico pelo seu ID.
*   **`POST /product`**: Cria um novo produto.
    *   **Corpo da Requisição (JSON):**
        ```json
        {
            "name": "Nome do Produto",
            "price": 99.90
        }
        ```

## O Problema que o Projeto Resolve

Este projeto serve como um backend fundamental para um sistema de gerenciamento de produtos. Ele oferece operações básicas de CRUD (Criar, Ler) através de uma API REST, permitindo que aplicações front-end ou outros serviços possam consumir e manipular dados de produtos de forma estruturada e segura. O objetivo principal foi construir uma base sólida e escalável, aplicando boas práticas de engenharia de software.

## Desafios e Aprendizados

### Desafios Enfrentados

*   **Compreensão da Arquitetura Limpa:** O maior desafio foi entender como as camadas (`controller`, `usecase`, `repository`) se comunicam e como a injeção de dependência funciona na prática para manter o código desacoplado.
    *   **Solução:** Desenhar o fluxo de uma requisição, desde a chegada no `controller` até a consulta no banco de dados pelo `repository`, ajudou a visualizar o papel de cada camada. A configuração no `main.go`, onde as dependências são "injetadas" de fora para dentro (repository -> usecase -> controller), foi a chave para consolidar o entendimento.

*   **Tratamento de Erros Específicos:** Lidar com casos como um `GET` para um ID que não existe no banco. Inicialmente, a API retornava um erro genérico `500 Internal Server Error`.
    *   **Solução:** No `repository`, foi implementada uma verificação para o erro `sql.ErrNoRows`. Quando esse erro ocorre, a camada retorna um objeto de produto vazio e um erro `nil`. Isso permite que o `controller` verifique se o produto foi encontrado e retorne um status `404 Not Found` com uma mensagem clara, melhorando a experiência de quem consome a API.

### O que Aprendi com o Processo

*   **Desenvolvimento de APIs com Gin:** Como criar rotas, validar e extrair dados de requisições (parâmetros de URL e corpo JSON) e formatar respostas JSON de maneira eficiente.
*   **Injeção de Dependência em Go:** A importância de usar interfaces e structs para construir um sistema modular, onde as implementações concretas (como o repositório do PostgreSQL) podem ser facilmente trocadas sem alterar a lógica de negócio (`usecase`).
*   **Gerenciamento de Banco de Dados:** Como utilizar o pacote `database/sql` para executar queries parametrizadas, prevenindo SQL Injection, e como gerenciar o ciclo de vida de conexões e statements (`Prepare`, `QueryRow`, `Scan`).
*   **Ambientes de Desenvolvimento com Docker:** A praticidade de usar o Docker Compose para configurar e executar serviços dependentes (como o banco de dados), garantindo que o ambiente de desenvolvimento seja consistente e fácil de replicar.

## Referência:
Tema do vídeo:Como criar uma REST API completa do zero com GO | Golang tutorial - iniciante
Link: https://www.youtube.com/watch?v=3p4mpId_ZU8&t=2777s