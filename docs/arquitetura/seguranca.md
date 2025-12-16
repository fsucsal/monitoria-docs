# Segurança

Este documento descreve as **diretrizes de segurança** adotadas no Sistema de Seleção de Monitoria, considerando o contexto acadêmico, mas com **qualidade de produção**.

O objetivo é garantir **autenticação, autorização e proteção básica da API**, sem introduzir complexidade excessiva para um time majoritariamente júnior.

---

## 1. Princípios de Segurança

As decisões de segurança do sistema seguem os princípios abaixo:

- Segurança **por padrão** (secure by default)
- Simplicidade na implementação
- Clareza para quem está aprendendo
- Padronização e previsibilidade

> Segurança não deve ser opcional nem espalhada pelo código.

---

## 2. Modelo de Autenticação

### 2.1 Estratégia

O sistema utiliza **JWT (JSON Web Token)** para autenticação.

Fluxo resumido:

1. Usuário realiza login
2. API valida credenciais
3. API emite um JWT assinado
4. Cliente envia o token no header `Authorization`
5. API valida o token a cada requisição

```http
Authorization: Bearer <token>
```

---

### 2.2 Conteúdo do Token (Claims)

O JWT deve conter apenas informações essenciais:

- `sub`: ID do usuário
- `role`: papel do usuário (ALUNO, PROFESSOR, ADMIN)
- `iat` / `exp`: datas de emissão e expiração

> Nunca incluir dados sensíveis (senha, CPF, e-mail).

---

## 3. Autorização

### 3.1 Papéis (Roles)

Papéis básicos do sistema:

- **ALUNO** – pode se inscrever em monitorias
- **PROFESSOR** – avalia inscrições
- **ADMIN** – acesso administrativo

---

### 3.2 Controle de Acesso

A autorização deve ser aplicada em **nível de controller** usando anotações do Spring Security.

Exemplo:

```java
@PreAuthorize("hasRole('ALUNO')")
@PostMapping("/enroll")
public ResponseEntity<Void> enroll(@RequestBody EnrollmentRequest request) {
    enrollmentService.enroll(request);
    return ResponseEntity.ok().build();
}
```

> Não realizar controle de acesso dentro do service.

---

## 4. Configuração do Spring Security

### 4.1 Filtro de Autenticação JWT

- Implementar um filtro que:

  - Extraia o token do header
  - Valide assinatura e expiração
  - Popule o `SecurityContext`

O filtro deve ser:

- Centralizado
- Simples
- Sem lógica de negócio

---

### 4.2 Cadeia de Segurança

Diretrizes:

- Endpoints públicos explicitamente definidos (`/auth/**`)
- Demais endpoints exigem autenticação
- CSRF desabilitado (API stateless)

---

## 5. Tratamento de Erros de Segurança

### 5.1 Erros Comuns

| Situação       | HTTP |
| -------------- | ---- |
| Token ausente  | 401  |
| Token inválido | 401  |
| Token expirado | 401  |
| Acesso negado  | 403  |

Esses erros são tratados pelo próprio Spring Security.

---

## 6. Boas Práticas

- Sempre validar expiração do token
- Tokens com tempo de vida curto
- Usar HTTPS (mesmo em ambiente acadêmico)
- Logs nunca devem conter tokens

---

## 7. O que Evitar

- Autorização manual via `if` no código
- Lógica de segurança em services
- Reutilizar JWT de outros projetos sem entendimento
- Criar múltiplos filtros sem necessidade

---

## 8. Evolução Futura

Este modelo permite evolução para:

- Refresh tokens
- MFA
- Integração com Identity Provider

Sem impacto estrutural no sistema atual.

---

## 9. Considerações Finais

A segurança do sistema foi projetada para ser:

- Fácil de entender
- Difícil de usar errado
- Suficiente para o contexto do projeto

Qualquer alteração significativa deve ser registrada via ADR.
