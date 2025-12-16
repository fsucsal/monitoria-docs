# Antipadrões de Desenvolvimento Backend

Este documento descreve **práticas proibidas (anti‑patterns)** no backend do Sistema de Seleção de Monitoria — UCSal.

O objetivo é **evitar erros comuns em times iniciantes**, reduzir retrabalho e preservar a arquitetura proposta.

> Se você sentir necessidade de aplicar algum dos itens abaixo, **pare e revise o guia** antes de continuar.

---

## God Service (Service Gigante)

### O problema

Um único service contendo:

- Muitas responsabilidades
- Regras não relacionadas
- Fluxos complexos

### Por que evitar

- Código difícil de entender
- Testes grandes e frágeis
- Alto acoplamento

### Correto

- Um service por **caso de uso**
- Responsabilidade clara
- Métodos pequenos

---

## Validação de Regra de Negócio no Controller

### O problema

Validar regras como:

- Limite de inscrições
- Estado permitido
- Regras transversais

Diretamente no controller.

### Por que evitar

- Duplica regras
- Controller vira regra de negócio
- Difícil testar

### Correto

- Controller valida apenas **estrutura** (DTO)
- Service valida **regras de negócio**

---

## Acessar Repositório de Outro Módulo

### O problema

Um módulo acessa diretamente:

- Repository
- Entity

De outro módulo.

### Por que evitar

- Viola encapsulamento
- Cria dependências ocultas
- Quebra modularidade

### Correto

Usar **interfaces públicas** expostas pelo módulo dono do dado.

---

## Criar Exceções em Excesso

### O problema

Criar uma classe de exceção para cada regra de negócio.

### Por que evitar

- Explosão de classes
- Handler gigantesco
- Difícil padronização

### Correto

- Usar uma exceção base de domínio
- Diferenciar regras por **código de erro**

---

## Traduzir Mensagens no Backend

### O problema

Backend retornando mensagens prontas para UI:

- Em português
- Com tom de UX

### Por que evitar

- Impede i18n
- Acopla backend à UI

### Correto

Backend retorna:

- Código de erro
- Contexto

Frontend traduz e apresenta.

---

## Logger em Todo Lugar Sem Critério

### O problema

Logs:

- Em controllers
- Em entidades
- Em getters

### Por que evitar

- Poluição de logs
- Dificulta análise

### Correto

Logar apenas:

- Entrada de caso de uso (service)
- Erros no handler global

---

## Testar Framework em vez de Regra

### O problema

Testes que apenas verificam:

- Getters
- Setters
- Mapeamento automático

### Por que evitar

- Baixo valor
- Alto custo

### Correto

Testar:

- Regras de negócio
- Comportamento esperado

---

## Uso Indevido de @Transactional

### O problema

Anotar:

- Controllers
- Métodos utilitários

### Por que evitar

- Transações longas
- Comportamento imprevisível

### Correto

- Usar @Transactional apenas em services
- Apenas onde há escrita

---

## Shared como Lixeira

### O problema

Colocar qualquer coisa em `shared`:

- Regra de negócio
- Lógica específica

### Por que evitar

- Shared vira módulo oculto
- Perda de ownership

### Correto

Shared contém apenas:

- Infraestrutura
- Utilitários genéricos
- Tipos transversais

---

## Considerações Finais

Evitar esses antipadrões garante:

- Código previsível
- Arquitetura sustentável
- Facilidade de evolução

> **Quando em dúvida, prefira simplicidade e clareza.**
