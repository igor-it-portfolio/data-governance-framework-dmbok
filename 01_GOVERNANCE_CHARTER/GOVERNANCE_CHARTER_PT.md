# 📜 Carta de Operacionalização da Governança de Dados (Data Governance Charter)

## 1. Visão Geral e Objetivos
Esta Carta de Governança estabelece as diretrizes, responsabilidades e o modelo operacional para a gestão de ativos de dados na organização, fundamentado nas melhores práticas do **DAMA-DMBOK**.

O objetivo principal é garantir que os dados corporativos sejam **confiáveis, padronizados, seguros e alinhados às exigências regulatórias (LGPD/GDPR)**, maximizando o seu valor estratégico para decisões de negócio.

---

## 2. Estrutura de Domínios de Dados
Para fins de governança e atribuição de responsabilidades, os ativos de dados da empresa são divididos nos seguintes **Domínios Corporativos**:

| Domínio de Dados | Descrição do Escopo | Exemplos de Entidades Principais |
| :--- | :--- | :--- |
| **Clientes (Customer)** | Dados cadastrais, identificação e contato de clientes | `tb_clientes`, `tb_enderecos`, `tb_contatos` |
| **Financeiro (Finance)** | Transações, faturamento, meios de pagamento e contabilidade | `tb_vendas`, `tb_pagamentos`, `tb_faturamento` |
| **Produtos (Product)** | Catálogo de produtos, serviços e precificação | `tb_produtos`, `tb_categorias`, `tb_precos` |

---

## 3. Matriz de Responsabilidades (Matriz RACI)

A matriz abaixo define o papel de cada agente na gestão de dados:
* **R (Responsible / Responsável):** Quem executa a tarefa.
* **A (Accountable / Aprovador):** Quem responde pela entrega e aprova o resultado (apenas 1 por tarefa).
* **C (Consulted / Consultado):** Quem deve ser consultado antes/durante a execução.
* **I (Informed / Informado):** Quem deve ser notificado sobre o progresso ou resultado.

### 👥 Papéis Mapeados:
1. **Data Owner (Dono do Dado):** Executivo/Gerente da área de negócio responsável pelo ativo.
2. **Data Steward (Guardião do Dado):** Especialista da área de negócio focado na qualidade e regras de negócio.
3. **Data Custodian (TI / Engenharia):** Time técnico responsável pela infraestrutura e segurança do banco.
4. **Governante de Dados (Nossa Atuação):** Responsável por facilitar, auditar e garantir os processos de governança.

---

### 📊 Tabela RACI de Governança de Dados

| Atividade / Processo de Governança | Data Owner | Data Steward | Data Custodian (TI) | Governante de Dados |
| :--- | :---: | :---: | :---: | :---: |
| Definição do significado de termos de negócio | **A** | **R** | I | **C** |
| Cadastro e atualização do Dicionário de Dados | C | **R** | I | **A / R** |
| Aprovação de regras de acesso e permissões | **A** | C | **R** | C |
| Identificação e classificação de PII (LGPD) | C | **R** | C | **A** |
| Correção de falhas de pipeline de dados (ETL) | I | I | **A / R** | C |
| Homologação de qualidade das bases de dados | C | **R** | C | **A** |
| Execução de auditorias periódicas em SQL | I | C | I | **A / R** |

---

## 4. Ritos e Comunicação
* **Comitê de Governança (Mensal):** Reunião entre Governante de Dados, DPO e Data Owners para revisão de KPIs de qualidade e novos projetos.
* **Reunião Operacional de Stewardship (Quinzenal):** Alinhamento entre Governante de Dados e Data Stewards para revisão e atualização do catálogo.
