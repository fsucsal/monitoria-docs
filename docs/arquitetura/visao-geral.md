# Documento de Arquitetura de Software

**Sistema de Seleção de Monitoria — UCSal**
**Versão:** 1.0
**Data:** 04-12-2025
**Autor:** Anderson Vieira

---

## 1. Visão Geral

Este documento descreve a **visão arquitetural de alto nível** do _Sistema de Seleção de Monitoria — UCSal_.

Ele tem como objetivo apresentar **o que é o sistema, como ele está organizado e quais decisões estruturais foram tomadas**, sem entrar em detalhes de implementação de código.

> Detalhes de padrões de desenvolvimento, validações, exceções, testes e logging estão documentados no **Guia de Padrões de Desenvolvimento Backend**.

### 1.1 Objetivos do Documento

- Apresentar a arquitetura do sistema de forma clara e padronizada
- Servir como referência para evolução técnica do projeto
- Apoiar decisões de manutenção, extensão e refatoração
- Alinhar entendimento entre backend, frontend e infraestrutura

### 1.2 Escopo

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

## 2. Visão Arquitetural

### 2.1 Estilo Arquitetural

- **Arquitetura:** Monólito Modular
- **Separação por módulos de domínio**
- **Sem DDD tático pesado** (sem aggregates complexos, factories, etc.)

> A modularização é lógica e organizacional, preparando o terreno para extração futura de serviços.

### 2.2 Modelo de Execução

- Backend Java + Spring Boot
- Deploy em containers Docker
- Docker Compose no início
- Kubernetes como possibilidade futura

### 2.3 Comunicação

- APIs REST
- Contrato documentado com **OpenAPI (Swagger)**
- Comunicação síncrona entre frontend e backend
- Processos pesados podem evoluir para execução assíncrona

### 2.4 Persistência

- **PostgreSQL** como banco principal
- **Redis (opcional)** para cache, locks simples e filas leves

### 2.5 Autenticação

- JWT stateless
- Evolução futura para OAuth2 / SSO institucional

### 2.6 Observabilidade

- Logs estruturados (JSON)
- Preparado para integração com ferramentas externas

---

## 3. Decisões Arquiteturais Principais

### 3.1 Monólito Modular

**Justificativa:**

- Reduz complexidade inicial
- Facilita aprendizado
- Mantém organização clara

### 3.2 API REST + OpenAPI

**Justificativa:**

- Contrato claro
- Desenvolvimento paralelo
- Base para testes e documentação

### 3.3 PostgreSQL

**Justificativa:**

- Relacionamentos claros
- Integridade referencial
- Relatórios estruturados

### 3.4 Docker

**Justificativa:**

- Padronização de ambiente
- Facilidade de setup

---

## 4. Organização em Módulos

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

## 5. Estrutura de Código

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

### Observações

- `shared` contém apenas código **transversal**
- Nada de regras de negócio no `shared`

---

## 6. Modelagem de Dados (Visão Geral)

A modelagem segue princípios relacionais clássicos, sem lógica de negócio nas entidades.

> Entidades JPA representam **estado**, não comportamento complexo.

(Tabelas conforme especificação funcional do projeto)

---

## 7. Contrato de APIs

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

## 8. Segurança

### 8.1 Autenticação

- JWT com access token e refresh token

### 8.2 Autorização

Perfis principais:

- STUDENT
- TEACHER
- COORDINATOR
- ADMIN

Autorização aplicada no **controller** via annotations.

---

## 9. Tratamento de Erros e Exceções

- Controllers não tratam exceções
- Services lançam exceções de negócio
- Um único handler global traduz para HTTP

(Regras detalhadas no Guia de Padrões Backend)

---

## 10. Logs e Observabilidade

- Logger padrão do Spring (SLF4J)
- Logs estruturados em JSON
- Logar apenas:

  - Eventos relevantes
  - Erros

---

## 11. Infraestrutura e Deploy

### Ambientes

- Dev: Docker Compose
- Prod: a definir

### CI/CD

- GitHub Actions
- Migrations automáticas

---

## 12. Qualidade de Código

- Testes unitários obrigatórios
- Code review obrigatório
- ADRs documentados em `/docs/adr`

---

## 13. Evolução Futura

- Extração do módulo `certification`
- Introdução de mensageria
- Escalabilidade horizontal
