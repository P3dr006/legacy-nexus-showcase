# 🛠️ Diário de Desenvolvimento (DevLog)

Este documento registra a evolução técnica do **Legacy Nexus**, documentando as decisões de arquitetura e o roteiro de implementação do SaaS.

## 📅 09/01/2026 - CRM Tático & Inteligência de Funil
**Tipo:** `Nova Feature (Backend/BI)` | **Status:**  🚧 Em Desenvolviment
### 🎯 O Objetivo
Integrar a camada de dados financeiros (extraídos pelo robô) com uma camada de gestão comercial, permitindo que o usuário gerencie o ciclo de vida do cliente sem sair da aplicação.

### 🛠️ Funcionalidades Entregues
1.  **Auto-Sync Database:**
    * Refatoração do `database.py` para injetar dados na tabela `clientes_crm` automaticamente sempre que um novo contrato é detectado pelo Robô.
2.  **Dashboard de Funil (UI):**
    * Implementação do gráfico `px.funnel` no Streamlit para visualização da conversão.
    * Criação do KPI de **Churn Rate** (Taxa de Desistência) em tempo real.
3.  **UX de Cadência:**
    * Adição da barra de progresso "Dias Parado" na tabela de leads, calculada pela diferença entre `datetime.now()` e a `data_ultima_interacao`.

---

## 📅 05/01/2026 - Central de Comando (Launcher)
**Tipo:** `Nova Feature (UX)` | **Status:** ✅ Finalizado

### 🎯 O Objetivo
Centralizar a operação do ecossistema Legacy Nexus, eliminando a necessidade de terminais e comandos manuais. O Launcher atua como um "wrapper" (envelope) que gerencia os processos do Robô e do Dashboard.

### 🛠️ Funcionalidades Entregues
1.  **Terminal Embarcado:** Redirecionamento dos logs (`stdout`) do Python para uma caixa de texto na interface, com suporte a **UTF-8** (emojis e acentos) e auto-scroll.
2.  **Execução Assíncrona:** O botão "Iniciar" dispara o robô em uma **Thread** separada usando `subprocess`, garantindo que a interface não trave durante a extração.
3.  **Fluxo de Decisão (UX):**
    * Ao finalizar, o sistema exibe um Pop-up: *"Deseja abrir o Dashboard?"*.
    * Evita a abertura forçada de janelas e dá controle ao usuário.
4.  **Feedback Visual:** Botões mudam de cor para indicar status (🟨 Executando / 🟩 Concluído).

### 🎨 Stack Visual
Desenvolvido em `customtkinter` com paleta **Dark/Matte** (Fosco) para reduzir o ofuscamento visual e manter a identidade Cyberpunk do projeto.

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

## 🚀 Versão Atual: 2.0 (Desktop System)

### 📌 Situação do Projeto
O sistema atingiu a maturidade de arquitetura desktop. Não é mais dependente de arquivos Excel soltos.

* **Interface:** Launcher Executável (`customtkinter`)
* **Ingestão:** Robô Selenium (Headless) com logs no terminal visual.
* **Persistência:** Banco de Dados Relacional (`SQLite`).
* **Visualização:** Dashboard Interativo (`Streamlit` + `SAC`).

### 📜 Histórico de Versões

#### v1.0 (MVP Legacy) - *Descontinuada*
* Baseada em Excel e execução via terminal de comando.
* **Problema:** Conflitos de I/O e falta de interface amigável.

#### v2.0 (Atual)
* Baseada em SQL e Interface Gráfica Dedicada.
* **Ganho:** Performance, Segurança de dados e UX Profissional.

---

<details>
<summary>📂 <strong>Clique para ver o Histórico de 2025 (Versões Antigas)</strong></summary>
<br>
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


## 🚧 Versão Atual: 1.0 (MVP)(finalizada)

### 📌 Situação 
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
</details>
