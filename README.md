# CondoManager API

API REST para gerenciamento de usuários de um condomínio, desenvolvida com Kotlin e Spring Boot. O projeto fornece operações básicas de cadastro, consulta, atualização e exclusão de usuários, utilizando Spring Data JPA e um banco de dados H2 persistido em arquivo.

> **Status:** em desenvolvimento.

## Tecnologias

- [Kotlin](https://kotlinlang.org/) 2.3.21
- [Spring Boot](https://spring.io/projects/spring-boot) 4.1.1
- Spring Web MVC
- Spring Data JPA
- Hibernate
- H2 Database
- Gradle Kotlin DSL
- Java 17 ou superior
- Docker

## Funcionalidades

- Cadastrar usuários
- Listar usuários cadastrados
- Atualizar dados de um usuário
- Excluir usuários
- Persistir dados localmente em um banco H2
- Acessar o console web do H2 para inspeção e desenvolvimento

## Estrutura do projeto

```text
.
├── src
│   ├── main
│   │   ├── kotlin/com/condomanager/api
│   │   │   ├── controller/     # Endpoints HTTP
│   │   │   ├── entity/         # Entidades JPA
│   │   │   ├── repository/     # Repositórios de persistência
│   │   │   ├── service/        # Regras de negócio
│   │   │   └── ApiApplication.kt
│   │   └── resources/
│   │       └── application.properties
│   └── test/                   # Testes automatizados
├── data/                       # Arquivos do banco H2 local
├── build.gradle.kts
├── Dockerfile
└── gradlew
```

A aplicação segue uma separação simples em camadas:

- **Controller:** recebe requisições HTTP e retorna respostas da API.
- **Service:** centraliza as operações de negócio.
- **Repository:** realiza o acesso aos dados por meio do Spring Data JPA.
- **Entity:** representa os dados persistidos no banco.

## Pré-requisitos

Para executar localmente, instale:

- JDK 17 ou superior
- Git

O Gradle não precisa ser instalado separadamente, pois o projeto inclui o Gradle Wrapper (`gradlew`).

## Como executar localmente

1. Clone o repositório:

   ```bash
   git clone https://github.com/PabloEtec/api.git
   cd api
   ```

2. Inicie a aplicação:

   **Linux/macOS:**

   ```bash
   ./gradlew bootRun
   ```

   **Windows:**

   ```powershell
   .\gradlew.bat bootRun
   ```

3. A API estará disponível em:

   ```text
   http://localhost:8080
   ```

Na primeira execução, o banco H2 será criado no diretório `data/`, conforme configurado em `application.properties`.

## Executando com Docker

Para criar a imagem:

```bash
docker build -t condomanager-api .
```

Para iniciar o container:

```bash
docker run --rm -p 8080:8080 -v "$(pwd)/data:/app/data" condomanager-api
```

No Windows PowerShell, utilize:

```powershell
docker run --rm -p 8080:8080 -v "${PWD}/data:/app/data" condomanager-api
```

O volume mantém os dados do H2 mesmo após o container ser encerrado.

## Endpoints

A API utiliza o recurso `/usuarios`.

| Método | Rota | Descrição | Resposta esperada |
|---|---|---|---|
| `POST` | `/usuarios` | Cadastra um novo usuário | `200 OK` |
| `GET` | `/usuarios` | Lista todos os usuários | `200 OK` |
| `PUT` | `/usuarios/{id}` | Atualiza um usuário existente | `200 OK` ou `404 Not Found` |
| `DELETE` | `/usuarios/{id}` | Exclui um usuário | `204 No Content` ou `404 Not Found` |

### Cadastrar usuário

```bash
curl -X POST http://localhost:8080/usuarios \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "Maria Silva",
    "email": "maria@example.com",
    "senha": "senha-de-exemplo"
  }'
```

### Listar usuários

```bash
curl http://localhost:8080/usuarios
```

### Atualizar usuário

```bash
curl -X PUT http://localhost:8080/usuarios/1 \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "Maria Souza",
    "email": "maria.souza@example.com",
    "senha": "nova-senha-de-exemplo"
  }'
```

### Excluir usuário

```bash
curl -X DELETE http://localhost:8080/usuarios/1
```

## Console H2

Durante o desenvolvimento, o console do H2 fica disponível em:

```text
http://localhost:8080/h2-console
```

Use os seguintes dados de conexão:

| Campo | Valor |
|---|---|
| JDBC URL | `jdbc:h2:file:./data/condomanager` |
| User Name | `sa` |
| Password | deixar em branco |

O console H2 está habilitado para facilitar o desenvolvimento local. Não o disponibilize publicamente em ambientes de produção sem revisar as configurações de segurança.

## Banco de dados

A aplicação utiliza um banco H2 baseado em arquivo, com as seguintes configurações principais:

- Arquivo do banco: `./data/condomanager`
- Estratégia de atualização do schema: `update`
- Porta da aplicação: `8080`
- Tabela de usuários: `tb_usuarios`

A entidade `Usuario` possui os campos `id`, `nome`, `email` e `senha`. O nome é configurado como único no banco de dados.

## Testes

Para executar os testes:

```bash
./gradlew test
```

Para gerar o artefato da aplicação:

```bash
./gradlew bootJar
```

O arquivo `.jar` será gerado em `build/libs/`.

## Observações de segurança

Este projeto está em desenvolvimento e a implementação atual deve ser reforçada antes de qualquer uso em produção. Em especial:

- Senhas não devem ser armazenadas ou retornadas em texto puro; utilize hash seguro, como BCrypt.
- Adicione autenticação e autorização antes de expor os endpoints.
- Configure CORS, validação de entrada e tratamento padronizado de erros.
- Não utilize credenciais padrão nem mantenha o console H2 exposto em produção.
- Prefira um banco de dados de produção e migrações versionadas, como Flyway ou Liquibase.

## Licença

Nenhuma licença foi definida no repositório até o momento.
