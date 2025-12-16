# Infraestrutura e Deploy

Este documento descreve a **visão de infraestrutura** do Sistema de Seleção de Monitoria — UCSal.

O foco é apresentar **como o sistema é executado, implantado, configurado e observado**, sem entrar em detalhes de implementação de código.

> Padrões de logging, tratamento de erros e instrumentação no código estão documentados no **Guia de Padrões de Desenvolvimento Backend**.

---

## 1. Visão Geral da Infraestrutura

O sistema é uma aplicação web **backend stateless**, projetada para execução em ambientes conteinerizados.

Princípios adotados:

- Reprodutibilidade entre ambientes
- Simplicidade operacional
- Baixo custo inicial
- Preparação para evolução futura

---

## 2. Ambiente de Desenvolvimento

- Execução local via **Docker Compose**
- Banco de dados PostgreSQL em container
- Configuração por variáveis de ambiente

Objetivos:

- Setup rápido
- Ambiente próximo ao produtivo
- Facilidade para novos membros

---

## 3. Containerização

### 3.1 Docker

- Backend empacotado como imagem Docker
- Imagem única por versão
- Build automatizado em pipeline CI

Boas práticas:

- Imagens pequenas
- Uso de multi-stage build
- Sem segredos hardcoded

---

### 3.2 Orquestração

- Inicialmente: Docker Compose
- Evolução futura: Kubernetes

A arquitetura não depende de recursos específicos do orquestrador.

---

## 4. Configuração e Segredos

### 4.1 Configuração

- Variáveis de ambiente (`ENV VARS`)
- Perfis Spring (`dev`, `prod`)

Exemplos de configurações:

- URL do banco de dados
- Chave JWT
- Tempo de expiração de tokens

---

### 4.2 Segredos

- Nunca versionados no repositório
- Em produção: serviço de secret management (a definir)

---

## 5. Banco de Dados

### 5.1 PostgreSQL

- Banco relacional principal
- Integridade referencial
- Migrações versionadas

### 5.2 Migrações

- Executadas automaticamente no deploy
- Versionadas junto ao código

---

## 6. CI/CD

### 6.1 Pipeline

Pipeline padrão:

1. Build
2. Testes unitários
3. Build da imagem Docker
4. Publicação da imagem

---

### 6.2 Deploy

- Deploy manual ou automatizado
- Rollback via versão da imagem

---

## 7. Observabilidade

### 7.1 Logs

- Logs estruturados (JSON)
- Um logger padrão por classe
- Foco em eventos relevantes e erros

> Detalhes de formato e uso estão no Guia de Padrões Backend.

---

### 7.2 Métricas

- Métricas de aplicação e infraestrutura
- Preparado para Prometheus

Exemplos:

- Tempo de resposta
- Taxa de erro
- Uso de memória

---

### 7.3 Tracing

- Preparado para OpenTelemetry
- Foco em operações críticas

---

## 8. Segurança de Infraestrutura

- HTTPS obrigatório
- Configurações sensíveis fora do código
- Princípio do menor privilégio

---

## 9. Performance e Escalabilidade

### 9.1 Escalabilidade

- Backend stateless
- Escala horizontal

### 9.2 Cache

- Uso opcional de Redis
- Cache para leituras frequentes

---

## 10. Backup e Disaster Recovery

### 10.1 Backups

- Backups periódicos do banco de dados
- Retenção configurável

### 10.2 Recuperação

- Procedimento de restore documentado
- Testes periódicos de recuperação

---

## 11. Evolução da Infraestrutura

Possíveis evoluções:

- Kubernetes
- Mensageria dedicada
- Serviços gerenciados
