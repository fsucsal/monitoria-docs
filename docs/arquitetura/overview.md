# Documento de Arquitetura de Software

**Sistema de Seleção de Monitoria — UCSal**

**Versão:** 1.0

**Data:** 04-12-2025

**Autor:** Anderson Vieira

---

## Visão Geral

Este documento descreve a arquitetura do Sistema de Seleção de Monitoria, cobrindo decisões arquiteturais, visão por camadas, componentes, integração, segurança, infraestrutura de execução e critérios não-funcionais. Tem como objetivo guiar a implementação e servir de referência para evolução e manutenção.

### Objetivos

- Fornecer uma visão técnica.
- Garantir alinhamento entre times (frontend, backend, infra, QA).
- Minimizar riscos de integração, performance e segurança.

### Contexto

O sistema é uma aplicação web acessada por Estudantes, Professores e Coordenadores, com responsabilidades de inscrição, seleção, gerenciamento de horas e emissão de certificados.

---

## Visão arquitetural

- **Estilo arquitetural:** Arquitetura modular baseada em DDD; Modular Monolith inicialmente com possibilidade de evolução para microsserviços por contexto.
- **Modelo de execução:** Deploy em contêineres Docker orquestrados via Docker Compose (estágio inicial) e Kubernetes (futuro).
- **APIs:** RESTful (OpenAPI/Swagger) para comunicação entre frontend e backend; eventos assíncronos (opcional) via message broker para tarefas demoradas (ex.: geração de certificados em lote).
- **Banco de dados:** PostgreSQL (principal). Redis para cache/locking e filas leves (opcional).
- **Autenticação:** JWT + OAuth2 (integração com SSO institucional futura).
- **Observabilidade:** Logs estruturados (JSON), métricas Prometheus, traces distribuídos (OpenTelemetry).

---

## Principais decisões arquiteturais

- **Modular Monolith com Boundaries por Contexto (DDD)**

  - Justificativa: reduzir complexidade inicial; manter separação lógica.

- **REST API com contrato OpenAPI**

  - Justificativa: permite desenvolvimento paralelo frontend/backend, documenta e permite geração de stubs.

- **PostgreSQL relacional**

  - Justificativa: integridade referencial e consultas relacionais para relatórios.

- **JWT para autenticação**

  - Justificativa: stateless, compatível com APIs e escalabilidade.

- **Docker para ambiente e deploys**

  - Justificativa: repetibilidade entre dev/staging/prod.

- **ADRs serão mantidos no repositório**

  - Justificativa: preservar razões das decisões.

---

## Módulos

> Implementar cada context como um módulo isolado no código, com camada Domain/Application/Infrastructure.

| Contextos               | Módulo        | Principais responsabilidades                                            |
| ----------------------- | ------------- | ----------------------------------------------------------------------- |
| Autenticação e Usuários | auth          | Login, roles, refresh token, integração SSO futura                      |
| Inscrição de Monitoria  | enrollment    | Formulário de inscrição, validações, estado da inscrição                |
| Seleção de Monitores    | selection     | Avaliação, ranking, designação de monitores por disciplina/turno        |
| Gestão da Monitoria     | monitoring    | Registro de atividades, controle de horas, validação pelas coordenações |
| Emissão de Certificados | certification | Geração PDF/assinatura digital (se houver), fila de geração             |
| Relatórios e Exportação | reports       | Endpoints de consulta, filtros, export CSV/PDF                          |
| Infra & Integrations    | infra         | Integração com storage, e-mail, SSO, mensageria                         |

Cada módulo deverá expor **APIs internas** (funções/serviços) e **contratos DTO** usados por outros módulos.

---

## Estrutura de Código

```
backend/
├─ src/
│  ├─ auth/
│  │  ├─ domain/
│  │  ├─ application/
│  │  └─ infra/
│  ├─ enrollment/
│  ├─ selection/
│  ├─ monitoring/
│  ├─ certification/
│  ├─ reports/
│  └─ shared/
│     ├─ db/
│     ├─ http/
│     ├─ utils/
│     └─ dtos/
├─ infra/
│  ├─ docker/
├─ tests/
└─ openapi.yaml
```

**Observações:**

- `shared` contém utilitários reutilizáveis e adaptadores (p.ex.: mapeadores, exceptions, middlewares).
- Cada módulo deve ter testes unitários e de integração localizados em `tests/<module>`.

---

## Modelagem de dados

> Aqui apresentamos os principais modelos (sintético).

### Courses

Representa os cursos da instituição.

| Campo         | Tipo    | Descrição            |
| ------------- | ------- | -------------------- |
| id            | PK      | Identificador único  |
| name          | varchar | Nome do curso        |
| internal_code | varchar | Código institucional |

---

### Shifts

Representa o período do dia em que a turma ocorre.

| Campo | Tipo    | Descrição                     |
| ----- | ------- | ----------------------------- |
| id    | PK      | Identificador único           |
| name  | varchar | Matutino, Vespertino, Noturno |

---

### Classes

Representa uma turma específica dentro de um curso e turno.

| Campo     | Tipo       | Descrição              |
| --------- | ---------- | ---------------------- |
| id        | PK         | Identificador único    |
| name      | varchar    | A, B, C, etc           |
| course_id | FK → CURSO | Curso ao qual pertence |
| shift_id  | FK → TURNO | Turno da turma         |

---

### Subjects

Representa a disciplina em sua forma **genérica**, sem vínculo com curso, turno ou turma.

| Campo         | Tipo    | Descrição            |
| ------------- | ------- | -------------------- |
| id            | PK      | Identificador único  |
| name          | varchar | Nome da disciplina   |
| internal_code | varchar | Código institucional |

---

### OffersSubjects

Representa uma disciplina sendo ministrada para uma turma específica, em determinado semestre e por um professor.

| Campo      | Tipo            | Descrição             |
| ---------- | --------------- | --------------------- |
| id         | PK              | Identificador único   |
| subject_id | FK → DISCIPLINA | Disciplina base       |
| class_id   | FK → TURMA      | Turma                 |
| semester   | varchar         | Ex: 2025.1            |
| teacher_id | FK → USUARIO    | Professor responsável |

### Enrollments

Representa a candidatura do estudante a uma monitoria.

| Campo            | Tipo                      | Descrição                     |
| ---------------- | ------------------------- | ----------------------------- |
| id               | PK                        | Identificador único           |
| student_id       | FK → USUARIO              | Estudante                     |
| offer_subject_id | FK → OFERTA_DE_DISCIPLINA | Contexto da monitoria         |
| status           | enum                      | PENDENTE, APROVADO, REPROVADO |
| enrolled_at      | date                      | Data da inscrição             |

---

### Monitoring

Representa o vínculo efetivo do estudante como monitor.

| Campo            | Tipo                      | Descrição           |
| ---------------- | ------------------------- | ------------------- |
| id               | PK                        | Identificador único |
| teacher_id       | FK → USUARIO              | Monitor             |
| offer_subject_id | FK → OFERTA_DE_DISCIPLINA | Contexto            |
| started_at       | date                      | Início              |
| ended_at         | date                      | Término             |

---

### Activities

Representa o registro diário/semanal da atuação do monitor.

| Campo          | Tipo           | Descrição                                 |
| -------------- | -------------- | ----------------------------------------- |
| id             | PK             | Identificador único                       |
| monitoring_id  | FK → MONITORIA | Vínculo                                   |
| date           | date           | Data da atividade                         |
| total_workload | decimal        | Quantidade de horas dedicadas à monitoria |
| description    | text           | Descrição da atividade                    |

---

### Certificates

Documento final emitido ao término da monitoria.

| Campo           | Tipo           | Descrição           |
| --------------- | -------------- | ------------------- |
| id              | PK             | Identificador único |
| monitoring_id   | FK → MONITORIA | Monitoria vinculada |
| total_workload  | decimal        | Total de horas      |
| issue_date      | date           | Data do certificado |
| validation_code | varchar        | Código de validação |

---

## Contrato de APIs (visão resumida)

- **/api/identity/** - register, login, refresh, logout
- **/api/enrollments/** - CRUD inscrições, status
- **/api/selection/** - endpoints para professores avaliarem e designarem
- **/api/monitoring/** - CRUD registros de horas
- **/api/certificates/** - solicitar e consultar certificados
- **/api/reports/** - gerar e exportar relatórios

Todos os endpoints documentados em OpenAPI; usar versionamento `/api/v1/...`.

---

## Segurança e Controle de Acesso

### Autenticação

- JWT com `access_token` (curto) + `refresh_token`.

### Autorização (RBAC mínimo)

- Perfis: `ROLE_STUDENT`, `ROLE_TEACHER`, `ROLE_COORDINATOR`, `ROLE_ADMIN`.
- Políticas por endpoint:

  - Inscrição: STUDENT
  - Avaliação/Selection: TEACHER
  - Emissão de Certificados: COORDINATOR

### Práticas de segurança

- HTTPS obrigatório
- Validação e sanitização de entrada (SQL injection, XSS)
- Rate limiting em endpoints críticos ou via proxy gateway
- Hash de senhas com para armazenamento de credenciais e dados sensíveis

---

## Integrações Externas

- **Storage (arquivos/certificados):** a definir
- **E-mail:** provedor SMTP para notificações
- **Mensageria:** Redis (certificados, relatórios pesados)

---

## Infraestrutura e deploy

### Ambiente inicial

- **Dev:** Docker Compose
- **Staging/Prod:** a definir
- **DB:** PostgreSQL gerenciado ou em container
- **Storage:** a definir

### CI/CD

- Pipelines GitHub Actions
- Migrations automatizadas via tool (Flyway por exemplo)

### Configuração

- Segredos em Vault em prod ou staging
- Configurações por env vars em ambiente local

---

## Observabilidade

- **Logs:** JSON estruturado, ELK-compatible stack ou Grafana Loki
- **Métricas:** Prometheus + Grafana (counters, histograms para latência)
- **Tracing:** OpenTelemetry para identificar latência em chamadas críticas
- **Alerting:** thresholds para erros, latência, uso de memória, disco e processador

---

## Performance e Escalabilidade

- **SLA desejado:** tempo de resposta < 2s para operações comuns
- **Escala vertical e horizontal:** serviços stateless (API) escaláveis horizontalmente; DB verticalmente e read-replicas para leitura intensiva
- **Cache:** Redis para consultas frequentes e sessões curtas

---

## Qualidade de código e padrões

- **Linters:** Checkstyle/SpotBugs (Java)
- **Code review obrigatório** com PRs
- **Documentar ADRs** no diretório `/docs/adr`
- **Dependabot / Renovate** para gerenciar dependências

---

## Backup e disaster recovery

- Backups diários do PostgreSQL com retenção configurável
- Estratégia de restore testada trimestralmente
- Storage versioning para arquivos importantes (certificados)

---

## Plano de migração para microsserviços

- Extrair módulo `certification`
- Usar eventos assíncronos para sincronização de estados entre serviços
