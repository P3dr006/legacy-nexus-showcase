# 📱 Legacy Nexus - Módulo CRM & Inteligência Comercial

![Status](https://img.shields.io/badge/STATUS-OPERACIONAL-green?style=for-the-badge)
![BI](https://img.shields.io/badge/INTELIGÊNCIA-BUSINESS_INTELLIGENCE-blue?style=for-the-badge)

> **Nota:** Este documento detalha o subsistema de **Gestão de Carteira e Automação**. O código fonte utiliza algoritmos de sincronização automática entre o Robô extrator e a base de gestão.

## 🎯 O Objetivo do Módulo
Transformar o sistema de um simples "leitor de dados" para uma ferramenta ativa de gestão. O CRM centraliza o histórico, calcula automaticamente a "saúde" da carteira (Churn Rate) e organiza o fluxo de cadência de contatos.

---

## 🛠️ Funcionalidades Que Serão Entregues (Core CRM)

### 1. Auto-Sync (Sincronização Robô ↔ CRM)
O sistema possui uma trigger lógica no Backend (`database.py`).
* **Como funciona:** Assim que o robô identifica um novo contrato no portal da seguradora, ele cria automaticamente um "Lead" na tabela do CRM com status `NOVO`.
* **Benefício:** Elimina a digitação manual. O consultor apenas abre o painel e os clientes do dia já estão lá.

### 2. Análise de Funil de Vendas (Visualização Gráfica)
Implementação de gráficos de funil (`Plotly Funnel`) para identificar gargalos.
* **Mapeamento de Etapas:** O sistema plota visualmente a conversão: `NOVO` → `EM NEGOCIAÇÃO` → `FECHADO`.
* **Detecção de Gargalos:** Permite ver visualmente onde o volume de clientes está travando.

### 3. Indicadores de Risco (Churn & Stagnation)
Algoritmos de alerta para evitar perda de vendas.
* **Taxa de Desistência (Churn):** KPI automático que calcula a % de clientes marcados como `PERDIDO`.
* **Monitor de Estagnação:** Uma barra de progresso ("Dias Parado") alerta visualmente quando um lead não recebe interação há mais de 7 dias.

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