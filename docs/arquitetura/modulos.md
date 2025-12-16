# Organização dos Módulos

Este documento descreve a **organização modular** do Sistema de Seleção de Monitoria — UCSal.

O objetivo é apresentar **quais módulos existem, suas responsabilidades e como eles podem interagir**, garantindo baixo acoplamento e clareza para evolução do sistema.

> Detalhes de implementação (services, controllers, exceções, validações) estão descritos no **Guia de Padrões de Desenvolvimento Backend**.

---

## 1. Princípios de Modularização

A aplicação segue o modelo de **Monólito Modular**, adotando os seguintes princípios:

- Cada módulo representa um **contexto funcional claro**
- Módulos são isolados por pacote
- Regras de negócio ficam **dentro do módulo dono da regra**
- Comunicação entre módulos ocorre apenas por **interfaces explícitas**

---

## 2. Regras de Interação Entre Módulos

### 2.1 Regras Permitidas

- Um módulo pode **consumir serviços públicos (interfaces)** de outro módulo
- Um módulo pode depender de **DTOs expostos** por outro módulo
- Dependências devem ser **unidirecionais**

### 2.2 Regras Proibidas

- Acessar diretamente **repositórios** de outro módulo
- Acessar **entidades JPA** internas de outro módulo
- Compartilhar lógica de negócio via `shared`
- Dependências circulares entre módulos

---

## 3. Comunicação Entre Módulos

### 3.1 Serviços Públicos (Read-only)

Quando um módulo precisa **consultar informações** de outro módulo, deve fazê-lo via **interface pública**.

Exemplo conceitual:

- `enrollment` precisa saber quantas inscrições um aluno possui
- `enrollment` consome uma interface exposta por `monitoring`

> Interfaces públicas devem ser **estáveis e simples**.

---

### 3.2 Regras de Negócio Transversais

Quando uma regra envolve mais de um módulo:

- A regra deve residir no **módulo dono da ação**
- Outros módulos apenas fornecem dados

Exemplo:

> "Aluno só pode se inscrever em até 3 monitorias"

- A regra pertence ao módulo `enrollment`
- Dados sobre monitorias vêm de `monitoring`

---

## 4. Lista de Módulos

### 4.1 auth

**Responsabilidade:**

- Autenticação
- Emissão e validação de JWT
- Gestão de perfis e roles

Não contém regras de negócio do domínio de monitoria.

---

### 4.2 enrollment

**Responsabilidade:**

- Inscrição de alunos
- Validações de regras de inscrição
- Estado da inscrição

É o **dono das regras relacionadas à candidatura**.

---

### 4.3 selection

**Responsabilidade:**

- Avaliação de inscrições
- Ranking
- Seleção de monitores

---

### 4.4 monitoring

**Responsabilidade:**

- Gestão do vínculo de monitoria
- Registro de atividades
- Controle de carga horária

---

### 4.5 certification

**Responsabilidade:**

- Emissão de certificados
- Consolidação de carga horária

---

### 4.6 reports

**Responsabilidade:**

- Consultas
- Relatórios
- Exportações

Não contém regras de negócio críticas.

---

### 4.7 shared

**Responsabilidade:**

- Código transversal
- Infraestrutura
- Tipos genéricos

> O módulo `shared` **nunca contém regras de negócio**.

---

## 5. Evolução dos Módulos

A modularização atual permite:

- Extração futura de módulos como serviços independentes
- Introdução de mensageria
- Escalabilidade sem refatorações estruturais

Módulos candidatos à extração:

- `certification`
- `reports`

---

## 6. Considerações Finais

Esta organização visa:

- Facilitar o entendimento do sistema
- Reduzir acoplamento
- Guiar decisões de implementação

Qualquer exceção às regras aqui descritas deve ser documentada via ADR.
