# Controllers — Camada de Entrada da Aplicação

Este documento define o **padrão oficial para implementação da camada de Controllers** no backend do Sistema de Seleção de Monitoria — UCSal.

Controllers representam a **porta de entrada da aplicação**, sendo responsáveis apenas por adaptar o mundo HTTP para o domínio.

> Controllers **não contêm regras de negócio**.

---

## Papel do Controller na Arquitetura

O Controller é responsável por:

- receber requisições HTTP;
- validar estrutura e formato dos dados de entrada;
- delegar a execução para um Service;
- devolver respostas HTTP apropriadas.

O Controller **não decide nada** sobre regras de negócio.

---

## Responsabilidades do Controller

Um Controller **deve**:

- mapear endpoints HTTP;
- validar DTOs de entrada;
- converter dados de entrada em comandos para o Service;
- retornar DTOs de resposta;
- definir status HTTP de sucesso.

---

## O que NÃO é responsabilidade do Controller

Um Controller **não deve**:

- conter regras de negócio;
- acessar repositories;
- realizar validações de domínio;
- lançar exceções HTTP manualmente;
- conter lógica condicional complexa.

---

## Validação no Controller

Controllers realizam **validação estrutural**, como:

- campos obrigatórios;
- formatos;
- tamanhos máximos;
- tipos de dados.

Validações são feitas via DTOs e anotações.

---

## Tratamento de Exceções

Controllers **não tratam exceções de negócio**.

Qualquer exceção lançada por um Service é propagada e tratada pelo **handler global**.

O Controller apenas define respostas de sucesso.

---

## Retorno de Dados

Controllers retornam **DTOs**, nunca entidades do domínio.

Regras:

- DTOs de entrada são separados dos DTOs de saída;
- nenhuma entidade JPA é exposta diretamente.

---

## Organização dos Controllers

Estrutura recomendada:

```
<modulo>/
 └─ controller/
    └─ <Modulo>Controller.java
```

Um controller deve representar um **recurso HTTP claro**.

---

## Considerações Finais

Controllers devem ser **simples, finos e previsíveis**.

Se um controller começa a crescer ou conter lógica condicional relevante, isso é um sinal claro de que a regra pertence ao Service.
