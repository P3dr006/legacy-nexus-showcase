# 🚀 Legacy Nexus - Ecossistema de Inteligência Comercial

![Status](https://img.shields.io/badge/VERSÃO-2.0-blueviolet?style=for-the-badge)
![Python](https://img.shields.io/badge/PYTHON-3.12-blue?style=for-the-badge&logo=python&logoColor=white)
![Database](https://img.shields.io/badge/SQLITE-EMBEDDED-003B57?style=for-the-badge&logo=sqlite&logoColor=white)

> **Nota:** Este é um repositório de demonstração (Showcase). O código-fonte original é mantido em repositório privado para proteção de regras de negócio.

## 📋 Sobre o Projeto
O **Legacy Nexus** evoluiu de uma automação simples para uma **Central de Operações (Command Center)** completa para gestão de consórcios.

O sistema elimina o trabalho manual de verificar portais de seguradoras, extraindo dados automaticamente e transformando-os em inteligência de negócios. A versão 2.0 introduz uma interface desktop (Launcher), persistência em banco de dados relacional e um Dashboard interativo com design de alta fidelidade.

## ⚙️ Arquitetura da Solução (V2.0 - Atual)

O sistema abandonou planilhas (`.xlsx`) em favor de uma arquitetura robusta baseada em Banco de Dados e Interface Gráfica Dedicada:

```mermaid
graph TD
    User((Usuário)) -->|Clica| A[🖥️ Launcher Desktop]
    
    subgraph "Legacy Nexus System"
    A -->|Dispara Thread| B[🤖 Nexus Bot RPA]
    A -->|Inicia Servidor| E[📊 Dashboard Web]
    
    B -->|Scraping Selenium| Internet((Portal Seguradora))
    B -->|Persistência SQL| C[(Legacy_Core.db)]
    
    C <-->|Leitura/Escrita| E
    end

    style C fill:#00ADB5,stroke:#333,stroke-width:2px,color:white
    style A fill:#222831,stroke:#00ADB5,stroke-width:2px,color:white
```

## 📜 Evolução da Arquitetura (Histórico)

O projeto sofreu uma refatoração completa para garantir escalabilidade e segurança de dados. Abaixo, o comparativo entre a abordagem inicial e a solução atual.

### ❌ Versão Legada (V1.0 - Baseada em Arquivo)
Na primeira versão, o sistema utilizava planilhas Excel como banco de dados.
* **Problema:** Conflitos de I/O (o robô não conseguia salvar se o Dashboard estivesse lendo o arquivo).
* **Interface:** Execução via linha de comando (terminal).

```mermaid
graph LR
  Portal[Portal Seguradora] -->|Selenium| Robo(Script Python)
  Robo -->|Escreve Linha a Linha| Excel{Relatório_Mestre.xlsx}
  Excel -->|Leitura Lenta| Dash[Dashboard V1]

  style Excel fill:#1D6F42,stroke:#333,stroke-width:2px,color:white
```
## 📸 Demonstração

Confira o **Legacy Nexus** em ação: o robô realizando a extração e o Dashboard atualizando em tempo real.


https://github.com/user-attachments/assets/b2042647-fa48-4663-93f3-3fd0cf15471c

[![DashBoard](./prints/dashboard.2.0.png)]

[![Analise de Dados](./prints/analise_comparativo_2.0.png)]


## 🛠️ Stack Tecnológica

    - Core: Python 3.12

    - Interface Desktop (GUI): CustomTkinter

    - Automação Web: Selenium WebDriver

    - Banco de Dados: SQLite3 (Migrado do Excel)

    - BI & Frontend: Streamlit + Streamlit Antd Components (SAC)

    - Visualização de Dados: Plotly Express

## 📅 Roadmap e Atualizações

Acompanhe a evolução técnica detalhada no [DEVLOG.md](./DEVLOG.md).

- [x] **Fase 1:** Extração de dados automatizada (RPA)

- [x] **Fase 2:** Migração de Excel para Banco de Dados SQL (Performance)

- [x] **Fase 3:** Refatoração de UI/UX (Design System Dark/Neon)

- [ ] **Fase 4:** Criação do Launcher Desktop (Central de Comando)

- [ ] **Futuro:** Criação de leads para whatsapp.

