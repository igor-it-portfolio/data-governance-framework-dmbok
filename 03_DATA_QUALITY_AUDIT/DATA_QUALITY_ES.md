# 🔍 Matriz de Calidad de Datos (Data Quality) y Script de Auditoría SQL

## 1. Reglas de Calidad por Dimensión (DAMA-DMBOK)
Esta matriz define las reglas de validación aplicadas sobre la tabla `db_prod.tb_clientes` para asegurar que los datos cumplan con los estándares mínimos de confiabilidad antes de ser consumidos por los paneles ejecutivos.

| Dimensión DQ | Regla de Negocio / Prueba | Criterio de Aceptación (SLA) | Impacto en el Negocio |
| :--- | :--- | :---: | :--- |
| **Completitud (Completeness)** | El campo `nm_cliente` no puede contener valores nulos o vacíos. | 100% completado | Impide la generación de informes de clientes sin identificación. |
| **Unicidad (Uniqueness)** | La clave primaria `id_cliente` debe ser única en la tabla. | 0% duplicados | Evita el doble conteo de métricas en la base de clientes. |
| **Validez (Validity)** | El campo `ds_email` debe poseer una estructura válida de correo (contener `@` y dominio). | ≥ 98% válido | Garantiza la operatividad de campañas de marketing y contacto. |
| **Integridad (Integrity)** | El campo `st_cliente` debe contener solo valores permitidos (`ACTIVO`, `INACTIVO`, `BLOQUEADO`). | 100% conforme | Evita fallas en filtros de dashboards y rupturas de pipelines. |
| **Actualidad (Timeliness)** | Los datos deben actualizarse diariamente antes de las 06:00 AM. | Retraso máx. 2h | Asegura que la dirección tome decisiones con datos del día anterior. |

---

## 2. Script SQL de Auditoría de Calidad de Datos
Las consultas SQL a continuación fueron desarrolladas para ejecutarse de manera automatizada sobre la tabla `db_prod.tb_clientes` para identificar inconsistencias y generar alertas de gobernanza.

```sql
-- 1. PRUEBA DE UNICIDAD (Identificación de Claves Primarias Duplicadas)
SELECT 
    id_cliente,
    COUNT(*) AS total_duplicadas
FROM db_prod.tb_clientes
GROUP BY id_cliente
HAVING COUNT(*) > 1;

-- 2. PRUEBA DE COMPLETITUD (Mapeo de Nulos en Campos Obligatorios)
SELECT 
    COUNT(*) AS total_registros,
    SUM(CASE WHEN nm_cliente IS NULL OR TRIM(nm_cliente) = '' THEN 1 ELSE 0 END) AS nulos_nombre,
    SUM(CASE WHEN num_cpf IS NULL OR TRIM(num_cpf) = '' THEN 1 ELSE 0 END) AS nulos_cpf,
    SUM(CASE WHEN ds_email IS NULL OR TRIM(ds_email) = '' THEN 1 ELSE 0 END) AS nulos_email
FROM db_prod.tb_clientes;

-- 3. PRUEBA DE VALIDEZ (Identificación de Formatos Inválidos de Correo y CPF)
SELECT 
    id_cliente,
    ds_email,
    num_cpf
FROM db_prod.tb_clientes
WHERE 
    ds_email NOT LIKE '%@%.%' 
    OR LENGTH(num_cpf) <> 14;

-- 4. PRUEBA DE INTEGRIDAD DEL DOMINIO (Valores fuera del estándar permitido)
SELECT 
    id_cliente,
    st_cliente
FROM db_prod.tb_clientes
WHERE st_cliente NOT IN ('ACTIVO', 'INACTIVO', 'BLOQUEADO') 
   OR st_cliente IS NULL;

-- 5. INFORME CONSOLIDADO DE INDICADORES DE CALIDAD (DQ Scorecard)
SELECT 
    CURRENT_DATE() AS dt_auditoria,
    COUNT(*) AS total_linhas_auditadas,
    ROUND((1.0 - (SUM(CASE WHEN id_cliente IS NULL THEN 1 ELSE 0 END) / COUNT(*))) * 100, 2) AS pct_completitud_pk,
    ROUND((1.0 - (SUM(CASE WHEN st_cliente NOT IN ('ACTIVO', 'INACTIVO', 'BLOQUEADO') THEN 1 ELSE 0 END) / COUNT(*))) * 100, 2) AS pct_conformidad_estado
FROM db_prod.tb_clientes;
