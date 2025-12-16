# Services — Regras de Negócio

Este documento define o **padrão oficial para implementação de Services** no Sistema de Seleção de Monitoria — UCSal.

Os Services representam a **camada central do sistema**, onde vivem as **regras de negócio**, validações de domínio e orquestração entre módulos.

---

## Papel do Service

O Service é responsável por:

- Implementar regras de negócio do módulo
- Coordenar operações entre repositórios
- Consumir serviços públicos (interfaces) de outros módulos
- Controlar transações
- Lançar exceções de domínio

O Service **não deve**:

- Receber ou devolver entidades JPA diretamente para controllers
- Conter lógica de apresentação
- Fazer validações de formato (null, tamanho, regex)

---

## Onde o Service Vive

Cada módulo possui sua camada de aplicação:

```
enrollment/
 ├─ application/
 │  └─ EnrollmentService.java
```

> Services **nunca ficam em `shared`**.

---

## Estrutura Básica de um Service

```java
@Service
public class EnrollmentService {

    private final EnrollmentRepository enrollmentRepository;
    private final MonitoringReadService monitoringReadService;

    public EnrollmentService(
        EnrollmentRepository enrollmentRepository,
        MonitoringReadService monitoringReadService
    ) {
        this.enrollmentRepository = enrollmentRepository;
        this.monitoringReadService = monitoringReadService;
    }

    @Transactional
    public void enrollStudent(Long studentId, Long offerSubjectId) {
        validateBusinessRules(studentId, offerSubjectId);

        Enrollment enrollment = Enrollment.create(studentId, offerSubjectId);
        enrollmentRepository.save(enrollment);
    }
}
```

---

## Regras de Negócio

### Onde ficam as regras

Regras de negócio ficam:

- No **Service** (orquestração)
- Opcionalmente em métodos da entidade (validações internas)

Exemplo:

```java
private void validateBusinessRules(Long studentId, Long offerSubjectId) {
    long activeMonitorings = monitoringReadService
        .countActiveMonitoringsByStudent(studentId);

    if (activeMonitorings >= 3) {
        throw new DomainRuleException("STUDENT_MAX_MONITORINGS_REACHED");
    }
}
```

---

## Comunicação com Outros Módulos

- Services **não acessam repositórios externos**
- Comunicação ocorre apenas via **interfaces públicas**

Exemplo:

```java
public interface MonitoringReadService {
    long countActiveMonitoringsByStudent(Long studentId);
}
```

---

## 6. Transações

### 6.1 Regra Geral

- Transações são controladas **no Service**
- Controllers não usam `@Transactional`

```java
@Transactional
public void approveEnrollment(Long enrollmentId) {
    ...
}
```

### Operações Read-only

```java
@Transactional(readOnly = true)
public EnrollmentDetails findById(Long id) {
    ...
}
```

---

## Exceções no Service

Services lançam **exceções de domínio**, nunca exceções HTTP.

Exemplo:

```java
throw new DomainRuleException("ENROLLMENT_ALREADY_EXISTS");
```

A tradução para HTTP ocorre no **Exception Handler Global**.

---

## Logs no Service

Services utilizam o **logger padrão** para registrar eventos relevantes:

```java
log.info("Student {} enrolled in offer {}", studentId, offerSubjectId);
```

Logs devem registrar:

- Início de operações importantes
- Violações de regra de negócio

---

## Testes de Service

Services devem ser **amplamente testados**:

- Regras de negócio
- Fluxos felizes
- Cenários de erro

Exemplo:

```java
@Test
void shouldNotAllowMoreThanThreeMonitorings() {
    when(monitoringReadService.countActiveMonitoringsByStudent(1L))
        .thenReturn(3L);

    assertThrows(DomainRuleException.class, () ->
        service.enrollStudent(1L, 10L)
    );
}
```

---

## Anti-patterns em Services

- Service acessando repository de outro módulo
- Service retornando entidade JPA
- Regras de negócio no controller
- Service com lógica de UI

---

## Resumo

- Service é o coração do sistema
- Regras de negócio vivem aqui
- Comunicação entre módulos ocorre via interfaces
- Transações e exceções são responsabilidade do Service
