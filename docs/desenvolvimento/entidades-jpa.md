# Entidades JPA

Este documento define os **padrões oficiais para modelagem de entidades JPA** no backend do Sistema de Seleção de Monitoria — UCSal.

O objetivo é garantir entidades **simples, previsíveis e focadas em persistência**, sem acoplamento indevido com regras de negócio ou camadas superiores.

> Entidades representam **estado persistido**, não regras de negócio nem fluxos de aplicação.

---

## Princípios Gerais

As entidades devem:

- Representar **estrutura de dados persistidos**
- Possuir **identidade própria**
- Ser simples e previsíveis
- Não concentrar regras de negócio
- Ser independentes de controllers, DTOs e services

---

## O que uma Entidade PODE Conter

- Campos persistidos (estado)
- Métodos de domínio simples
- Regras de consistência interna
- Transições de estado válidas

Exemplos de regras permitidas:

- Status inválido
- Datas inconsistentes
- Mudança de estado não permitida

---

## O que uma Entidade NÃO PODE Conter

- Chamadas a serviços
- Acesso a repositórios
- Validações que dependem de banco de dados
- Regras que dependem de outros módulos
- Lógica de fluxo de aplicação

> Regras que exigem consulta externa pertencem ao **Service**.

---

## Entidades Simples (Posicionamento do Projeto)

Este projeto **não utiliza DDD** e **não adota entidades ricas**.

As entidades são **intencionalmente simples**, com foco em:

- Persistência
- Estrutura de dados
- Relacionamentos

Toda **regra de negócio vive nos Services**.

Esse modelo é adotado para:

- Facilitar o aprendizado
- Reduzir complexidade
- Evitar abstrações desnecessárias

---

## Estrutura Recomendada

Padrões estruturais:

- Campos privados
- Construtor protegido
- Criação via método estático ou factory
- Métodos expressivos de domínio

Evitar:

- Setters públicos genéricos
- Modificação direta de estado fora do domínio

---

## Identidade e Igualdade

- Entidades possuem **identidade única**
- `equals` e `hashCode` devem usar apenas o `id`
- Nunca usar todos os atributos

Motivo:

- Entidade pode mudar de estado
- Identidade deve permanecer estável

---

## Relacionamentos JPA

### Diretrizes

- Preferir `@ManyToOne`
- Evitar `@ManyToMany`
- Usar `fetch = LAZY`
- Evitar cascatas agressivas

Relacionamentos devem refletir o **domínio**, não conveniência técnica.

---

### Coleções

- Inicializar coleções
- Evitar setters públicos
- Manipular via métodos de domínio

---

## 8. Uso de Enums

Enums devem representar **estados finitos e explícitos**.

Exemplos:

- Status de inscrição
- Estado da monitoria

Evitar:

- Strings mágicas
- Inteiros sem significado semântico

---

## Validações Dentro da Entidade

### Permitido (técnico)

- `@NotNull`, `@Column(nullable = false)`
- Restrições simples de integridade

### Proibido (negócio)

- Limites de inscrições
- Regras transversais
- Dependência de outros módulos

Essas validações pertencem **exclusivamente aos Services**.

---

## Entidade ≠ DTO

- Entidade representa o domínio
- DTO representa comunicação
- Nunca reutilizar entidade como request ou response

Motivos:

- Vazamento de regras internas
- Acoplamento indevido

---

## Persistência e Infraestrutura

- Anotações JPA são aceitáveis
- Entidade não conhece SQL
- Não conter lógica de persistência manual

---

## Considerações Finais

Boas entidades JPA são:

- Pequenas
- Coesas
- Expressivas
- Estáveis ao longo do tempo

> **Quando em dúvida, prefira clareza ao excesso de abstração.**
