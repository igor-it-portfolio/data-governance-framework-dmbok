# ⏳ Gestión del Ciclo de Vida del Dato, Políticas de Retención y Borrado Seguro

## 1. Fases del Ciclo de Vida del Dato (Data Lifecycle)
Alineado con el estándar **DAMA-DMBOK**, esta política define el flujo de madurez y almacenamiento de la información desde su creación hasta su eliminación final.

| Fase del Ciclo | Descripción Operativa | Capa de Almacenamiento | Política de Costo / Rendimiento |
| :--- | :--- | :--- | :--- |
| **1. Creación / Ingestión** | Captura de datos brutos de sistemas OLTP, APIs y socios. | Capa Bronce (Raw) | Alto Rendimiento (Hot Storage) |
| **2. Procesamiento y Uso** | Limpieza, transformación, reglas de negocio y consumo en BI. | Capas Plata y Oro | Alto Rendimiento (Hot Storage) |
| **3. Archivo (Warm/Cold)** | Datos con baja frecuencia de consulta mantenidos para histórico. | Cloud Storage (Cold / Glacier) | Bajo Costo (Cold Storage) |
| **4. Purga y Retención Legal** | Eliminación definitiva o anonimización permanente tras plazo legal. | Purge / Hard Delete | Costo Cero / Cumplimiento Legal |

---

## 2. Matriz de Retención Legal de Datos (Data Retention Matrix)
Plazos obligatorios de retención de datos basados en regulaciones internacionales y requisitos de negocio (GDPR, CCPA y Código Comercial).

| Dominio / Tipo de Dato | Base Legal / Regulatoria | Tiempo de Retención Activa | Tiempo de Archivo (Cold) | Acción de Fin de Vida |
| :--- | :--- | :---: | :---: | :--- |
| **Logs de Acceso a la Aplicación** | Regulaciones de Internet / GDPR | 6 Meses | 6 Meses | Purga Definitiva (*Hard Delete*) |
| **Datos Catastrales de Clientes** | GDPR / CCPA / Código Comercial | Vigencia del Contrato | 5 Años (Tributario/Civil) | Anonimización / Purga |
| **Histórico de Transacciones / Ventas** | Código Tributario Nacional | 1 Año (Hot Analytics) | 5 Años (Cold Lake) | Archivo Histórico Definitivo |
| **Datos de Candidatos (RRHH)** | GDPR (Consentimiento) | 1 Año | 0 Meses | Purga Automática de Currículums |

---

## 3. Automatización de Purga y Borrado Seguro vía SQL
Rutina automatizada de limpieza y eliminación de registros expirados (*Purge Query*) para garantizar cumplimiento continuo (*Privacy by Design*).

```sql
-- 1. PURGA DEFINITIVA DE LOGS DE ACCESO CON MÁS DE 365 DÍAS
DELETE FROM db_prod.tb_logs_acesso
WHERE dt_log < CURRENT_DATE() - INTERVAL '365' DAY;

-- 2. ANONIMIZACIÓN AUTOMÁTICA DE CLIENTES INACTIVOS HACE MÁS DE 5 AÑOS
-- En cumplimiento con el GDPR (Derecho al Olvido / Fin de la Finalidad)
UPDATE db_prod.tb_clientes
SET 
    nm_cliente = 'ANONIMO_GDPR',
    num_cpf = '000.000.000-00',
    ds_email = CONCAT('anonimo_', id_cliente, '@expurgado.local'),
    st_cliente = 'EXPURGADO'
WHERE 
    st_cliente = 'INACTIVO' 
    AND dt_ultima_compra < CURRENT_DATE() - INTERVAL '1825' DAY;
