# Logs e Observabilidade

Este documento define o **padrão oficial de logs e observabilidade** no Sistema de Seleção de Monitoria — UCSal.

O objetivo é garantir que o sistema seja:

- fácil de depurar
- observável em produção
- preparado para integração futura com ferramentas de análise

Tudo isso **sem adicionar complexidade desnecessária** para o time.

---

## Princípios Gerais

- Logs devem ser **estruturados desde o início**
- Todo módulo usa o **mesmo padrão de logger**
- Logs não substituem tratamento de erro
- Logs não devem vazar dados sensíveis

---

## Logger Padrão

Todos os componentes usam o logger padrão do projeto:

```java
private static final Logger log = LoggerFactory.getLogger(NomeDaClasse.class);
```

> Não criar wrappers ou abstrações próprias.

---

## Onde Logar

### Services

Services são o **principal ponto de logging**.

Logar:

- início de operações relevantes
- decisões importantes de negócio
- violações de regra de negócio

Exemplo:

```java
log.info("Starting enrollment for student {}", studentId);

if (active >= 3) {
    log.warn("Student {} reached max monitorings", studentId);
    throw new DomainRuleException("STUDENT_MAX_MONITORINGS_REACHED");
}
```

---

### Exception Handler Global

O handler global registra:

- exceções de domínio (`warn`)
- exceções técnicas (`error`)

```java
log.warn("Domain rule violated: {}", ex.getCode());
log.error("Unexpected error", ex);
```

---

### Onde NÃO Logar

- Controllers
- Repositories
- DTOs
- Entidades JPA

Motivo:

- evitar duplicidade
- evitar ruído

---

## Níveis de Log

| Nível | Uso                           |
| ----- | ----------------------------- |
| INFO  | Fluxos importantes do sistema |
| WARN  | Regras de negócio violadas    |
| ERROR | Falhas técnicas               |
| DEBUG | Apoio a desenvolvimento local |

---

## Logs Estruturados

Logs devem ser preparados para formato **JSON**.

Exemplo conceitual:

```json
{
  "level": "WARN",
  "module": "enrollment",
  "event": "DOMAIN_RULE_VIOLATION",
  "code": "STUDENT_MAX_MONITORINGS_REACHED",
  "timestamp": "2025-01-01T10:00:00"
}
```

> A estrutura será configurada pelo framework de logging.

---

## Dados Sensíveis

Nunca logar:

- senhas
- tokens JWT
- dados pessoais sensíveis

---

## Correlação de Logs

Sempre que possível:

- usar `requestId`
- propagar via MDC

Exemplo:

```java
MDC.put("requestId", requestId);
```

---

## Integração Futura

O padrão adotado permite integração com:

- ELK Stack
- Grafana Loki
- Datadog

Sem mudanças no código de negócio.

---

## Testes e Logs

- Logs não são testados
- Logs não controlam fluxo

---

## Anti-patterns Comuns

- Logar tudo
- Logar stacktrace para erro de negócio
- Criar log em controller
- Usar `System.out.println`

---

## Resumo

- Logger padrão único
- Services e handler são pontos de log
- Logs estruturados desde o início
- Preparado para produção
