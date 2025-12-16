# Desenvolvimento Backend — Visão Geral

Este espaço reúne as **diretrizes oficiais de desenvolvimento backend** do Sistema de Seleção de Monitoria da UCSal.

O objetivo é garantir que todo o código produzido:

- seja **legível**, **organizado** e **previsível**;
- mantenha **qualidade compatível com ambiente de produção**, mesmo em um contexto acadêmico;
- possa ser entendido, mantido e evoluído por diferentes pessoas ao longo do tempo.

Este material segue práticas adotadas em projetos profissionais, evitando tanto a informalidade excessiva quanto complexidade desnecessária.

---

## Objetivo deste guia

O **Guia de Padrões de Desenvolvimento Backend** define **como o código deve ser escrito**, e não apenas _o que o sistema faz_.

Ele serve como:

- **Referência oficial** de implementação
- **Material de apoio** para aprendizado
- **Base para revisão de código (code review)**
- **Fundação do boilerplate** inicial do projeto

> Se um padrão não estiver documentado aqui, ele **não deve ser adotado sem alinhamento prévio**.

---

## O que este guia cobre

Os documentos desta seção abordam, entre outros pontos:

- Estrutura de módulos e camadas
- Responsabilidades de controllers, services e repositories
- Estratégias de validação (entrada vs regras de negócio)
- Comunicação entre módulos
- Tratamento padronizado de erros e exceções
- Boas práticas de injeção de dependência
- Padrões de nomenclatura
- Estratégia de testes
- Uso de logs e observabilidade básica
- Anti-patterns comuns em times iniciantes

---

## O que este guia **não** cobre

Para manter o foco e a didática, este guia **não** aborda:

- Decisões macro de arquitetura (ver seção **Arquitetura**)
- Configuração de infraestrutura e deploy
- Detalhes avançados de DDD, CQRS ou Event Sourcing
- Otimizações prematuras ou abstrações complexas

Esses temas são tratados em documentos específicos ou poderão ser introduzidos conforme a maturidade do time.

---

## Princípios adotados

As diretrizes aqui descritas seguem alguns princípios centrais:

- **Simplicidade antes de sofisticação**
- **Código explícito é melhor que código “esperto”**
- **Regras de negócio ficam no service**
- **Controllers são finos**
- **Entidades não acessam serviços ou repositórios**
- **Erros são tratados de forma centralizada**
- **Testes unitários são a base da qualidade**

---

## Público-alvo

Este guia se destina a:

- Estudantes desenvolvedores do projeto
- Monitores e líderes técnicos
- Professores orientadores
- Qualquer pessoa que venha a contribuir com o backend

Todos devem seguir os padrões aqui descritos para garantir consistência e qualidade no código-base.

---

## Como usar este material

- Leia esta seção antes de escrever código
- Consulte os guias específicos ao implementar uma feature
- Use os exemplos como referência, não como “receita cega”
- Em caso de dúvida, **priorize o padrão documentado**

---

## Organização da seção

Os documentos de desenvolvimento estão organizados da seguinte forma:

- **Guia de Padrões Backend** — regras práticas de implementação
- **Estrutura do Projeto** — organização de pastas e módulos
- **Tratamento de Erros** — exceções, respostas HTTP e padronização
- **Testes** — estratégia e exemplos
- **Observações e Anti-patterns** — erros comuns a evitar

---

Este material evoluirá junto com o projeto. Alterações relevantes devem ser discutidas e documentadas para manter o alinhamento do time.
