# 🛠️ Diário de Desenvolvimento (DevLog)

Este documento registra a evolução técnica do **Legacy Nexus**, documentando as decisões de arquitetura e o roteiro de implementação do SaaS.

## 📅 29/12/2025 - Início da Refatoração: SQLite (Adeus Excel)
**Tipo:** `Refatoração de Core` | **Status:** 🚧 Em Andamento

### 🔄 Mudança de Rota
A partir de hoje, a arquitetura baseada em `.xlsx` foi **descontinuada**.
Decidi reescrever a camada de dados para suportar o futuro SaaS e acesso via Web.

### 🛠️ O Que Estou Fazendo Agora
Estou substituindo toda a lógica de manipulação de planilhas (`openpyxl`/`pandas`) por comandos SQL diretos.

1.  **Novo Banco:** Criando o arquivo `legacy_core.db` (SQLite).
2.  **Nova Tabela:** Estruturando a tabela `vendas_contratos` com colunas tipadas (TEXT, REAL, INTEGER).
3.  **Objetivo:** Garantir que o robô consiga inserir dados sem travar a leitura do Dashboard.

---


## 🚧 Versão Atual: 1.0 (MVP)

### 📌 Situação Atual
O sistema está estável e operacional.
- **Ingestão:** O robô (Selenium) extrai dados do portal da seguradora com sucesso.
- **Persistência:** Os dados são tratados via Pandas e salvos em uma planilha estruturada (`.xlsx`) local.
- **Visualização:** O Dashboard lê esta planilha para gerar os indicadores.

### 🔎 Análise de Arquitetura (Por que mudar?)
Embora o uso de Excel funcione perfeitamente para uso local, identifiquei que ele será um gargalo para os próximos passos do roadmap, especificamente a **Integração Web**.

Como o projeto prevê um **Portal do Cliente** (onde o usuário fará login no site novo) e um **Módulo de WhatsApp**, manter os dados em planilha torna inviável:

1.  **Segurança:** Planilhas não oferecem controle de acesso granular para múltiplos usuários.
2.  **Conectividade:** O futuro site precisará consultar dados via API/Backend, e consultar um Excel é ineficiente comparado a uma query SQL.

### 🚀 Decisão Estratégica (Roadmap v2.0)
Para suportar o módulo de **Login de Clientes** e o **CRM de WhatsApp**, foi decidido a mudança da camada de dados.

- **De:** Armazenamento em Arquivo (`.xlsx`).
- **Para:** Banco de Dados Relacional (`SQLite` inicialmente, preparado para `PostgreSQL`).

*Status: Proximo passo a migração do código*