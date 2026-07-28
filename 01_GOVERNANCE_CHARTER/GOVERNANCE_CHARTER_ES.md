# 📜 Carta de Operacionalización de Gobernanza de Datos (Data Governance Charter)

## 1. Visión General y Objetivos
Esta Carta de Gobernanza establece las directrices, responsabilidades y el modelo operativo para la gestión de activos de datos en la organización, basado en las mejores prácticas de **DAMA-DMBOK**.

El objetivo principal es garantizar que los datos corporativos sean **confiables, estandarizados, seguros y alineados con los requisitos regulatorios (LGPD/GDPR)**, maximizando su valor estratégico para las decisiones de negocio.

---

## 2. Estructura de Dominios de Datos
Para efectos de gobernanza y asignación de responsabilidades, los activos de datos de la empresa se dividen en los siguientes **Dominios Corporativos**:

| Dominio de Datos | Descripción del Alcance | Ejemplos de Entidades Principales |
| :--- | :--- | :--- |
| **Clientes (Customer)** | Datos catastrales, identificación y contacto de clientes | `tb_clientes`, `tb_enderecos`, `tb_contatos` |
| **Financiero (Finance)** | Transacciones, facturación, medios de pago y contabilidad | `tb_vendas`, `tb_pagamentos`, `tb_faturamento` |
| **Productos (Product)** | Catálogo de productos, servicios y precios | `tb_produtos`, `tb_categorias`, `tb_precos` |

---

## 3. Matriz de Responsabilidades (Matriz RACI)

La siguiente matriz define el rol de cada actor en la gestión de datos:
* **R (Responsible / Responsable):** Quien ejecuta la tarea.
* **A (Accountable / Aprobador):** Quien responde por la entrega y aprueba el resultado (solo 1 por tarea).
* **C (Consulted / Consultado):** Quien debe ser consultado antes/durante la ejecución.
* **I (Informed / Informado):** Quien debe ser notificado sobre el progreso o resultado.

### 👥 Roles Mapeados:
1. **Data Owner (Propietario del Dato):** Ejecutivo/Gerente del área de negocio responsable del activo.
2. **Data Steward (Custodio de Negocio):** Especialista del área de negocio enfocado en la calidad y reglas de negocio.
3. **Data Custodian (TI / Ingeniería):** Equipo técnico responsable de la infraestructura y seguridad de la base de datos.
4. **Governante de Dados / Gobernador de Datos (Nuestra Actuación):** Responsable de facilitar, auditar y garantizar los procesos de gobernanza.

---

### 📊 Tabla RACI de Gobernanza de Datos

| Actividad / Proceso de Gobernanza | Data Owner | Data Steward | Data Custodian (TI) | Gobernador de Datos |
| :--- | :---: | :---: | :---: | :---: |
| Definición del significado de términos de negocio | **A** | **R** | I | **C** |
| Registro y actualización del Diccionario de Datos | C | **R** | I | **A / R** |
| Aprobación de reglas de acceso y permisos | **A** | C | **R** | C |
| Identificación y clasificación de PII (LGPD/GDPR) | C | **R** | C | **A** |
| Corrección de fallas en pipeline de datos (ETL) | I | I | **A / R** | C |
| Homologación de calidad de las bases de datos | C | **R** | C | **A** |
| Ejecución de auditorías periódicas en SQL | I | C | I | **A / R** |

---

## 4. Ritos y Comunicación
* **Comité de Gobernanza (Mensual):** Reunión entre el Gobernador de Datos, DPO y Data Owners para revisión de KPIs de calidad y nuevos proyectos.
* **Reunión Operativa de Stewardship (Quincenal):** Alineación entre el Gobernador de Datos y los Data Stewards para revisión y actualización del catálogo.
