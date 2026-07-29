# 📖 Catálogo de Metadados, Glossário e Linhagem de Dados

## 1. Glossário de Negócio (Business Glossary)
O Glossário de Negócio padroniza os conceitos corporativos do Domínio de Clientes e Vendas para garantir uma visão única e sem ambiguidade entre as áreas de negócio e TI.

| Termo de Negócio | Definição de Negócio | Regra de Negócio | Data Steward Responsável |
| :--- | :--- | :--- | :--- |
| **Cliente Ativo** | Cliente que realizou ao menos 1 compra aprovada nos últimos 90 dias. | Exclui compras canceladas ou com *chargeback*. | Steward de Vendas |
| **Receita Líquida** | Valor total das vendas faturadas deduzido de impostos e devoluções. | `Receita Bruta - (Impostos + Devoluções)` | Steward Financeiro |
| **Status da Conta** | Situação cadastral do cliente no ecossistema da empresa. | Valores aceitos: `ATIVO`, `INATIVO`, `BLOQUEADO`. | Steward de Atendimento |

---

## 2. Dicionário de Dados Técnico (Technical Data Dictionary)
Mapeamento da estrutura física das tabelas no ambiente de banco de dados / Data Lake (`db_analytics_prod`).

### 📑 Tabela: `tb_clientes` (Domínio: Clientes)
* **Descrição:** Armazena os dados cadastrais e de identificação de clientes da plataforma.
* **Frequência de Atualização:** Diária (Batch às 02:00 AM).

| Nome da Coluna | Tipo de Dado | Permite Nulo? | Chave (PK/FK) | Classificação de Privacidade (LGPD) | Descrição do Campo |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id_cliente` | `STRING` | Não | **PK** | Pública | ID único e imutável do cliente (UUID). |
| `nm_cliente` | `STRING` | Não | - | **Dado Pessoal (PII)** | Nome completo do cliente cadastrado. |
| `num_cpf` | `STRING` | Não | - | **Dado Pessoal Sensível (PII/Mascara)** | Número do CPF (deve ser armazenado mascarado). |
| `ds_email` | `STRING` | Sim | - | **Dado Pessoal (PII)** | E-mail de contato principal. |
| `st_cliente` | `STRING` | Não | - | Interna / Operacional | Status do cliente (`ATIVO`, `INATIVO`, `BLOQUEADO`). |
| `dt_cadastro` | `TIMESTAMP` | Não | - | Interna | Data e hora em que o cadastro foi efetuado. |

---

## 3. Linhagem de Dados (Data Lineage)
Representação do fluxo de integração e transformação do dado desde a sua origem até o consumo final.

### 🔄 Fluxo: Origem ➔ Processamento ➔ Consumo (Dashboard de Vendas)

* **Etapa 1 (Sistema Origem):** App / PostgreSQL (`tb_raw_customers`) — Captura do dado bruto.
* **Etapa 2 (Data Lake / Ingestão Bronze):** Google BigQuery / AWS (`db_raw.tb_clients_daily`) — Armazenamento do histórico.
* **Etapa 3 (Camada Analítica Gold):** Databricks / SQL (`db_prod.tb_clientes`) — Aplicação de regras de qualidade, remoção de duplicados e mascaramento de CPF (PII).
* **Etapa 4 (Consumo Final):** Power BI (`Dashboard de Churn e Performance`) — Leitura para executivos e diretores.
