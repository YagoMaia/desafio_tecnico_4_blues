# Desafio Técnico Individual: Automação de Matrículas e Boas-vindas (4blue)

Este repositório contém a solução para o desafio técnico de automação de matrículas utilizando a plataforma n8n. A solução foca em processamento de dados via Webhook, sanitização de strings/datas, persistência em banco de dados relacional e comunicação transacional por e-mail.

## 📺 Demonstração em Vídeo

Confira a explicação técnica detalhada do fluxo e a resolução do "Desafio Oculto" na demonstração em vídeo disponível no repositório.

https://www.loom.com/share/5a0b13d0513b48e58ca4c5f47d980844

## 🛠️ Tecnologias Utilizadas

| Tecnologia                | Uso                               |
| ------------------------- | --------------------------------- |
| **n8n**                   | Orquestração do fluxo de trabalho |
| **JavaScript (Node.js)**  | Tratamento e sanitização de dados |
| **PostgreSQL (Supabase)** | Persistência de dados relacional  |
| **Gmail/SMTP**            | Disparo de e-mails transacionais  |
| **Postman**               | Testes de integração de API       |

## 🏗️ Arquitetura e Decisões Técnicas

O fluxo foi projetado com foco em **robustez**, **escalabilidade** e **boas práticas**. Abaixo estão as decisões técnicas principais:

### 1. Ingestão e Normalização de Dados

Identifiquei uma divergência entre a documentação textual (que solicitava chaves como `email_aluno`) e o exemplo prático (que utilizava `email`). Implementei uma lógica de normalização via JavaScript que torna o fluxo resiliente a ambas as entradas, garantindo que a automação não quebre caso o formulário de origem seja alterado.

### 2. Sanitização e Transformação

O nó de código (JavaScript) realiza operações rigorosas de limpeza nos dados recebidos:

- **Nomes**: Aplicado Title Case (primeira letra maiúscula) via Regex
- **Telefones**: Sanitização completa via Regex (`\D`), removendo caracteres não numéricos
- **Datas**: Conversão do formato brasileiro (DD/MM/YYYY) para ISO (YYYY-MM-DD), essencial para operações de banco de dados

### 3. Persistência de Dados (PostgreSQL)

Apesar do tutorial sugerir o uso de Data Tables internas, optei por utilizar uma instância externa de PostgreSQL (Supabase) para cumprir rigorosamente o requisito obrigatório de infraestrutura externa. Essa abordagem demonstra domínio sobre:

- Conexões de banco de dados relacional
- Tratamento de case-sensitivity em colunas
- Configuração de SSL para comunicação segura
- Otimização de queries SQL

## 🕵️ O "Desafio Oculto": Resolvendo a Falha de Idempotência

### Contexto do Problema

Em um cenário real, o disparo repetido de um Webhook (cliques duplos, reautenticação ou reenvios de requisição) geraria:

- ❌ Registros duplicados no banco de dados
- ❌ Inconsistência operacional (múltiplos cadastros para o mesmo aluno)
- ❌ Spam de e-mails de boas-vindas

### Solução Implementada

O fluxo implementa uma verificação de idempotência em 2 etapas:

1. **Consulta de Duplicidade**: Antes de qualquer inserção, realiza-se uma consulta SQL (`EXISTS`) no banco de dados para verificar se a matrícula já existe

2. **Fluxos Condicionais**:
   - **Se existir**: O fluxo desvia para uma comunicação específica informando que o cadastro já está ativo
   - **Se for novo**: O fluxo segue para a inserção no banco e envio do e-mail de boas-vindas padrão

Essa abordagem garante a integridade dos dados e uma excelente experiência do usuário.
