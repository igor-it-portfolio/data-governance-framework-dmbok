# ⏳ Gestão do Ciclo de Vida do Dado, Políticas de Retenção e Descarte Seguro

## 1. Fases do Ciclo de Vida do Dado (Data Lifecycle)
Alinhado ao padrão **DAMA-DMBOK**, esta política define o fluxo de maturidade e armazenamento da informação desde a sua criação até o expurgo final.

| Fase do Ciclo | Descrição Operacional | Camada de Armazenamento | Política de Custo / Performance |
| :--- | :--- | :--- | :--- |
| **1. Criação / Ingestão** | Coleta de dados brutos de sistemas OLTP, APIs e parceiros. | Camada Bronze (Raw) | Alta Performance (Hot Storage) |
| **2. Processamento & Uso** | Limpeza, transformação, regras de negócio e consumo em BI. | Camadas Silver & Gold | Alta Performance (Hot Storage) |
| **3. Arquivamento (Warm/Cold)** | Dados com baixa frequência de consulta mantidos para histórico. | Cloud Storage (Cold / Glacier) | Baixo Custo (Cold Storage) |
| **4. Expurgos & Retenção Legal** | Eliminação definitiva ou anonimização permanente após prazo legal. | Purge / Hard Delete | Custo Zero / Conformidade Legal |

---

## 2. Matriz de Retenção Legal de Dados (Data Retention Matrix)
Prazos obrigatórios de retenção de dados baseados em legislações regulatórias e requisitos de negócio (GDPR, LGPD, Marco Civil da Internet e Código Civil).

| Domínio / Tipo de Dado | Base Legal / Regulatório | Tempo de Retenção Ativa | Tempo de Arquivo (Cold) | Ação de Fim de Vida |
| :--- | :--- | :---: | :---: | :--- |
| **Logs de Acesso à Aplicação** | Marco Civil da Internet (Art. 15) | 6 Meses | 6 Meses | Expurgo Definitivo (*Hard Delete*) |
| **Dados Cadastrais de Clientes** | GDPR / LGPD / Código Civil | Vigência do Contrato | 5 Anos (Tributário/Civil) | Anonimização / Expurgo |
| **Histórico de Transações / Vendas** | Código Tributário Nacional | 1 Ano (Hot Analytics) | 5 Anos (Cold Lake) | Arquivamento Histórico Definitivo |
| **Dados de Candidatos (RH)** | LGPD / GDPR (Consentimento) | 1 Ano | 0 Meses | Expurgo Automático de Currículos |

---

## 3. Automação do Expurgo e Descarte Seguro via SQL
Rotina automatizada de limpeza e eliminação de registros expirados (*Purge Query*) para garantir conformidade contínua (*Privacy by Design*).

```sql
-- =============================================================================
-- SCRIPT DE EXPURGO AUTOMÁTICO DE DADOS EXPIRADOS (DATA PURGE & RETENTION)
-- Objetivo: Eliminar logs de acesso com mais de 1 ano (365 dias)
-- Frequência: Execução diária via job de ETL (Airflow / dbt)
-- =============================================================================

-- 1. EXPURGO DEFINITIVO DE LOGS DE ACESSO COM MAIS DE 365 DIAS
DELETE FROM db_prod.tb_logs_acesso
WHERE dt_log < CURRENT_DATE() - INTERVAL '365' DAY;

-- 2. ANONIMIZAÇÃO AUTOMÁTICA DE CLIENTES INATIVOS HÁ MAIS DE 5 ANOS
-- Em conformidade com o GDPR/LGPD (Direito ao Esquecimento / Fim da Finalidade)
UPDATE db_prod.tb_clientes
SET 
    nm_cliente = 'ANONIMO_LGPD',
    num_cpf = '000.000.000-00',
    ds_email = CONCAT('anonimo_', id_cliente, '@expurgado.local'),
    st_cliente = 'EXPURGADO'
WHERE 
    st_cliente = 'INATIVO' 
    AND dt_ultima_compra < CURRENT_DATE() - INTERVAL '1825' DAY; -- 5 Anos
