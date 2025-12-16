# Comunicação entre Módulos

Este documento define **como os módulos do sistema podem se comunicar**, garantindo **baixo acoplamento**, **clareza de responsabilidades** e **evolução segura** do Sistema de Seleção de Monitoria — UCSal.

A arquitetura adotada é **Monólito Modular**, portanto **separação lógica é obrigatória**, mesmo rodando no mesmo deploy.

---

## Princípios Fundamentais

A comunicação entre módulos deve seguir os seguintes princípios:

- Cada módulo é **dono das suas regras de negócio**
- Módulos não conhecem detalhes internos uns dos outros
- Dependências são **explícitas e unidirecionais**
- A comunicação ocorre via **contratos bem definidos**

---

## O Que é Permitido

Um módulo pode:

- Consumir **interfaces públicas** de outro módulo
- Consumir **DTOs de leitura** expostos por outro módulo
- Solicitar informações **read-only** de outro módulo

---

## O Que é Proibido

Um módulo **nunca pode**:

- Acessar diretamente o **repositório** de outro módulo
- Manipular **entidades JPA** de outro módulo
- Compartilhar regras de negócio via `shared`
- Criar dependências circulares

---

## Regra do Dono da Regra

> A regra de negócio deve sempre residir no **módulo dono da ação**.

Exemplo:

> "Aluno só pode se inscrever em até 3 monitorias"

- A ação é **inscrição** → módulo `enrollment`
- Logo, a regra vive em `enrollment`
- Outros módulos apenas **fornecem dados**

---

## Comunicação via Interfaces

### Interface Pública

Quando um módulo precisa de dados de outro:

- O módulo dono do dado expõe uma **interface pública**
- A interface deve ser **simples e estável**

Exemplo:

```java
public interface MonitoringReadService {
    long countActiveMonitoringsByStudent(Long studentId);
}
```

---

### Implementação Interna

A implementação da interface pertence ao módulo dono:

```java
@Service
class MonitoringReadServiceImpl implements MonitoringReadService {

    private final MonitoringRepository repository;

    public long countActiveMonitoringsByStudent(Long studentId) {
        return repository.countActiveByStudent(studentId);
    }
}
```

---

### Consumo pelo Módulo Cliente

```java
@Service
public class EnrollmentService {

    private final MonitoringReadService monitoringReadService;

    public EnrollmentService(MonitoringReadService monitoringReadService) {
        this.monitoringReadService = monitoringReadService;
    }
}
```

---

## Interfaces Read-only

Interfaces expostas entre módulos devem:

- Expor apenas **operações de leitura**
- Não permitir alterações de estado

Motivo:

- Evitar vazamento de regras
- Facilitar futura extração para microsserviços

---

## DTOs na Comunicação entre Módulos

Quando necessário:

- Usar **DTOs específicos de leitura**
- Nunca reutilizar DTOs de API

```java
public record StudentMonitoringSummary(Long studentId, long total) {}
```

---

## Quando Criar uma Interface

Crie uma interface quando:

- Um módulo precisa de dados de outro
- A dependência deve ser controlada
- A operação é read-only

> Interfaces são o **contrato oficial entre módulos**.

---

## Testes e Comunicação entre Módulos

Nos testes de service:

- Interfaces externas devem ser **mockadas**
- Nunca usar repositórios de outro módulo

---

## Anti-patterns Comuns

- Service acessando repository externo
- Entidade de um módulo sendo usada em outro
- Lógica de negócio dividida entre módulos
- `shared` virando "terra de ninguém"

---

## Resumo

- Comunicação ocorre por interfaces
- Regras vivem no módulo dono
- Dependências são explícitas
- Leitura é permitida, escrita não

Este padrão mantém o sistema organizado, previsível e evolutivo.
