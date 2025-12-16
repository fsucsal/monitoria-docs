# Guia de Padrões de Desenvolvimento Backend

Este documento define os **padrões oficiais de desenvolvimento backend** do Sistema de Seleção de Monitoria da UCSal.

Ele estabelece **como o código deve ser escrito**, organizado e validado, servindo como referência obrigatória para todos os desenvolvedores do projeto.

O objetivo é garantir:

- qualidade de código compatível com ambiente de produção;
- simplicidade e previsibilidade para desenvolvedores;
- facilidade de manutenção e evolução do sistema.

---

## Escopo do guia

Este guia cobre:

- organização geral do backend
- responsabilidades de cada camada
- princípios de validação
- regras de negócio
- comunicação entre módulos
- tratamento de erros
- logging
- testes
- práticas a evitar

Detalhes específicos de cada tema são aprofundados nos documentos complementares desta seção.

---

## Princípios gerais

Os seguintes princípios **não são negociáveis** no projeto:

### Controllers são finos

**Controllers devem:**

- receber a requisição HTTP;
- validar dados de entrada (DTO);
- delegar a lógica para o service;
- retornar a resposta HTTP.

**Controllers **não** devem conter:**

- regras de negócio;
- acesso direto a repositories;
- lógica condicional complexa.

---

### Regras de negócio vivem no Service

Toda regra de negócio deve estar na camada de **service**.

Exemplos de regras de negócio:

- aluno só pode se inscrever em até 3 monitorias;
- não permitir inscrição duplicada;
- impedir alteração de uma inscrição já aprovada.

Services:

- orquestram entidades e outros serviços;
- aplicam regras de domínio;
- lançam exceções de negócio quando necessário.

---

### Validação é feita em dois níveis

A validação é dividida claramente:

| Tipo de validação | Onde ocorre           | Exemplo                    |
| ----------------- | --------------------- | -------------------------- |
| Estrutural        | DTO (Bean Validation) | campo obrigatório, formato |
| Regra de negócio  | Service               | limite de inscrições       |

**Nunca misturar validação de entrada com regra de negócio.**

---

### Entidades não conhecem serviços nem repositórios

Entidades JPA:

- representam o estado do domínio;
- podem conter comportamentos simples;
- **não** acessam serviços, repositories ou APIs externas.

Isso garante:

- baixo acoplamento;
- facilidade de teste;
- clareza de responsabilidades.

---

### Comunicação entre módulos é controlada

Em um **monólito modular**, módulos:

- não acessam repositories de outros módulos;
- não dependem de entidades internas de outros módulos;
- comunicam-se via **interfaces de serviço** bem definidas.

Dependências entre módulos devem ser explícitas e unidirecionais.

---

### Exceções representam falhas de regra ou uso inválido

Exceções não são erros técnicos apenas.

No projeto:

- exceções de domínio indicam violação de regra de negócio;
- exceções técnicas indicam falhas de infraestrutura ou sistema.

Controllers **não** tratam exceções.
Toda tradução para HTTP ocorre em um handler global.

---

### Logs são responsabilidade do sistema, não do aluno

O projeto utiliza:

- logger padrão (SLF4J);
- logs estruturados;
- pontos de log bem definidos.

Logs devem existir principalmente:

- no service (operações relevantes);
- no handler global de exceções.

**Evitar logs em excesso ou em controllers.**

---

### Testes unitários são obrigatórios

A base da qualidade do sistema são **testes unitários**.

- Services devem ser testados;
- Regras de negócio devem ter testes;
- Controllers podem ser testados apenas quando necessário.

Testes de integração são usados com moderação.

---

## Organização por módulos

O sistema é organizado como um **monólito modular**, onde cada módulo representa um contexto do domínio.

Cada módulo deve conter suas próprias camadas:

- controller
- service
- repository
- domain (entidades, enums, exceções específicas)

Dependências cruzadas devem ser mínimas e controladas.

---

## Padrão de nomenclatura

Regras gerais:

| Elemento           | Padrão               |
| ------------------ | -------------------- |
| Controller         | `*Controller`        |
| Service            | `*Service`           |
| Repository         | `*Repository`        |
| DTO de request     | `*Request`           |
| DTO de response    | `*Response`          |
| Exceção de domínio | `*BusinessException` |

Nomes devem ser:

- claros;
- explícitos;
- evitar abreviações obscuras.

---

## O que evitar (anti-patterns)

Os seguintes padrões **não são permitidos**:

- regra de negócio em controller;
- repository sendo chamado fora do service;
- entidade acessando service;
- try/catch espalhado no código;
- exceções sendo tratadas manualmente em controllers;
- logs duplicados ou genéricos;
- validações de negócio em DTO.

Esses pontos são detalhados no documento de anti-patterns.

---

## Evolução do guia

Este guia:

- evolui junto com o projeto;
- pode ser atualizado conforme maturidade do time;
- deve refletir sempre o padrão **real** do código.

Alterações relevantes devem ser discutidas com a liderança técnica e documentadas.

---

## Leitura complementar

Para aprofundamento, consulte:

- Estrutura do Projeto
- Controllers REST
- Services e Regras de Negócio
- Validações
- Comunicação entre Módulos
- Tratamento de Erros
- Logs e Observabilidade
- Testes
- Anti-patterns

---

Este guia é a **fonte de verdade** para o desenvolvimento backend do projeto.
