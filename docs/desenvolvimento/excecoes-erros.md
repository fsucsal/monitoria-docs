# Exceções e Erros da API

Este documento define o **padrão oficial de tratamento de exceções e erros** no Sistema de Seleção de Monitoria — UCSal.

O objetivo é:

- manter regras de negócio limpas
- padronizar respostas da API
- facilitar integração com frontend
- evitar explosão de classes e mapeamentos

---

## Princípios Gerais

- Services lançam **exceções de domínio**
- Controllers **não tratam exceções**
- Existe **um único handler global**
- A API responde sempre em formato padronizado

> Exceções **não são controle de fluxo**.

---

## Tipos de Exceção

### Exceções Técnicas

Usadas para falhas inesperadas:

- erro de banco
- null explicito
- falha de infraestrutura

Exemplo:

```java
throw new IllegalStateException("Unexpected state");
```

---

### Exceções de Domínio

Representam **violação de regras de negócio**.

Características:

- lançadas apenas em Services
- não conhecem HTTP
- carregam um **código de erro**

---

## Exceção Base de Domínio

Todas as regras de negócio devem lançar uma exceção comum:

```java
public class DomainRuleException extends RuntimeException {

    private final String code;

    public DomainRuleException(String code) {
        super(code);
        this.code = code;
    }

    public String getCode() {
        return code;
    }
}
```

> Não criar uma classe por regra.

---

## Códigos de Erro

Cada regra de negócio define **apenas um código**:

Exemplos:

- `STUDENT_MAX_MONITORINGS_REACHED`
- `ENROLLMENT_ALREADY_EXISTS`
- `ENROLLMENT_NOT_FOUND`

Esses códigos:

- são estáveis
- são usados pelo frontend
- permitem tradução

---

## Categorias de Erro

A API trabalha com **categorias**, não com classes.

Exemplos:

- `DOMAIN_VALIDATION`
- `RESOURCE_NOT_FOUND`
- `UNAUTHORIZED`
- `TECHNICAL_ERROR`

---

## Modelo Padrão de Erro da API

```json
{
  "timestamp": "2025-01-01T10:00:00",
  "status": 400,
  "category": "DOMAIN_VALIDATION",
  "code": "STUDENT_MAX_MONITORINGS_REACHED",
  "message": "Business rule violated"
}
```

> `message` não é para usuário final.

---

## Exception Handler Global

Existe **um único handler**:

```java
@RestControllerAdvice
public class ApiExceptionHandler {

    @ExceptionHandler(DomainRuleException.class)
    public ResponseEntity<ApiError> handleDomain(DomainRuleException ex) {
        return ResponseEntity
            .badRequest()
            .body(ApiError.domain(ex.getCode()));
    }
}
```

---

## Preciso Mapear Todos os Erros?

### Não.

O handler mapeia:

- **tipos de exceção**
- não regras específicas

200 regras de negócio → **1 handler**.

---

## Tradução para UI

A API **não traduz mensagens**.

Fluxo:

- Backend retorna `code`
- Frontend traduz usando i18n

Exemplo:

```json
"STUDENT_MAX_MONITORINGS_REACHED": "Você já atingiu o limite de monitorias"
```

---

## Profundidade de Erro

- Backend retorna **código + categoria**
- Frontend decide como exibir

Nunca:

- mensagens longas
- stacktrace

---

## Logs e Exceções

- Exceções de domínio → `warn`
- Exceções técnicas → `error`

---

## Anti-patterns Comuns

- Criar exceção por regra
- Lançar HTTPException no service
- Traduzir erro no backend
- Vazar stacktrace

---

## Resumo

- DomainRuleException centraliza regras
- Handler mapeia categorias
- Frontend traduz
- Simples, escalável e previsível
