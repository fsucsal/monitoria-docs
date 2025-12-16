# Validações — DTO x Domínio

Este documento define a **estratégia oficial de validação** no Sistema de Seleção de Monitoria — UCSal.

O objetivo é separar claramente:

- validações **técnicas/de formato**
- validações **de regras de negócio**

Isso evita duplicidade, confusão conceitual e código difícil de manter — problemas comuns em times iniciantes.

---

## Tipos de Validação

No sistema, existem **dois tipos distintos de validação**:

1. **Validação de Entrada (DTO)**
2. **Validação de Domínio (Service)**

Cada uma tem um papel específico.

---

## Validação no DTO (Bean Validation)

### Objetivo

Garantir que os dados recebidos pela API:

- não sejam nulos quando obrigatórios
- estejam no formato correto
- respeitem limites simples (tamanho, valor mínimo, regex)

> DTO valida **forma**, não **significado**.

---

### Onde aplicar

- Apenas em **DTOs de entrada**
- Usando Bean Validation (`jakarta.validation`)

Exemplo:

```java
public record EnrollmentRequest(
    @NotNull Long studentId,
    @NotNull Long offerSubjectId
) {}
```

---

### O que NÃO validar no DTO

- Quantidade máxima de inscrições
- Regras dependentes de banco de dados
- Regras envolvendo outros módulos
- Regras de negócio

---

## Validação de Domínio (Service)

### Objetivo

Garantir que as **regras do sistema** sejam respeitadas.

Essas validações:

- dependem de estado do sistema
- envolvem consultas ao banco
- envolvem outros módulos

---

### Onde aplicar

- Nos **Services**
- Antes de alterar estado do sistema

Exemplo:

```java
private void validateEnrollmentRules(Long studentId, Long offerSubjectId) {
    long active = monitoringReadService
        .countActiveMonitoringsByStudent(studentId);

    if (active >= 3) {
        throw new DomainRuleException("STUDENT_MAX_MONITORINGS_REACHED");
    }
}
```

---

## Entidades e Validação

### Entidades podem validar invariantes simples

Exemplo aceitável:

```java
public class Enrollment {

    public static Enrollment create(Long studentId, Long offerSubjectId) {
        if (studentId == null || offerSubjectId == null) {
            throw new IllegalArgumentException();
        }
        return new Enrollment(studentId, offerSubjectId);
    }
}
```

---

### O que entidades NÃO devem fazer

- Consultar banco de dados
- Acessar outros módulos
- Implementar regras complexas

> Entidades **não são services**.

---

## Usar Entidade Rica + Bean Validation?

### Resposta curta: **Não**.

Motivos:

- Duplica responsabilidades
- Confunde iniciantes
- Dificulta testes

Padrão adotado:

- DTO → Bean Validation
- Service → Regra de negócio
- Entidade → Invariantes simples

---

## Fluxo Completo de Validação

```text
Controller
  ↓
DTO (Bean Validation)
  ↓
Service (Regras de Domínio)
  ↓
Persistência
```

---

## Validação Entre Módulos

Quando uma validação depende de outro módulo:

- A regra fica no **service do módulo dono**
- Dados vêm via **interface pública**

Nunca:

- validar regras de outro módulo
- acessar repository externo

---

## 8. Erros de Validação

- Erros de DTO → `400 Bad Request`
- Erros de domínio → exceção de domínio

A tradução para HTTP ocorre no **Exception Handler Global**.

---

## Testes de Validação

- Validação de DTO → testes de controller
- Validação de domínio → testes de service

---

## Anti-patterns Comuns

- Regras de negócio em DTO
- Validação de formato no service
- Bean Validation em entidade JPA
- Regra duplicada em vários lugares

---

## Resumo

- DTO valida forma
- Service valida regra
- Entidade valida invariantes simples
- Cada validação no seu lugar

Esse padrão mantém o sistema simples, previsível e fácil de evoluir.
