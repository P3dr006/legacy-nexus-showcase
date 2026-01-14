# 📱 Legacy Nexus - Módulo CRM & Inteligência Comercial

![Status](https://img.shields.io/badge/STATUS-OPERACIONAL-green?style=for-the-badge)
![BI](https://img.shields.io/badge/INTELIGÊNCIA-BUSINESS_INTELLIGENCE-blue?style=for-the-badge)

> **Nota:** Este documento detalha o subsistema de **Gestão de Carteira e Automação**. O código fonte utiliza algoritmos de sincronização automática entre o Robô extrator e a base de gestão.

## 🎯 O Objetivo do Módulo
Transformar o sistema de um simples "leitor de dados" para uma ferramenta ativa de gestão. O CRM centraliza o histórico, calcula automaticamente a "saúde" da carteira (Churn Rate) e organiza o fluxo de cadência de contatos.

---

## 🛠️ Funcionalidades que Serão Entregues (Core CRM)

### 1. 🔄 Auto-Sync (Sincronização Robô ↔ CRM)
O sistema conta com uma *trigger lógica* no backend (`database.py`) responsável pela sincronização automática.
- **Funcionamento:** Assim que o robô identifica um novo contrato no portal da seguradora, um *Lead* é criado automaticamente na tabela do CRM com o status `NOVO`.
- **Benefício:** Elimina a necessidade de digitação manual. O consultor acessa o painel e os clientes do dia já estarão disponíveis.

### 2. 📊 Análise de Funil de Vendas (Visualização Gráfica)
Implementação de gráficos de funil utilizando **Plotly Funnel**, permitindo a identificação de gargalos no processo comercial.
- **Mapeamento de Etapas:** Visualização clara do fluxo de conversão:  
  `NOVO` → `EM NEGOCIAÇÃO` → `FECHADO`
- **Detecção de Gargalos:** Possibilita identificar visualmente em qual etapa ocorre a maior perda ou estagnação de clientes.

### 3. ⚠️ Indicadores de Risco (Churn & Estagnação)
Sistema de monitoramento inteligente para reduzir perdas de vendas.
- **Taxa de Desistência (Churn):** KPI automático responsável por calcular a porcentagem de clientes com status `PERDIDO`.
- **Monitor de Estagnação:** Integração com a **Evolution API** para envio automatizado de mensagens.  
  O sistema aplica tratamento de texto para evitar comportamento de bot, reduzindo riscos de bloqueio ou banimento do número do usuário.

---

## 🛠️ Funcionalidades Que Serão Entregues (Core CRM)

---

## 🔌 Arquitetura de Conectividade (Evolution API)

Para viabilizar o envio das mensagens (Cobrança/Lembretes), o sistema está preparado para conexão via API REST.

```mermaid
graph LR
    Launcher[💻 Launcher Desktop] -->|"1. Verifica Status/Gatilho"| DB[(Legacy_Core.db)]
    DB -->|"2. Retorna Lista de Envio"| Launcher
    Launcher -->|"3. Dispara Mensagem JSON"| API[📡 API WhatsApp]
    API -->|"4. Envia para Cliente"| Phone[📱 Cliente]
    
    style API fill:#25D366,stroke:#333,stroke-width:2px,color:white