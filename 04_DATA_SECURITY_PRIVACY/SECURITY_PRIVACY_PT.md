# 🔒 Governança de Segurança da Informação, GDPR / LGPD e Controle de Acesso (RBAC)

## 1. Níveis de Classificação da Informação
Política internacional de privacidade e sensibilidade de dados aplicada a todas as tabelas e ativos de dados do ambiente analítico, alinhada às normas **GDPR (Regulamento Geral sobre a Proteção de Dados - UE)** e **LGPD (Lei Geral de Proteção de Dados - BR)**.

| Nível de Sensibilidade | Descrição | Exemplos de Dados | Restrição de Acesso |
| :--- | :--- | :--- | :--- |
| **Público (Public)** | Informações de domínio público que não geram impacto ao negócio. | Catálogo de produtos, preços públicos, documentações. | Acesso livre |
| **Interno (Internal)** | Dados operacionais para uso exclusivo dos colaboradores. | Métricas operacionais, IDs de processos, logs. | Colaboradores autenticados |
| **Confidencial (Confidential)** | Dados estratégicos e financeiros de alto valor de negócio. | Margem de lucro, receita detalhada, projeções. | Apenas cargos autorizados |
| **Dado Pessoal / Sensível (PII / GDPR / LGPD)** | Informações de identificação pessoal sob regulação internacional (PII - *Personally Identifiable Information*). | Nome, CPF/DNI/SSN, e-mail, telefone, endereço. | Requer anonimização ou mascaramento |

---

## 2. Matriz de Controle de Acesso por Papel (RBAC - Role-Based Access Control)
Mapeamento de permissões de acesso por perfil técnico/negócio sobre as camadas do Data Lake (`db_analytics`), garantindo o princípio do menor privilégio (*Least Privilege Principle*) exigido pelo GDPR.

| Perfil / Role | Permissão na Camada Bronze (Raw) | Permissão na Camada Silver (Prata) | Permissão na Camada Gold (Ouro) | Visualização de PII (GDPR/LGPD) |
| :--- | :---: | :---: | :---: | :---: |
| **Data Engineer (Engenheiro)** | `ALL PRIVILEGES` | `ALL PRIVILEGES` | `READ / WRITE` | Dados Mascarados |
| **Data Steward (Guardião)** | `NO ACCESS` | `READ` | `READ` | Leitura (Se autorizado) |
| **Data Analyst (Analista)** | `NO ACCESS` | `NO ACCESS` | `READ` | **Apenas Mascarado** |
| **Business User (Executivos)** | `NO ACCESS` | `NO ACCESS` | `READ` (Visão BI) | **Sem Acesso a PII** |
| **Data Protection Officer (DPO)** | `READ` (Auditoria) | `READ` (Auditoria) | `READ` (Auditoria) | Acesso para Auditoria |

---

## 3. Técnicas de Proteção, Mascaramento e Anonimização de Dados
Exemplo prático de aplicação de mascaramento (*Data Masking*) e Hash unidirecional (SHA-256) no motor analítico para proteção e conformidade de PII perante os requisitos do GDPR (Art. 32º - Segurança do Processamento).

```sql
-- =============================================================================
-- VISTA SEGURA COM MASCARAMENTO DE DADOS PESSOAIS (PII MASKING) - GDPR & LGPD
-- Objetivo: Permitir acesso analítico sem expor dados pessoais dos clientes
-- =============================================================================

CREATE OR REPLACE VIEW db_prod.vw_tb_clientes_segura AS
SELECT 
    id_cliente,
    
    -- Mascaramento do Nome: Exibe apenas o primeiro nome (Minimização de dados - GDPR Art. 5)
    SPLIT(nm_cliente, ' ')[OFFSET(0)] AS nm_cliente_primeiro,
    
    -- Mascaramento do Documento/CPF: Exibe apenas os 2 últimos dígitos (ex: ***.***.***-00)
    CONCAT('***.***.***-', RIGHT(num_cpf, 2)) AS num_cpf_mascarado,
    
    -- Anonimização do E-mail via HASH (SHA-256) para permitir JOINs sem expor o e-mail real
    SHA256(ds_email) AS ds_email_hash,
    
    st_cliente,
    dt_cadastro
FROM db_prod.tb_clientes;
