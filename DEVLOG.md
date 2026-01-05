# 🛠️ Diário de Desenvolvimento (DevLog)

Este documento registra a evolução técnica do **Legacy Nexus**, documentando as decisões de arquitetura e o roteiro de implementação do SaaS.

## 📅 05/01/2026 - Central de Comando (Launcher)
**Tipo:** `Nova Feature (UX)` | **Status:** 🚧 Em Desenvolvimento

### 🎯 O Objetivo
Para eliminar a necessidade de abrir terminais e digitar códigos manualmente, estou desenvolvendo uma Interface Gráfica (GUI) que servirá como o "Controle Remoto" de todo o ecossistema Legacy Nexus.

### 🛠️ Funcionalidades do Launcher
1.  **Terminal Embarcado:** Uma tela preta dentro da janela que mostra os logs do Robô em tempo real (prints do Python), dando feedback visual do progresso da extração.
2.  **Botão "Executar Extração":** Dispara o `robo_main.py` em uma thread separada para não travar a interface.
3.  **Botão "Abrir BI":** Inicia o servidor do Streamlit e abre o Dashboard no navegador automaticamente.

### 🎨 Stack Visual
Utilizando `customtkinter` para manter a identidade visual **Dark/Neon** do projeto, fugindo do visual padrão cinza do Windows.

---

## 📅 04/01/2026 - Dashboard 3.0 & UX Avançada
**Tipo:** `Refatoração de Interface (UI/UX)` | **Status:** ✅ Finalizado

### 📌 Contexto
Com a migração para SQLite concluída e estável (v2.0), o foco agora é transformar os dados brutos em inteligência visual. O antigo painel era estático; o novo precisa ser uma ferramenta de decisão (BI).

### 🛠️ O Que Estou Fazendo Agora
Estou reescrevendo o `dashboard.py` focando em interatividade e estética "Cyberpunk/Clean".

1.  **Filtros em Camadas:** Implementação de lógica de filtragem em 3 etapas (Porcentagem da Cota -> Faixa de Valor -> Tipo de Contrato).
2.  **Visualização Rica:** Substituição de números crus por **Barras de Progresso** dentro da tabela de listagem.
3.  **Engine de Comparação:** Criação de algoritmo para comparar performance temporal (Mês Atual vs. Mês Anterior ou Ano x Ano) com cálculo automático de Delta (%).
4.  **Estética:** Aplicação de CSS personalizado para remover o visual padrão do Streamlit e aplicar identidade visual Neon/Dark.

### ⏭️ Próximos Passos
Integrar lógica de CRM (n8n + Evolution API) para agir sobre esses leads.

---


## 📅 29/12/2025 - Início da Refatoração: SQLite (Adeus Excel)
**Tipo:** `Refatoração de Core` | **Status:** ✅ Finalizado

### 🔄 Mudança de Rota
A arquitetura baseada em `.xlsx` foi descontinuada para evitar travamentos de leitura/escrita simultânea.
Migrei 100% da camada de persistência para **SQLite3**.

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
