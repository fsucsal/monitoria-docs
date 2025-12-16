# Estrutura do Projeto

Este documento descreve a **estrutura oficial de pastas e pacotes** do backend do Sistema de Seleção de Monitoria — UCSal.

O objetivo é garantir:

- Organização previsível do código
- Facilidade de aprendizado para desenvolvedores iniciantes
- Separação clara de responsabilidades
- Evolução segura do sistema sem refatorações estruturais

> Esta estrutura é **obrigatória** no projeto. Novos módulos e pacotes devem seguir exatamente o padrão descrito aqui.

---

## Visão Geral da Estrutura

O projeto segue o modelo de **Monólito Modular**, organizado por **módulos funcionais**, e não por camadas globais.

Cada módulo contém suas próprias camadas internas (controller, service, repository, etc.).

Estrutura geral:

```
backend/
├── src/
│   ├── auth/
│   ├── enrollment/
│   ├── selection/
│   ├── monitoring/
│   ├── certification/
│   ├── reports/
│   └── shared/
│       ├── config/
│       ├── exception/
│       ├── logging/
│       ├── security/
│       └── utils/
├── resources/
│   ├── application.yml
│   └── db/migration/
├── test/
└── pom.xml
```

---

## Organização por Módulo

Cada módulo representa um **contexto funcional isolado**.

Exemplo (`enrollment`):

```
enrollment/
├── controller/
├── service/
├── repository/
├── domain/
│   └── entity/
├── dto/
└── mapper/
```

### Responsabilidade de cada pasta

| Pasta         | Responsabilidade                  |
| ------------- | --------------------------------- |
| controller    | Exposição da API REST             |
| service       | Regras de negócio                 |
| repository    | Acesso a dados (JPA)              |
| domain/entity | Entidades JPA                     |
| dto           | Objetos de entrada e saída da API |
| mapper        | Conversão entre Entity ↔ DTO      |

---

## Controllers

Local:

```
<modulo>/controller
```

Regras:

- Um controller por recurso
- Controllers **não contêm regra de negócio**
- Controllers dependem apenas de services
- Controllers usam apenas DTOs

Exemplo de nome:

```
EnrollmentController
```

---

## Services

Local:

```
<modulo>/service
```

Regras:

- Contêm **todas as regras de negócio**
- Coordenam validações, persistência e integrações
- Podem depender de outros services **via interface**

Exemplo:

```
EnrollmentService
```

---

## Repositories

Local:

```
<modulo>/repository
```

Regras:

- Apenas acesso a dados
- Nunca contém regra de negócio
- Estende `JpaRepository`
- Nunca é acessado fora do módulo

Exemplo:

```
EnrollmentRepository
```

---

## Entidades JPA

Local:

```
<modulo>/domain/entity
```

Regras:

- Representam o modelo persistente
- Contêm:

  - Mapeamentos JPA
  - Regras simples de consistência

- **Não contêm validações de negócio complexas**

Exemplo:

```
Enrollment
```

> As regras de negócio ficam nos services, não nas entidades.

---

## DTOs

Local:

```
<modulo>/dto
```

Tipos comuns:

- `CreateRequest`
- `UpdateRequest`
- `Response`

Regras:

- DTOs de request usam Bean Validation
- DTOs de response nunca expõem entidades

---

## Mapper

Local:

```
<modulo>/mapper
```

Responsabilidade:

- Converter Entity ↔ DTO
- Centralizar transformação de dados

Pode ser:

- Manual
- MapStruct (se adotado)

---

## Módulo Shared

Local:

```
shared/
```

Responsabilidade:

- Código transversal
- Infraestrutura
- Configurações

Contém:

- Tratamento global de exceções
- Logger padrão
- Configuração de segurança
- Utilitários genéricos

> **Nunca** colocar regra de negócio no `shared`.

---

## Testes

Estrutura espelha o código principal:

```
src/test/java/
└── enrollment/
    └── service/
```

Regras:

- Testar principalmente services
- Controllers apenas quando necessário
- Repositórios com testes simples ou integração

---

## Convenções Gerais

- Um pacote = uma responsabilidade
- Evitar pacotes genéricos como `impl`, `util` dentro de módulos
- Nomes claros e explícitos

---

## Considerações Finais

Esta estrutura foi pensada para:

- Clareza de responsabilidades
- Facilidade de manutenção

Qualquer exceção deve ser discutida e documentada via ADR.
