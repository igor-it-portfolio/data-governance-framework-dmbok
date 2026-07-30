# 🏛️ Enterprise Data Governance Framework (DAMA-DMBOK)

![Data Governance](https://img.shields.io/badge/Data_Governance-DAMA--DMBOK2-blue?style=for-the-badge)
![Compliance](https://img.shields.io/badge/Compliance-GDPR%20%7C%20LGPD-green?style=for-the-badge)
![Security](https://img.shields.io/badge/Security-RBAC%20%26%20PII%20Masking-red?style=for-the-badge)
![Quality](https://img.shields.io/badge/Data_Quality-SQL%20Scorecard-orange?style=for-the-badge)

## 📌 Visão Geral do Projeto

Este repositório contém um **Framework de Governança e Gestão de Dados Enterprise**, desenvolvido com base nas melhores práticas do **DAMA-DMBOK2 (Data Management Body of Knowledge)** e adaptado para conformidade com regulamentações internacionais de privacidade (**GDPR, CCPA e LGPD**).

O objetivo deste projeto é estruturar a governança de um ecossistema analítico (*Data Lake / Data Warehouse*), cobrindo desde a fundação organizacional e papéis de liderança até a implementação técnica de qualidade, linhagem, controle de acesso e ciclo de vida dos dados.

---

## 🗺️ Arquitetura do Framework & Estrutura de Módulos

O repositório está organizado em módulos que espelham as áreas de conhecimento do DAMA-DMBOK:

- **01_GOVERNANCE_CHARTER_RACI/** - Governança & Organização
  - `GOVERNANCE_CHARTER_PT.md` & `GOVERNANCE_CHARTER_ES.md` (Carta da Governança de Dados)
  - `RACI_MATRIX_PT.md` & `RACI_MATRIX_ES.md` (Matriz RACI - Owners, Stewards, Custodians)
- **02_METADATA_DATA_DICTIONARY/** - Arquitetura, Metadados & Linhagem
  - `METADATA_CATALOG_PT.md` & `METADATA_CATALOG_ES.md` (Glossário, Dicionário & Linhagem)
- **03_DATA_QUALITY_AUDIT/** - Gestão da Qualidade de Dados (DQ)
  - `DATA_QUALITY_POLICY_PT.md` & `DATA_QUALITY_POLICY_ES.md` (Política & 5 Dimensões de DQ em SQL)
- **04_DATA_SECURITY_PRIVACY/** - Segurança, Privacidade & Controle de Acesso
  - `SECURITY_PRIVACY_PT.md` & `SECURITY_PRIVACY_ES.md` (RBAC, Classificação PII & Visões Mascaradas)
- **05_DATA_LIFECYCLE_RETENTION/** - Ciclo de Vida, Retenção & Descarte
  - `LIFECYCLE_RETENTION_PT.md` & `LIFECYCLE_RETENTION_ES.md` (Matriz de Temporalidade, Tiering & Purge)

---

## 🛠️ Resumo das Implementações Técnicas

### 1. Governança e Papéis (DAMA Chapter: Data Governance)
* Definição do **Comitê de Governança de Dados**.
* Atribuição clara de responsabilidades via **Matriz RACI** entre *Data Owners* (Negócio), *Data Stewards* (Guardiões), *Data Custodians* (Engenharia) e *DPO*.

### 2. Gestão de Metadados e Linhagem (DAMA Chapter: Metadata Management)
* **Glossário de Negócio:** Padronização de termos e métricas da empresa.
* **Dicionário de Dados:** Mapeamento técnico de colunas, tipos, chaves e restrições.
* **Linhagem de Dados (*Data Lineage*):** Rastreabilidade de ponta a ponta desde a ingestão (*Bronze*) até o consumo em BI (*Gold*).

### 3. Data Quality Scorecard via SQL (DAMA Chapter: Data Quality)
* Automação de auditoria cobrindo as 5 dimensões oficiais: **Completude, Unicidade, Validade, Integridade e Atualidade**.
* Geração de indicadores de saúde da base em percentual (`DQ Scorecard`).

### 4. Segurança, RBAC & Proteção PII (DAMA Chapter: Data Security)
* Matriz de permissão por papel (**RBAC**) aplicando o *Princípio do Menor Privilégio*.
* Proteção de **PII** (*Personally Identifiable Information*) via **Data Masking** e **SHA-256 Hashing** em SQL para conformidade com **GDPR / LGPD**.

### 5. Gestão do Ciclo de Vida & Temporalidade (DAMA Chapter: Data Lifecycle)
* Matriz de Retenção Legal baseada em regulamentos fiscais, civis e regulatórios.
* Automação de descarte de logs (*Hard Delete*) e anonimização de clientes inativos (*Right to be Forgotten / GDPR Art. 17*) via SQL.

---

## 🌐 Suporte Multilíngue (Multilingual Support)
Todos os artefatos de documentação e scripts foram implementados em **Português (PT)** e **Espanhol (ES)**, visando aplicação em ambientes corporativos internacionais e equipes globais.

---
*Framework desenvolvido como portfólio prático de Governança, Arquitetura e Engenharia de Dados.*
