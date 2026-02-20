# 🛠️ Diário de Desenvolvimento (DevLog)

Este documento registra a evolução técnica do **Legacy Nexus**, documentando as decisões de arquitetura e o roteiro de implementação do SaaS.


---

## 📅 (20/02/2026) - 🚀 Release: CRM Tático & Inteligência Webhook 

**Tipo:** `Nova Feature (Backend/Integração)` | **Status:** 🚧 `Em Desenvolvimento`

### 🎯 Objetivo da Sprint
Evoluir o sistema de um simples disparador de mensagens para uma **Secretária Eletrônica Universal em Tempo Real (Webhook)**. O foco desta atualização é a introdução da escuta ativa via Evolution API, permitindo o autoatendimento fora de expediente, interpretação inteligente de datas e a captura automática de novos leads diretamente para o banco de dados.

---

### ✨ O que há de novo?

#### 🤖 1. Modo Vigilante Universal (Messaging)
*Autoatendimento 24/7 integrado de forma ativa com o WhatsApp.*
- **Recepção de Desconhecidos:** Capacidade de responder qualquer pessoa que entrar em contato fora do horário comercial (19h às 09h), independentemente de estar ou não no banco de dados.
- **Trava de Memória (Anti-Spam):** Implementação da lista volátil `JA_RESPONDIDO_HOJE`, garantindo que o robô envie a mensagem de ausência apenas uma vez por cliente ao dia.
- **Detecção de Intervenção Humana:** Validação do parâmetro `fromMe` para assegurar que o robô silencie automaticamente caso a operação manual assuma a conversa.

#### 🔄 2. Auto-Capture via Webhook (Backend)
*Integração instantânea e captação orgânica de novos contatos.*
- **Servidor de Escuta (Flask):** Transição do método de *Polling* para *Webhooks* (`messages.upsert`), zerando o atraso na recepção de mensagens da Evolution API.
- **Injeção Automática de Leads:** Lógica de `INSERT OR IGNORE` que cadastra instantaneamente números desconhecidos no CRM como status `NOVO` e origem `WEBHOOK`.
- **Auditoria Contínua:** Sistema de rotação de logs (`historico_log.txt`) que arquiva eventos automaticamente ao atingir 5MB, preservando a saúde do disco no servidor.

#### 🧠 3. Inteligência de Agendamento (Lógica/NLP)
*Processamento flexível de texto para marcação de retornos.*
- **Identificação Flexível de Datas:** Algoritmo capaz de interpretar o texto do cliente (ex: *"amanhã"*, *"segunda"* ou *"20/02"*) e convertê-lo com precisão para o padrão SQL (`YYYY-MM-DD`).
- **Auto-Sync de Status:** Atualização autônoma do lead no banco de dados para `AGENDADO_AUTO` assim que a intenção de data é confirmada no chat.

#### 📊 4. Dashboard & Gestão Visual (Frontend/BI)
*Novas métricas analíticas e rastreabilidade no Streamlit.*
- **Destaque de Automação:** Identificação visual exclusiva (Cor Ciano e Ícone 🤖) nas tabelas e calendário para leads capturados e agendados pela inteligência do robô.
- **Filtro de Origem:** Separação analítica entre leads extraídos do site e cadastros orgânicos (Manuais/WhatsApp) na aba de gestão de funil.
- **Monitoramento Integrado:** Expansão da interface para visualização em tempo real dos logs do sistema (erros, conexões e mensagens) diretamente pela tela principal do BI.
---

## 📅 09/01/2026 - CRM Tático & Inteligência de Funil
**Tipo:** `Nova Feature (Backend/BI)` | **Status:**  ✅ Finalizado
### 🎯 O Objetivo
Integrar a camada de dados financeiros (extraídos pelo robô) com uma camada de gestão comercial, permitindo que o usuário gerencie o ciclo de vida do cliente sem sair da aplicação.

### 🛠️ Funcionalidades Entregues
1.  **Auto-Sync Database:**
    * Refatoração do `database.py` para injetar dados na tabela `clientes_crm` automaticamente sempre que um novo contrato é detectado pelo Robô.
2.  **Dashboard de Funil (UI):**
    * Implementação do gráfico `px.funnel` no Streamlit para visualização da conversão.
    * Criação do KPI de **Churn Rate** (Taxa de Desistência) em tempo real.
3.  **Monitor de Estagnação:** 
    * Integração com a **Evolution API** para envio automatizado de mensagens.
    * O sistema aplica tratamento de texto para evitar comportamento de bot, reduzindo riscos de bloqueio ou banimento do número do usuário.

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

## 🚀 Versão Atual: 2.2 (Desktop System)

### 📌 Situação do Projeto
O sistema evoluiu de um simples leitor de dados para um **Ecossistema de Gestão (CRM)**. A arquitetura desktop agora integra automação financeira com ferramentas de relacionamento e agendamento.

* **Interface:** Launcher Executável (`customtkinter`) com terminal embarcado.
* **Ingestão:** Robô Selenium (Headless) com logs visuais e *Auto-Sync*.
* **Gestão:** Calendário Interativo e Funil de Vendas (CRM Tático).
* **Persistência:** Banco de Dados Relacional (`SQLite`).
* **Visualização:** Dashboard Interativo (`Streamlit` + `SAC`).

### 📜 Histórico de Versões

#### v1.0 (MVP Legacy) - *Descontinuada*
* Baseada em Excel e execução via terminal de comando.
* **Limitação:** Conflitos de I/O e falta de interface amigável.

#### v2.0 (SQL Migration) - *Estável*
* Migração completa de Excel para SQL e Interface Gráfica Dedicada.
* **Ganho:** Performance, Segurança de dados e UX Profissional.

#### v2.2 (CRM Tático) – *Atual*

- **Criação do CRM**
  - Sistema para adicionar usuários com status e marcação de data para envio de mensagens.
  - Envio automático de mensagens e cobranças.
  - Integração com calendário visual, sincronização automática e inteligência de churn.
  - **Ganho:** Controle total do ciclo de vida do cliente (Lead ➝ Contrato).

- **Dashboard Atualizado**
  - Relatórios de status dos clientes, permitindo identificar em qual etapa ocorre a maior perda.
  - Tratamento e consolidação de relatórios mensais.

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
