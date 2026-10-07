# API REST Node Fastify

API REST para controlar transações financeiras de um usuário, com identificação por cookie de sessão e persistência em banco de dados SQLite por padrão.

## Sobre o projeto

Este projeto foi desenvolvido com:

- Node.js
- Fastify
- TypeScript
- Knex.js
- SQLite (configuração padrão)
- Zod para validação de entradas
- Vitest para testes automatizados

A aplicação permite registrar transações financeiras de tipo crédito ou débito, listar as movimentações do usuário, buscar uma transação específica e calcular o resumo da conta atual.

## Funcionalidades

- Criação de transações
- Listagem de todas as transações do usuário atual
- Consulta de uma transação específica por ID
- Resumo da conta com soma das transações
- Identificação do cliente por cookie `sessionId`
- Isolamento das transações por usuário

## Requisitos

- Node.js >= 18
- npm

## Instalação

1. Clone o repositório
2. Instale as dependências:

```bash
npm install
```

3. Copie o arquivo de ambiente:

```bash
cp .env.example .env
```

> O projeto já vem configurado para usar SQLite por padrão. Se você quiser alterar o banco, ajuste as variáveis `DATABASE_CLIENT` e `DATABASE_URL` no arquivo `.env`.

Para executar os testes, também é necessário criar o arquivo de ambiente de teste:

```bash
cp .env.test.example .env.test
```

## Banco de dados

As migrations ficam em `db/migrations`.

Para criar as tabelas:

```bash
npm run knex -- migrate:latest
```

Se precisar reverter:

```bash
npm run knex -- migrate:rollback --all
```

## Executando a API

Modo de desenvolvimento:

```bash
npm run dev
```

A API fica disponível em:

```bash
http://localhost:3333
```

## Rotas

### POST /transactions

Cria uma nova transação.

Body:

```json
{
  "title": "Salário",
  "amount": 5000,
  "type": "credit"
}
```

- `title`: string
- `amount`: número
- `type`: `credit` ou `debit`

Se o usuário ainda não tiver `sessionId`, a API cria um cookie de sessão automaticamente.

### GET /transactions

Lista todas as transações vinculadas à sessão atual.

### GET /transactions/:id

Retorna uma transação específica da sessão atual.

### GET /transactions/summary

Retorna o resumo da conta somando todas as transações da sessão atual.

## Exemplo de resposta

```json
{
  "summary": {
    "amount": 3000
  }
}
```

## Testes

Para executar os testes da API:

```bash
npm test
```

## Estrutura principal

```text
src/
  app.ts
  database.ts
  server.ts
  env/
  middlewares/
  routes/

db/
  migrations/
```

## Observações

- O controle de usuário é feito via cookie de sessão (`sessionId`), sem autenticação por token.
- Cada sessão enxerga apenas as transações criadas dentro dela.
- O valor de transações do tipo `debit` é armazenado como negativo, permitindo o cálculo do saldo corretamente.

