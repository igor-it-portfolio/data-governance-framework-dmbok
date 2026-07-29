# 🔒 Gobernanza de Seguridad de la Información, GDPR / CCPA y Control de Acceso (RBAC)

## 1. Niveles de Clasificación de la Información
Política internacional de privacidad y sensibilidad de datos aplicada a todas las tablas y activos de datos del entorno analítico, alineada con las normas **GDPR (Reglamento General de Protección de Datos - UE)**, **CCPA (California Consumer Privacy Act - EE.UU.)** y la **LGPD (BR)**.

| Nivel de Sensibilidad | Descripción | Ejemplos de Datos | Restricción de Acceso |
| :--- | :--- | :--- | :--- |
| **Público (Public)** | Información de dominio público que no genera impacto al negocio. | Catálogo de productos, precios públicos, documentación. | Acceso libre |
| **Interno (Internal)** | Datos operativos para uso exclusivo de los colaboradores. | Métricas operativas, IDs de procesos, logs. | Colaboradores autenticados |
| **Confidencial (Confidential)** | Datos estratégicos y financieros de alto valor de negocio. | Margen de beneficio, ingresos detallados, proyecciones. | Solo cargos autorizados |
| **Dato Personal / Sensible (PII / GDPR / CCPA)** | Información de identificación personal bajo regulación internacional (PII - *Personally Identifiable Information*). | Nombre, DNI/CPF/SSN, correo electrónico, dirección. | Requiere anonimización o enmascaramiento |

---

## 2. Matriz de Control de Acceso por Rol (RBAC - Role-Based Access Control)
Mapeo de permisos de acceso por perfil técnico/negocio sobre las capas del Data Lake (`db_analytics`), garantizando el principio de menor privilegio (*Least Privilege Principle*) exigido por el GDPR.

| Perfil / Rol | Permiso en Capa Bronce (Raw) | Permiso en Capa Plata (Silver) | Permiso en Capa Oro (Gold) | Visualización de PII (GDPR / CCPA) |
| :--- | :---: | :---: | :---: | :---: |
| **Data Engineer (Ingeniero)** | `ALL PRIVILEGES` | `ALL PRIVILEGES` | `READ / WRITE` | Datos Enmascarados |
| **Data Steward (Custodio)** | `NO ACCESS` | `READ` | `READ` | Lectura (Si está autorizado) |
| **Data Analyst (Analista)** | `NO ACCESS` | `NO ACCESS` | `READ` | **Solo Enmascarado** |
| **Business User (Ejecutivos)** | `NO ACCESS` | `NO ACCESS` | `READ` (Vista BI) | **Sin Acceso a PII** |
| **Data Protection Officer (DPO)** | `READ` (Auditoría) | `READ` (Auditoría) | `READ` (Auditoría) | Acceso para Auditoría |

---

## 3. Técnicas de Protección, Enmascaramiento y Anonimización de Datos
Ejemplo práctico de aplicación de enmascaramiento (*Data Masking*) y Hash unidireccional (SHA-256) en el motor analítico para protección y cumplimiento de PII ante los requisitos del GDPR (Art. 32º - Seguridad del Tratamiento).

```sql
-- =============================================================================
-- VISTA SEGURA CON ENMASCARAMIENTO DE DATOS PERSONALES (PII MASKING) - GDPR & CCPA
-- Objetivo: Permitir acceso analítico sin exponer datos personales de clientes
-- =============================================================================

CREATE OR REPLACE VIEW db_prod.vw_tb_clientes_segura AS
SELECT 
    id_cliente,
    
    -- Enmascaramiento del Nombre: Muestra solo el primer nombre (Minimización de datos - GDPR Art. 5)
    SPLIT(nm_cliente, ' ')[OFFSET(0)] AS nm_cliente_primeiro,
    
    -- Enmascaramiento del Documento/DNI/CPF: Muestra solo los últimos 2 dígitos
    CONCAT('***.***.***-', RIGHT(num_cpf, 2)) AS num_cpf_mascarado,
    
    -- Anonimización del Correo vía HASH (SHA-256) para permitir JOINs sin exponer el correo real
    SHA256(ds_email) AS ds_email_hash,
    
    st_cliente,
    dt_cadastro
FROM db_prod.tb_clientes;
