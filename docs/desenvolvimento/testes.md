# Estratégia de Testes

Este documento define a **estratégia oficial de testes** do Sistema de Seleção de Monitoria.

O objetivo é garantir **qualidade, previsibilidade e segurança para refatorações**, sem gerar sobrecarga para um time majoritariamente iniciante.

> A base do projeto são **testes unitários**. Testes de integração são usados apenas quando agregam valor real.

---

## Princípios Gerais

- Testes existem para **proteger regras de negócio**, não para aumentar cobertura artificial
- Preferimos **poucos testes bons** a muitos testes frágeis
- Todo teste deve ser:

  - Legível
  - Rápido
  - Determinístico

---

## O Que Deve Ser Testado

### Services (Obrigatório)

Services concentram regras de negócio, portanto **devem sempre ser testados**.

Exemplos de regras a testar:

- Limites (ex: máximo de inscrições)
- Estados inválidos
- Fluxos felizes e fluxos de erro

---

### Controllers (Opcional)

Controllers **não contêm regra de negócio**.

Testes só são recomendados quando:

- Existe lógica de roteamento relevante
- Mapeamento de status HTTP é crítico

Na maioria dos casos, **podem ser ignorados**.

---

### Repositórios

- Não testar métodos padrão do Spring Data
- Testar apenas consultas customizadas relevantes

Preferir **testes de integração leves** quando necessário.

---

## O Que NÃO Deve Ser Testado

- DTOs
- Entidades JPA simples (getters/setters)
- Configurações
- Framework (Spring, Hibernate, Jackson)

---

## Tipos de Teste

### Teste Unitário

Características:

- Testa **uma classe por vez**
- Dependências são mockadas
- Não sobe contexto Spring

Ferramentas:

- JUnit 5
- Mockito

---

### Teste de Integração

Usar apenas quando:

- Validação depende do banco
- Consulta complexa
- Transações reais importam

Características:

- Usa `@SpringBootTest` ou `@DataJpaTest`
- Banco isolado (Testcontainers ou H2)

---

## Exemplo de Teste de Service

```java
@ExtendWith(MockitoExtension.class)
class EnrollmentServiceTest {

    @Mock
    private EnrollmentRepository repository;

    @Mock
    private MonitoringQueryService monitoringQueryService;

    @InjectMocks
    private EnrollmentService service;

    @Test
    void shouldNotAllowMoreThanThreeEnrollments() {
        when(repository.countByStudentId(1L)).thenReturn(3);

        assertThrows(DomainRuleException.class, () ->
            service.enroll(1L, 10L)
        );
    }
}
```

---

## Padrões de Escrita

- Nome do teste deve explicar o comportamento
- Um teste = um cenário
- Evitar múltiplos `asserts` desconexos

Exemplo:

```java
shouldNotAllowEnrollmentWhenLimitExceeded()
```

---

## Organização dos Testes

Estrutura espelhada da aplicação:

```
src/test/java
└── br.edu.ucsal
    └── enrollment
        └── service
            └── EnrollmentServiceTest.java
```

---

## Boas Práticas

- Teste deve falhar por **regra quebrada**, não por setup frágil
- Mock apenas o necessário
- Prefira dados simples e explícitos

---

## Anti-patterns em Testes

- Testar implementação ao invés de comportamento
- Testes que dependem de ordem
- Usar `@SpringBootTest` sem necessidade
- Criar testes apenas para aumentar cobertura

---

## Considerações Finais

Esta estratégia garante:

- Facilidade de escrita para iniciantes
- Proteção das regras de negócio
- Base sólida para evolução do sistema

Qualquer exceção a estas diretrizes deve ser justificada e documentada.
