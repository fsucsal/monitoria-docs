# API v1 — Sistema de Seleção de Monitoria (UCSal)

Esta é a **documentação oficial da API REST** do Sistema de Seleção de Monitoria da UCSal.

A API expõe operações para **autenticação, inscrições, seleção, acompanhamento de monitorias e certificação**, funcionando como **contrato público** entre backend e consumidores (frontend, integrações e relatórios).

---

## Visão Geral

- **Estilo:** REST
- **Formato de dados:** JSON
- **Padrão de contrato:** OpenAPI 3.0
- **Versão:** v1
- **Autenticação:** JWT (Bearer Token)
- **Base path:** `/api/v1`

Ambientes:

- Produção (exemplo): `https://api.ucsal.edu.br/api/v1`
- Local: `http://localhost:8080/api/v1`

---

## Autenticação e Autorização

A API utiliza **JWT (JSON Web Token)** para autenticação.

### Header obrigatório

```
Authorization: Bearer <access_token>
```

### Endpoints de autenticação

- `POST /auth/login`

  - Autentica o usuário
  - Retorna `accessToken` e `refreshToken`

- `POST /auth/refresh`

  - Gera um novo `accessToken` a partir de um `refreshToken` válido

> Endpoints protegidos retornam **401 Unauthorized** quando o token está ausente, inválido ou expirado.

---

## Padrão de Requests

### Corpo da requisição

- Sempre em **JSON**
- Apenas os campos necessários para a operação
- Campos extras são rejeitados quando violam regras de validação

Exemplo:

```json
{
  "disciplineId": "uuid",
  "applicationData": {
    "motivation": "Texto livre"
  }
}
```

### Validação

- Validações estruturais ocorrem no nível de **DTO**
- Validações de regra de negócio ocorrem nos **Services**
- Erros de validação resultam em **400 Bad Request**

---

## Padrão de Responses

### Responses de sucesso

| Código         | Uso                                  |
| -------------- | ------------------------------------ |
| 200 OK         | Consulta ou atualização bem-sucedida |
| 201 Created    | Recurso criado                       |
| 202 Accepted   | Processamento assíncrono iniciado    |
| 204 No Content | Operação realizada sem retorno       |

Exemplo:

```json
{
  "id": "uuid",
  "status": "APPROVED"
}
```

---

## Padrão de Erros

Todos os erros retornam um **formato único**, independente da origem (validação, regra de negócio ou infraestrutura).

### Estrutura padrão de erro

```json
{
  "code": "ENROLLMENT_LIMIT_EXCEEDED",
  "message": "O aluno já atingiu o limite de inscrições permitido"
}
```

### Regras importantes

- A API **não expõe detalhes internos** (stack trace, nomes de classes, SQL, etc.)
- Mensagens são **orientadas ao consumidor da API** (UI ou integração)
- O campo `code` é estável e pode ser usado pela UI para tradução

---

## Categorias de Erro e Status HTTP

| Categoria              | Status HTTP               |
| ---------------------- | ------------------------- |
| Erro de validação      | 400 Bad Request           |
| Regra de negócio       | 409 Conflict              |
| Não autorizado         | 401 Unauthorized          |
| Acesso negado          | 403 Forbidden             |
| Recurso não encontrado | 404 Not Found             |
| Erro inesperado        | 500 Internal Server Error |

---

## Assincronicidade

Algumas operações são executadas de forma assíncrona, como:

- Geração de certificados
- Processamentos de relatório

Nesses casos, a API retorna:

- **202 Accepted**
- Um identificador de processo (`jobId`)

Exemplo:

```json
{
  "jobId": "abc-123"
}
```

---

## Versionamento da API

- A versão atual é **v1**
- Mudanças incompatíveis resultarão em **nova versão** (`/v2`)
- Mudanças compatíveis podem ser introduzidas dentro da mesma versão

---

## Segurança

- Todos os endpoints protegidos exigem JWT
- Controle de acesso baseado em **roles**
- A API nunca confia em dados sensíveis vindos do cliente

---

## Referência Completa da API

A definição completa de endpoints, schemas e exemplos está disponível no contrato OpenAPI:

- [`openapi-v1.yaml`](./openapi-v1.yaml)

Este arquivo é a **fonte de verdade do contrato da API**.

---

## Considerações Finais

Esta documentação descreve **como consumir a API**, não como ela é implementada internamente.

Detalhes sobre:

- exceções
- regras de negócio
- validações
- logs

estão documentados na seção **Desenvolvimento**.
