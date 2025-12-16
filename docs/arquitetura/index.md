# Documento de Arquitetura de Software

## Visão Geral

Este documento descreve a **visão arquitetural de alto nível** do _Sistema de Seleção de Monitoria — UCSal_.

Ele tem como objetivo apresentar **o que é o sistema, como ele está organizado e quais decisões estruturais foram tomadas**, sem entrar em detalhes de implementação de código.

> Detalhes de padrões de desenvolvimento, validações, exceções, testes e logging estão documentados no **Guia de Padrões de Desenvolvimento Backend**.

### Objetivos do Documento

- Apresentar a arquitetura do sistema de forma clara e padronizada
- Servir como referência para evolução técnica do projeto
- Apoiar decisões de manutenção, extensão e refatoração
- Alinhar entendimento entre backend, frontend e infraestrutura

### Escopo

Este documento cobre:

- Estilo arquitetural adotado
- Organização modular do sistema
- Visão geral dos módulos e responsabilidades
- Estratégias de integração, segurança e observabilidade
- Diretrizes de infraestrutura e deploy

**Fora de escopo:**

- Padrões de código
- Convenções de nomenclatura
- Regras de negócio detalhadas
- Implementação de validações e exceções

---

## Visão Arquitetural

### Estilo Arquitetural

- **Arquitetura:** Monólito Modular
- **Separação por módulos de domínio**
- **Sem DDD tático pesado** (sem aggregates complexos, factories, etc.)

> A modularização é lógica e organizacional, preparando o terreno para extração futura de serviços.

### Modelo de Execução

- Backend Java + Spring Boot
- Deploy em containers Docker
- Docker Compose no início
- Kubernetes como possibilidade futura

### Comunicação

- APIs REST
- Contrato documentado com **OpenAPI (Swagger)**
- Comunicação síncrona entre frontend e backend
- Processos pesados podem evoluir para execução assíncrona

### Persistência

- **PostgreSQL** como banco principal
- **Redis (opcional)** para cache, locks simples e filas leves

### Autenticação

- JWT stateless
- Evolução futura para OAuth2 / SSO institucional

### Observabilidade

- Logs estruturados (JSON)
- Preparado para integração com ferramentas externas

---

## Decisões Arquiteturais Principais

### Monólito Modular

**Justificativa:**

- Reduz complexidade inicial
- Facilita aprendizado
- Mantém organização clara

### API REST + OpenAPI

**Justificativa:**

- Contrato claro
- Desenvolvimento paralelo
- Base para testes e documentação

### PostgreSQL

**Justificativa:**

- Relacionamentos claros
- Integridade referencial
- Relatórios estruturados

### Docker

**Justificativa:**

- Padronização de ambiente
- Facilidade de setup

---

## Organização em Módulos

Cada módulo representa um **contexto funcional claro**, isolado por pacote.

| Módulo        | Responsabilidade           |
| ------------- | -------------------------- |
| auth          | Autenticação, perfis, JWT  |
| enrollment    | Inscrição de alunos        |
| selection     | Avaliação e seleção        |
| monitoring    | Controle de horas          |
| certification | Emissão de certificados    |
| reports       | Consultas e exportações    |
| shared        | Componentes compartilhados |

### Regra Importante

- Um módulo **não acessa diretamente** repositórios de outro
- Comunicação ocorre via **interfaces expostas pelo módulo**

---

## Estrutura de Código

```text
src/
├── auth/
│   ├── controller/
│   ├── service/
│   ├── domain/
│   └── repository/
├── enrollment/
├── selection/
├── monitoring/
├── certification/
├── reports/
└── shared/
    ├── exception/
    ├── logging/
    ├── security/
    └── dto/
```

**Observações**

- `shared` contém apenas código **transversal**
- Nada de regras de negócio no `shared`

---

## Modelagem de Dados (Visão Geral)

A modelagem segue princípios relacionais clássicos, sem lógica de negócio nas entidades.

> Entidades JPA representam **estado**, não comportamento complexo.

(Tabelas conforme especificação funcional do projeto)

---

## Contrato de APIs

- Versionamento: `/api/v1/...`
- Todas as respostas de erro seguem padrão único

Exemplo:

```json
{
  "code": "ALUNO_LIMITE_INSCRICOES",
  "message": "Aluno atingiu o limite máximo de inscrições"
}
```

---

## Segurança

### Autenticação

- JWT com access token e refresh token

### Autorização

Perfis principais:

- STUDENT
- TEACHER
- COORDINATOR
- ADMIN

Autorização aplicada no **controller** via annotations.

---

## Tratamento de Erros e Exceções

- Controllers não tratam exceções
- Services lançam exceções de negócio
- Um único handler global traduz para HTTP

(Regras detalhadas no Guia de Padrões Backend)

---

## Logs e Observabilidade

- Logger padrão do Spring (SLF4J)
- Logs estruturados em JSON
- Logar apenas:

  - Eventos relevantes
  - Erros

---

## Infraestrutura e Deploy

### Ambientes

- Dev: Docker Compose
- Prod: a definir

### CI/CD

- GitHub Actions
- Migrations automáticas

---

## Qualidade de Código

- Testes unitários obrigatórios para recursos chave
- Code review obrigatório
- ADRs documentados

---

## Evolução Futura

- Extração do módulo `certification`
- Introdução de mensageria
- Escalabilidade horizontal
