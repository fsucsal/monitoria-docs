# Especificação de Requisitos — Sistema de Seleção de Monitoria

---

## Introdução

Este documento foi desenvolvido como parte dos trabalhos da **Fábrica de Software da UCSAL**, com o objetivo de especificar os requisitos e orientar o desenvolvimento de um **Sistema de Seleção de Monitoria Universitária**.

O projeto visa **otimizar o processo de inscrição, seleção e acompanhamento de monitores**, garantindo maior eficiência, organização e transparência na gestão acadêmica. Ao final do processo, o sistema deverá auxiliar a coordenação na **contabilização das horas e emissão automática de certificados de monitoria**.

---

### Propósito

Este documento descreve os **requisitos funcionais e não funcionais** do sistema de seleção de monitoria para a universidade.

O sistema permitirá:

- Inscrição de estudantes;
- Seleção e gerenciamento de monitores por disciplina;
- Controle de atividades ao longo do período;
- Emissão de certificados com carga horária cumprida.

---

### Escopo

O **Sistema de Seleção de Monitoria** deverá:

- Permitir a inscrição de estudantes interessados em monitoria;
- Viabilizar a seleção e designação de monitores por disciplina e curso;
- Controlar o registro de atividades dos monitores;
- Emitir certificados de monitoria ao final do período;
- Considerar diferentes turnos acadêmicos;
- Disponibilizar relatórios para acompanhamento gerencial.

---

### Definições, Acrônimos e Abreviações

| Termo           | Definição                                                                       |
| --------------- | ------------------------------------------------------------------------------- |
| **Monitoria**   | Atividade acadêmica na qual um estudante auxilia nas disciplinas                |
| **Monitor**     | Estudante selecionado para atuar na monitoria de uma disciplina                 |
| **Certificado** | Documento comprobatório da atividade de monitoria                               |
| **Turno**       | Período do dia no qual a disciplina é oferecida (matutino, vespertino, noturno) |

---

## Descrição Geral

### Perspectiva do Produto

O sistema será uma **aplicação web**, acessível por:

- Estudantes
- Professores
- Coordenadores

Permitirá a **gestão digital completa do processo de monitoria**, desde a inscrição até a emissão de certificados.

---

### Funções do Produto

- Cadastro de candidatos à monitoria;
- Seleção de monitores por disciplina e curso;
- Controle e registro das atividades dos monitores;
- Geração de certificados ao final do período de monitoria;
- Emissão de relatórios gerenciais.

---

### 2.3 Características dos Usuários

| Perfil            | Descrição                                               |
| ----------------- | ------------------------------------------------------- |
| **Estudantes**    | Realizam inscrição e acompanham o status da candidatura |
| **Professores**   | Avaliam e selecionam monitores                          |
| **Coordenadores** | Gerenciam o processo e emitem certificados              |

---

### Restrições

- O sistema deve seguir a política acadêmica da UCSAL;
- O acesso deve ser restrito e autenticado por perfil;
- Apenas usuários autorizados poderão realizar seleções e emitir certificados.

---

## Requisitos Específicos

---

### Requisitos Funcionais (RF)

| ID   | Descrição                                                                        |
| ---- | -------------------------------------------------------------------------------- |
| RF01 | O sistema deve permitir que estudantes realizem inscrições para a monitoria      |
| RF02 | O sistema deve permitir que professores avaliem e selecionem monitores           |
| RF03 | O sistema deve permitir a designação de múltiplos monitores para uma disciplina  |
| RF04 | O sistema deve considerar diferentes turnos ao associar monitores às disciplinas |
| RF05 | O sistema deve registrar as atividades dos monitores ao longo do período         |
| RF06 | O sistema deve emitir certificado com a carga horária ao final da monitoria      |
| RF07 | O sistema deve permitir a consulta e exportação de relatórios de monitoria       |

---

### Requisitos Não Funcionais (RNF)

| ID    | Descrição                                                       |
| ----- | --------------------------------------------------------------- |
| RNF01 | O sistema deve ser acessível via navegador web                  |
| RNF02 | O sistema deve garantir autenticação e controle de acesso       |
| RNF03 | O sistema deve armazenar dados em banco seguro e confiável      |
| RNF04 | O tempo de resposta para operações comuns deve ser ≤ 2 segundos |

---

## Critérios de Aceitação

- O sistema deve permitir a **inscrição de monitores de forma automatizada**;
- O sistema deve registrar e disponibilizar a **carga horária cumprida**;
- O **certificado gerado deve conter**:
  - Nome do monitor
  - Disciplina
  - Curso
  - Carga horária
  - Período de atuação

---

## Arquitetura Inicial do Sistema

### Frontend

Aplicação web desenvolvida em uma tecnologia moderna:

- React com Vite

---

### Backend (API)

API REST desenvolvida em:

- Java com Spring

Responsável por:

- Lógica de negócio
- Segurança
- Regras de autenticação e autorização
- Exposição de endpoints

---

### Banco de Dados

- **PostgreSQL**

---
