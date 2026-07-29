# 🔍 Matriz de Qualidade de Dados (Data Quality) e Script de Auditoria SQL

## 1. Regras de Qualidade por Dimensão (DAMA-DMBOK)
Esta matriz define as regras de validação aplicadas sobre a tabela `db_prod.tb_clientes` para assegurar que os dados atendam aos padrões mínimos de confiabilidade antes de serem consumidos pelos painéis executivos.

| Dimensão DQ | Regra de Negócio / Teste | Critério de Aceite (SLA) | Impacto no Negócio |
| :--- | :--- | :---: | :--- |
| **Completude (Completeness)** | O campo `nm_cliente` não pode conter valores nulos ou vazios. | 100% preenchido | Impede a geração de relatórios de clientes sem identificação. |
| **Unicidade (Uniqueness)** | A chave primária `id_cliente` deve ser única na tabela. | 0% duplicatas | Evita a contagem duplicada de métricas de base de clientes. |
| **Validade (Validity)** | O campo `ds_email` deve possuir uma estrutura válida de e-mail (conter `@` e domínio). | ≥ 98% válido | Garante a operabilidade de campanhas de marketing e contato. |
| **Integridade (Integrity)** | O campo `st_cliente` deve conter apenas valores permitidos (`ATIVO`, `INATIVO`, `BLOQUEADO`). | 100% conforme | Evita falhas em filtros de dashboards e quebras de pipeline. |
| **Atualidade (Timeliness)** | Os dados devem ser atualizados diariamente até as 06:00 AM. | Atraso máx. 2h | Assegura que a diretoria tome decisões com dados do dia anterior. |

---

## 2. Script SQL de Auditoria de Qualidade de Dados
As consultas SQL abaixo foram desenvolvidas para rodar de forma automatizada sobre a tabela `db_prod.tb_clientes` (SGBD: BigQuery / PostgreSQL / Databricks SQL) para identificar inconsistências e disparar alertas de governança.

```sql
-- =============================================================================
-- SCRIPT DE AUDITORIA DE QUALIDADE DE DADOS (DATA QUALITY AUDIT)
-- Tabela Alvo: db_prod.tb_clientes
-- Autor: Governança de Dados
-- Framework: DAMA-DMBOK
-- =============================================================================

-- 1. TESTE DE UNICIDADE (Identificação de Chaves Primárias Duplicadas)
SELECT 
    id_cliente,
    COUNT(*) AS total_duplicadas
FROM db_prod.tb_clientes
GROUP BY id_cliente
HAVING COUNT(*) > 1;


-- 2. TESTE DE COMPLETUDE (Mapeamento de Nulos em Campos Obrigatórios)
SELECT 
    COUNT(*) AS total_registros,
    SUM(CASE WHEN nm_cliente IS NULL OR TRIM(nm_cliente) = '' THEN 1 ELSE 0 END) AS nulos_nome,
    SUM(CASE WHEN num_cpf IS NULL OR TRIM(num_cpf) = '' THEN 1 ELSE 0 END) AS nulos_cpf,
    SUM(CASE WHEN ds_email IS NULL OR TRIM(ds_email) = '' THEN 1 ELSE 0 END) AS nulos_email
FROM db_prod.tb_clientes;


-- 3. TESTE DE VALIDADE (Identificação de Formatos Inválidos de E-mail e CPF)
SELECT 
    id_cliente,
    ds_email,
    num_cpf
FROM db_prod.tb_clientes
WHERE 
    -- Valida se o e-mail não possui formato padrão de e-mail
    ds_email NOT LIKE '%@%.%' 
    -- Valida se o CPF mascarado possui tamanho incorreto (diferente de 14 caracteres: '123.***.***-00')
    OR LENGTH(num_cpf) <> 14;


-- 4. TESTE DE INTEGRIDADE DO DOMÍNIO (Valores fora do padrão permitido no Glossário)
SELECT 
    id_cliente,
    st_cliente
FROM db_prod.tb_clientes
WHERE st_cliente NOT IN ('ATIVO', 'INATIVO', 'BLOQUEADO') 
   OR st_cliente IS NULL;


-- 5. RELATÓRIO CONSOLIDADO DE INDICADORES DE DATA QUALITY (DQ Scorecard)
SELECT 
    CURRENT_DATE() AS dt_auditoria,
    COUNT(*) AS total_linhas_auditadas,
    ROUND((1.0 - (SUM(CASE WHEN id_cliente IS NULL THEN 1 ELSE 0 END) / COUNT(*))) * 100, 2) AS pct_completude_pk,
    ROUND((1.0 - (SUM(CASE WHEN st_cliente NOT IN ('ATIVO', 'INATIVO', 'BLOQUEADO') THEN 1 ELSE 0 END) / COUNT(*))) * 100, 2) AS pct_conformidade_status
FROM db_prod.tb_clientes;
