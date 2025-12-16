# Repositories — Acesso a Dados

Este documento define os **padrões de uso e implementação de repositórios** no Sistema de Seleção de Monitoria — UCSal.

O objetivo é garantir **consistência**, **baixo acoplamento** e **segurança de regras de negócio**, especialmente em um time com desenvolvedores iniciantes.

> Repositórios **não contêm regras de negócio**. Eles apenas acessam dados.

---

## Papel do Repositório

O repositório é responsável por:

- Persistir e recuperar entidades JPA
- Executar consultas simples ou customizadas
- Isolar a camada de negócio do mecanismo de persistência

O repositório **não deve**:

- Validar regras de domínio
- Tomar decisões de negócio
- Conhecer outros módulos
- Retornar DTOs de API

---

## Onde o Repositório Vive

Cada módulo possui seus próprios repositórios:

```
enrollment/
 ├─ domain/
 │  └─ Enrollment.java
 ├─ application/
 │  └─ EnrollmentService.java
 └─ infra/
    └─ EnrollmentRepository.java
```

> Repositórios **nunca** ficam em `shared`.

---

## Padrão de Implementação

Todos os repositórios devem:

- Estender `JpaRepository` ou `CrudRepository`
- Ser interfaces
- Usar nomes explícitos

Exemplo:

```java
public interface EnrollmentRepository extends JpaRepository<Enrollment, Long> {

    boolean existsByStudentIdAndOfferSubjectId(Long studentId, Long offerSubjectId);

    long countByStudentIdAndStatus(Long studentId, EnrollmentStatus status);
}
```

---

## Consultas Customizadas

### Derived Queries (Preferencial)

Sempre que possível, usar **derived queries** do Spring Data:

```java
List<Enrollment> findByStudentId(Long studentId);
```

### @Query (Quando necessário)

Use `@Query` apenas quando:

- A consulta não puder ser expressa pelo nome do método
- Houver necessidade real de join explícito

```java
@Query("""
    select count(e)
    from Enrollment e
    where e.student.id = :studentId
      and e.status = 'APPROVED'
""")
long countApprovedByStudent(@Param("studentId") Long studentId);
```

---

## Retorno do Repositório

Repositórios devem retornar:

- Entidades
- Tipos primitivos
- `Optional<T>` quando apropriado

**Nunca** retornar:

- DTOs de API
- Objetos de outro módulo

---

## Repositórios e Módulos

### Regra Fundamental

> Um módulo **nunca acessa o repositório de outro módulo**.

Exemplo incorreto:

```java
// enrollment acessando repository de monitoring
monitoringRepository.findByStudentId(...);
```

### Forma Correta

- Criar uma **interface pública** no módulo dono do dado
- O módulo consumidor depende da interface

Exemplo conceitual:

- `monitoring` expõe `MonitoringReadService`
- `enrollment` consome essa interface

---

## Repositórios Read-only

Quando um módulo precisa apenas **consultar dados**:

- A interface exposta deve ser **read-only**
- Não expor métodos de escrita

Exemplo de contrato:

```java
public interface MonitoringReadService {
    long countActiveMonitoringsByStudent(Long studentId);
}
```

---

## Testes de Repositório

### Quando testar

Testes de repositório são escritos quando:

- Há consultas customizadas (`@Query`)
- Há lógica SQL relevante

Não é necessário testar:

- Métodos simples do Spring Data

### Tipo de teste

- Testes de integração
- Banco em memória ou container

---

## Anti-patterns Comuns

- Repositório chamando outro repositório
- Repositório com regras de negócio
- Repositório retornando DTO
- Queries complexas espalhadas sem necessidade

---

## Resumo

- Repositório acessa dados
- Service aplica regras
- Controller expõe API
- Módulos se comunicam por interfaces

Este padrão garante simplicidade, segurança e evolução futura do sistema.
