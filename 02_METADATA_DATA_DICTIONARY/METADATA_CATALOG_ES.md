# 📖 Catálogo de Metadatos, Glosario y Linaje de Datos

## 1. Glosario de Negocio (Business Glossary)
El Glosario de Negocio estandariza los conceptos corporativos del Dominio de Clientes y Ventas para garantizar una visión única y sin ambigüedades entre las áreas de negocio y TI.

| Término de Negocio | Definición de Negocio | Regla de Negocio | Data Steward Responsable |
| :--- | :--- | :--- | :--- |
| **Cliente Activo** | Cliente que realizó al menos 1 compra aprobada en los últimos 90 días. | Excluye compras canceladas o con *chargeback*. | Custodio de Ventas |
| **Ingreso Neto** | Valor total de ventas facturadas menos impuestos y devoluciones. | `Ingreso Bruto - (Impuestos + Devoluciones)` | Custodio Financiero |
| **Estado de la Cuenta** | Situación catastral del cliente en el ecosistema de la empresa. | Valores aceptados: `ACTIVO`, `INACTIVO`, `BLOQUEADO`. | Custodio de Atención |

---

## 2. Diccionario de Datos Técnico (Technical Data Dictionary)
Mapeo de la estructura física de las tablas en el entorno de base de datos / Data Lake (`db_analytics_prod`).

### 📑 Tabla: `tb_clientes` (Dominio: Clientes)
* **Descripción:** Almacena los datos catastrales y de identificación de clientes de la plataforma.
* **Frecuencia de Actualización:** Diaria (Batch a las 02:00 AM).

| Nombre de Columna | Tipo de Dato | ¿Permite Nulo? | Clave (PK/FK) | Clasificación de Privacidad (LGPD/GDPR) | Descripción del Campo |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id_cliente` | `STRING` | No | **PK** | Pública | ID único e inmutable del cliente (UUID). |
| `nm_cliente` | `STRING` | No | - | **Dato Personal (PII)** | Nombre completo del cliente registrado. |
| `num_cpf` | `STRING` | No | - | **Dato Personal Sensible (PII/Máscara)** | Número de identificación (debe almacenarse enmascarado). |
| `ds_email` | `STRING` | Sí | - | **Dato Personal (PII)** | Correo electrónico de contacto principal. |
| `st_cliente` | `STRING` | No | - | Interna / Operativa | Estado del cliente (`ACTIVO`, `INACTIVO`, `BLOQUEADO`). |
| `dt_cadastro` | `TIMESTAMP` | No | - | Interna | Fecha y hora en que se realizó el registro. |

---

## 3. Linaje de Datos (Data Lineage)
Representación del flujo de integración y transformación del dato desde su origen hasta el consumo final.

### 🔄 Flujo: Origen ➔ Procesamiento ➔ Consumo (Dashboard de Ventas)

* **Etapa 1 (Sistema Origen):** App / PostgreSQL (`tb_raw_customers`) — Captura de datos brutos.
* **Etapa 2 (Data Lake / Ingestión Bronce):** Google BigQuery / AWS (`db_raw.tb_clients_daily`) — Almacenamiento histórico.
* **Etapa 3 (Capa Analítica Oro):** Databricks / SQL (`db_prod.tb_clientes`) — Aplicación de reglas de calidad, eliminación de duplicados y enmascaramiento de CPF (PII).
* **Etapa 4 (Consumo Final):** Power BI (`Dashboard de Churn y Rendimiento`) — Lectura para ejecutivos y directores.
