# HelixCore — Business Intelligence Comercial

> **Reto Técnico · Business Intelligence Analyst - Sales · Alegra 2026**  
> Autor: Fredys Caballero · BI Analyst · [LinkedIn](https://www.linkedin.com/in/fcaballerosoto/)

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)
![Claude AI](https://img.shields.io/badge/Claude%20AI-D97706?style=flat&logo=anthropic&logoColor=white)
![MCP Server](https://img.shields.io/badge/MCP%20Server-1D9E75?style=flat)
![DAX](https://img.shields.io/badge/DAX-0C447C?style=flat)
![Power Query](https://img.shields.io/badge/Power%20Query-085041?style=flat)

---

## Tabla de contenidos

1. [Contexto del reto](#contexto-del-reto)
2. [Objetivos y entregables](#objetivos-y-entregables)
3. [Stack tecnológico](#stack-tecnológico)
4. [Instrucciones de uso](#instrucciones-de-uso)
5. [Enfoque general y decisiones de diseño](#enfoque-general-y-decisiones-de-diseño)
6. [Arquitectura del modelo de datos](#arquitectura-del-modelo-de-datos)
7. [Cómo se construyó — Claude + MCP Server](#cómo-se-construyó--claude--mcp-server)
8. [Los 3 reportes del dashboard](#los-3-reportes-del-dashboard)
9. [Insights principales y accionables](#insights-principales-y-accionables)
10. [Supuestos clave](#supuestos-clave)
11. [Segunda iteración — Roadmap](#segunda-iteración--roadmap)
12. [Inventario completo del modelo](#inventario-completo-del-modelo)
13. [Glosario — Términos, abreviaturas y medidas DAX](#glosario--términos-abreviaturas-y-medidas-dax)

---

## Contexto del reto

HelixCore es una empresa SaaS B2B/SMB en rápido crecimiento en Latinoamérica. Opera con un modelo híbrido: **self-service**, **sales-assisted** y **partners contables**. El equipo de Sales y RevOps llevaba meses tomando decisiones con poca claridad — había sensación de que había oportunidades que se estaban perdiendo, pero nadie sabía exactamente dónde ni por qué.

El reto simula una situación real: información imperfecta, tiempo limitado (2 días calendario), y necesidad de decidir con lo que tienes.

> *"Nos importa mucho más cómo piensas que cuánto construyes."* — Enunciado del reto

---

## Objetivos y entregables

| # | Entregable | Estado |
|---|---|---|
| 1 | **Solución analítica** — Dashboard en Power BI con 3 reportes (Ejecutivo, Sales, RevOps) | ✅ Completado |
| 2 | **Video 5-7 min** — Presentación a directores + recorrido del dashboard + uso de IA | ✅ Completado |
| 3 | **README** — Instrucciones, enfoque, supuestos, decisiones de diseño, roadmap | ✅ Este documento |

Los criterios de evaluación del reto son:
- Cómo estructuro un problema ambiguo
- Cómo priorizo
- Qué tan bien convierto datos en una herramienta útil para tomar decisiones
- Uso de IA para trabajar mejor, no solo más rápido

---

## Stack tecnológico

| Herramienta | Rol en el proyecto |
|---|---|
| **Power BI Desktop** | Dashboard, modelado de datos, visualizaciones |
| **Claude AI (Sonnet 4.6)** | Asistente BI senior — análisis, DAX, diseño, insights |
| **Power BI MCP Server** | Protocolo de conexión Claude → Power BI (modelado sin abrir el editor) |
| **Power Query (M)** | Transformaciones, limpieza, tabla Calendario, parámetros de entorno |
| **DAX** | 150 medidas organizadas en 13 carpetas temáticas |
| **Google Drive** | Almacenamiento de los datasets fuente |

### ¿Por qué Power BI y no HTML/React?

El reto dice que el formato es **libre**. Elegí Power BI por tres razones concretas:

**1. Alineación con el negocio.** HelixCore es una empresa SaaS B2B con equipo de Sales y RevOps. Power BI es exactamente la herramienta que este tipo de empresas usa en producción para tomar decisiones. Presentar un dashboard en Power BI demuestra pensar como negocio, no como desarrollador.

**2. Demostración del enfoque AI First.** El reto pide "un enfoque AI first" y "mostrar cómo usaste la IA con momentos concretos en pantalla". Usar el MCP Server para modelar Power BI directamente desde una conversación con Claude es la demostración más concreta de ese enfoque. No es usar IA para generar HTML — es conectar la IA al motor analítico y pedirle que construya el modelo en lenguaje natural.

**3. El reto dice explícitamente "no evaluamos la sofisticación del front-end".** Lo que sí evalúan es claridad, utilidad y accionabilidad. Power BI entrega las tres con filtros interactivos, slicers, y visualizaciones estándar de la industria.

---

## Instrucciones de uso

### Prerrequisitos

- Power BI Desktop instalado (versión Marzo 2026 o superior)
- Los archivos CSV del dataset en una carpeta local
- Acceso al archivo `RetoTecnicoAlegra.pbix`

### Configurar la ruta de datos

El modelo usa un **parámetro de Power Query** llamado `RutaOrigen` para apuntar a los archivos CSV. Para cambiar la ruta:

1. Abre Power BI Desktop → `Inicio → Transformar datos → Editor de Power Query`
2. En el panel izquierdo, busca el parámetro **`RutaOrigen`**
3. Cambia el valor a la ruta donde tienes los CSV en tu equipo
4. Ejemplo: `C:\Datos\HelixCore\datasets`
5. Cierra el editor y haz clic en **Aplicar cambios**

### Estructura esperada de carpeta

```
datasets/
├── leads.csv
├── deals.csv
├── funnel_stage_history.csv
├── sales_activities.csv
├── call_logs.csv
├── bot_interactions.csv
├── product_signals.csv
├── subscriptions.csv
├── partner_portfolios.csv
├── reps.csv
├── rep_daily_capacity.csv
└── data_dictionary.csv
```

### Navegar el dashboard

El archivo tiene 3 páginas (pestañas):

| Pestaña | Audiencia | Descripción |
|---|---|---|
| `Resumen Ejecutivo` | CEO / Directores | KPIs globales, tendencia MRR, segmentos, países |
| `Sales Performance` | VP Sales / Líderes | Funnel, reps, bot IA, razones de pérdida |
| `RevOps & Eficiencia` | RevOps / Operaciones | Tiempos, canales, capacidad, experimentos, partners |

Todos los reportes comparten **slicers sincronizados** de Periodo, País y Segmento.

---

## Enfoque general y decisiones de diseño

### Principio de diseño: de lo general a lo particular

Los 3 reportes siguen una narrativa descendente:

```
Reporte 1 (Ejecutivo) → ¿Cómo va el negocio?
    ↓
Reporte 2 (Sales) → ¿Dónde están los problemas comerciales?
    ↓
Reporte 3 (RevOps) → ¿Cómo los operamos y optimizamos?
```

Cada reporte tiene una audiencia definida, un lenguaje adaptado, y métricas específicas para esa audiencia. No es un dashboard genérico con todos los datos — es una herramienta diseñada para tomar decisiones concretas.

### Principio de visualización: señal vs ruido

Siguiendo las recomendaciones del libro *Storytelling with Data* de Cole Nussbaumer Knaflic:
- Cada visual tiene un propósito específico y una pregunta que responde
- Los colores codifican significado (verde = bueno, naranja = atención, rojo = alerta)
- Las etiquetas muestran solo los números relevantes
- El fondo neutro (`#F1EFE8`) reduce la carga visual

### Priorización del análisis

Con 11 datasets disponibles, prioricé en este orden:

1. **Funnel comercial** (`funnel_stage_history`, `deals`, `leads`) — core del problema
2. **Performance de reps** (`reps`, `rep_daily_capacity`, `call_logs`) — dónde está la eficiencia
3. **Señales de producto** (`product_signals`, `bot_interactions`) — el ángulo de IA
4. **Retención** (`subscriptions`) — el resultado de todo lo anterior
5. **Partners** (`partner_portfolios`) — el canal con mayor palanca

---

## Arquitectura del modelo de datos

### Diagrama simplificado (Star Schema)

```
                    ┌─────────────────┐
                    │   Calendario    │  ← Date Table oficial
                    │  (243 fechas)   │      Ene–Ago 2026
                    └────────┬────────┘
                             │ activa: leads→Cal
                             │
          ┌──────────────────▼──────────────────┐
          │                leads                 │  ← DIMENSIÓN CENTRAL
          │           (5,200 registros)           │
          └──┬──┬──┬──┬──┬──┬──┬──┬─────────────┘
             │  │  │  │  │  │  │  │
    M:1 ─────┘  │  │  │  │  │  │  └─── 1:1 BothDir ─── partner_portfolios
    deals        │  │  │  │  │  └────── 1:1 BothDir ─── subscriptions
                 │  │  │  │  └───────── 1:1 BothDir ─── product_signals
    M:1 ─────────┘  │  │  └──────────── 1:1 BothDir ─── bot_interactions
    funnel_stage     │  │
    M:1 ─────────────┘  └── call_logs (M:1 activa)
    sales_activities

          ┌──────────┐
          │   reps   │  ← DIMENSIÓN (18 registros)
          └─────┬────┘
                │ activas: deals (owner_rep_id), rep_daily_capacity
                │ inactivas: call_logs, sales_activities, funnel, partner_portfolios
```

### Tablas del modelo

| Tabla | Tipo | Filas | Descripción |
|---|---|---|---|
| `leads` | Dimensión (central) | 5,200 | Prospectos recibidos |
| `reps` | Dimensión | 18 | Equipo comercial (SDR, AE, KAM) |
| `Calendario` | Date Table | 243 | Ene–Ago 2026, festivos Colombia |
| `deals` | Hecho | 693 | Deals cerrados (won/lost) |
| `funnel_stage_history` | Hecho | 26,436 | Historial de etapas por lead |
| `sales_activities` | Hecho | 30,160 | Actividades comerciales |
| `call_logs` | Hecho | 12,594 | Registro de llamadas |
| `bot_interactions` | Puente/Dim | 3,145 | Interacciones con bot de IA |
| `product_signals` | Puente/Dim | 5,200 | PQL score, onboarding, experimentos |
| `subscriptions` | Hecho | 303 | Suscripciones activas y churned |
| `partner_portfolios` | Hecho | 164 | Portafolio de partners KAM |
| `rep_daily_capacity` | Hecho | 2,322 | Capacidad diaria por rep |
| `_Medidas` | Contenedor | — | Tabla vacía que contiene las 150 medidas |

### Relaciones: 22 en total

- **10 activas** — usadas por defecto en los visuales
- **12 inactivas** — activadas con `USERELATIONSHIP()` en medidas específicas

### Columnas calculadas añadidas en Power Query

| Tabla | Columna | Descripción |
|---|---|---|
| `deals` | `deal_cycle_days` | Días entre creación y cierre del deal |
| `funnel_stage_history` | `stage_order` | Orden numérico de etapa (1=New, 10=Closed Lost) |
| `funnel_stage_history` | `stage_duration_hours` | Horas en esa etapa antes de avanzar |
| `rep_daily_capacity` | `productive_minutes` | on_call + meeting (tiempo de venta real) |
| `rep_daily_capacity` | `utilization_rate` | productive_minutes / logged_in_minutes |
| `subscriptions` | `dias_activo` | Días desde inicio hasta churn o fecha corte |
| `reps` | `rep_display` | Nombre completo con rol (ej: "Laura Gómez (AE)") |
| `call_logs` | `duration_minutes` | Duración en minutos (desde segundos) |

---

## Cómo se construyó — Claude + MCP Server

Este proyecto fue construido usando el flujo de trabajo documentado en [PowerBI_MCP_WayOfWork_v2](https://docs.google.com/document/d/1ycC6dJ4LBHsU9q6aFWZky7oiozxYQAVpqj5Vw68qyh8), que define cómo usar Claude Desktop + Power BI MCP Server para modelar datos en lenguaje natural.

### Flujo de trabajo utilizado

```
PARTE 1 — Configuración del entorno
├── Instalar Power BI Modelling MCP Server (extensión VS Code)
├── Conectar Claude Desktop con el MCP Server (claude_desktop_config.json)
└── Verificar conexión al .pbix activo en Power BI Desktop

PARTE 2 — Modelado paso a paso
├── Paso 0 → Prompt de 6 capas (rol, contexto, objetivo, restricciones, formato, criterios)
├── Paso 1 → Verificar conexión: "Conéctate al .pbix RetoTecnicoAlegra"
├── Paso 2 → Diagnóstico completo del modelo (tipos, cardinalidad, fechas, calidad)
├── Paso 2.1 → Transformaciones Power Query (encoding UTF-8, tipos decimal, columnas calc.)
├── Paso 2.2 → Parámetro RutaOrigen para cambio de entorno
├── Paso 2.3 → Tabla Calendario en M (243 días, festivos Colombia, 15 columnas)
├── Paso 2.4 → Eliminar 28 relaciones auto-detectadas incorrectas
├── Paso 2.5 → Crear 22 relaciones star schema limpias
├── Paso 3 → 150 medidas DAX en 13 carpetas temáticas
├── Paso 4 → Validación completa (integridad referencial, tipos, calendario, KPIs)
└── Paso 5 → Mockups de los 3 reportes con datos reales del modelo

PARTE 3 — Análisis y entregables
├── Extracción de insights con consultas DAX desde Claude
├── Diseño de la presentación (5 slides + guión de video)
└── Documentación (glosario, guión, README)
```

### El prompt de 6 capas

La sesión comenzó con un prompt estructurado en 6 capas que definió el contexto completo antes de ejecutar cualquier acción:

```
1. ROL: BI Analyst Senior con 25 años de experiencia en Sales/RevOps, 
   experiencia en IA y Power BI MCP, empresas SaaS B2B/SMB.

2. CONTEXTO: Reto técnico para Alegra. Empresa simulada: HelixCore.
   Modelo híbrido: self-service, sales-assisted, partners contables.

3. OBJETIVO: Dashboard en Power BI (3 reportes: Ejecutivo, Sales, RevOps)
   + Presentación + README. Enfoque AI First.

4. RESTRICCIONES: 1 día de trabajo. Priorizar lo importante.
   Deadline: 22/Abril/2026.

5. FORMATO: Power BI con MCP Server. Lenguaje en español.
   Storytelling with Data (Cole Nussbaumer).

6. CRITERIOS: Claridad, utilidad, accionabilidad. 
   Pensar como negocio, no como técnico.
```

### Problemas resueltos durante el proceso

| Problema | Solución |
|---|---|
| Encoding 1252 → nombres truncados (GÃ³mez, PatiÃ±o) | Cambiar Encoding=65001 (UTF-8) en todas las tablas |
| 28 relaciones auto-detectadas incorrectas | Eliminar todas y crear 22 relaciones star schema desde cero |
| Relaciones 1:1 con OneDirection generaban error | Cambiar a BothDirections en bot_interactions, product_signals, subscriptions, partner_portfolios |
| Ambigüedad Calendario por rutas múltiples | Una sola relación activa (leads→Calendario), demás inactivas con USERELATIONSHIP |
| `_Medidas` con error "parámetro de columnas NULL" | Cambiar a `#table(type table [...], {})` en lugar de `Table.FromRows` |
| Tipos Int64 en campos MRR (decimales truncados) | Forzar tipo Double en el modelo via column_operations Update |
| KAMs sin datos en tabla de portafolio | Crear medidas con USERELATIONSHIP(partner_portfolios[assigned_kam_rep_id], reps[rep_id]) |

---

## Los 3 reportes del dashboard

### Reporte 1 — Resumen Ejecutivo
**Audiencia:** CEO y Directores  
**Pregunta central:** ¿Cómo va el negocio?

Visualizaciones:
- 5 tarjetas KPI: Leads recibidos, MRR Ganado, Win Rate, Ciclo de venta, Churn Rate
- Gráfico de barras: MRR ganado por mes (fecha de cierre)
- Gráfico de barras horizontales: Win Rate por segmento (SMB vs Contadores)
- Gráfico de barras horizontales: Revenue ganado vs perdido vs pipeline
- Gráfico de barras horizontales: MRR por país
- Gráfico de barras: Win Rate por mes
- Tabla: Resumen por país y segmento (con barras de datos condicionales)

### Reporte 2 — Sales Performance
**Audiencia:** VP Sales y líderes comerciales  
**Pregunta central:** ¿Dónde está el problema?

Visualizaciones:
- Gráfico de barras: Funnel de conversión (7 etapas con % de paso)
- Gráfico de barras horizontales: Win Rate por AE vs línea de promedio
- Gráfico combo: Llamadas + tasa de conexión por SDR
- Tarjetas: Impacto del bot de IA (5 KPIs)
- Tarjetas: Contactabilidad general (3 KPIs)
- Gráfico de barras horizontales: Razones de pérdida (5 causas)

### Reporte 3 — RevOps & Eficiencia
**Audiencia:** RevOps y operaciones  
**Pregunta central:** ¿Cómo optimizamos?

Visualizaciones:
- Tarjetas: Días promedio por etapa del funnel (6 etapas)
- Gráfico de barras horizontales: Win Rate por canal de adquisición
- Gráfico de barras horizontales: Distribución del tiempo (productivo vs admin vs resto)
- Gráfico de barras apiladas: Distribución del tiempo por rep
- Gráfico de barras: Experimento descuento marzo 2026
- Gráfico de barras: Experimento onboarding (3 variantes)
- Tabla: Portafolio de partners por KAM

---

## Insights principales y accionables

### Para el CEO

| Insight | Dato | Acción |
|---|---|---|
| Solo capturamos el 10.6% del pipeline | $6.75M ganado de $63.9M disponible | Evaluar capacidad del equipo para absorber más volumen |
| Contadores es el motor real | 27% de leads → 76.6% del MRR, WR 56.4% | Doblar inversión en Partner Program y KAMs |
| SMB Colombia tiene churn crítico | 19% churn en COL SMB vs 10.9% promedio | Investigar causas: ¿precio, producto o expectativas? |

### Para VP Sales

| Insight | Dato | Acción |
|---|---|---|
| Cuello de botella en Contacted→Qualified | Solo 58% de los contactados califican | Revisar criterios de calificación e ICP |
| 97 deals perdidos por "sin respuesta" | 25% de todos los deals perdidos | Implementar secuencia automática post-propuesta |
| Llamadas agendadas son 28pp más efectivas | 85.5% vs 57.6% en frío | Estandarizar agendamiento previo como proceso |
| Juan Pablo Mejía: 18pp bajo el promedio | WR 25.8% vs 43.7% promedio | Coaching focalizado |

### Para RevOps

| Insight | Dato | Acción |
|---|---|---|
| La propuesta se "enfría" en 6.6 días | Etapa más lenta del funnel | SLA de 3 días para cierre post-propuesta |
| Solo 39.7% del tiempo es venta real | 21.7% es administración pura | Automatizar tareas admin = +4 reps equivalentes |
| Daniela Rivas gestiona el 45% del MRR de partners | $2.73M de $6.06M | Plan de distribución gradual del portafolio |
| Descuentos de marzo no cerraron más deals | WR bajó -1.5pp con descuento | Eliminar descuento como táctica de cierre |
| AI Setup Assistant es el mejor onboarding | 48.6% WR vs 45.1% Control | Rollout completo como estándar de onboarding |

---

## Supuestos clave

1. **Periodo de análisis.** Los leads van de Enero a Junio 2026. Los deals y suscripciones se extienden hasta Julio/Agosto. El Calendario cubre hasta el 31 de Agosto 2026 para incluir todos los datos.

2. **Tipos de datos.** Los campos MRR en los CSV no tienen decimales visibles porque la fuente los truncó como enteros. Se corrigió el tipo a `Double` directamente en el modelo de Power BI para garantizar precisión.

3. **Festivos.** La tabla Calendario incluye los 14 festivos de Colombia para 2026. No se incluyeron festivos de otros países (MEX, DOM, PER, CRI) porque no impactan los KPIs actuales del dashboard.

4. **Año fiscal = año calendario.** Se asumió que el año fiscal de HelixCore empieza en enero. Las métricas YTD usan este supuesto.

5. **Pipeline abierto.** El `MRR Pipeline Abierto` se calculó como la suma del `expected_mrr_usd` de todos los leads que NO tienen un deal cerrado asociado. Es una estimación del potencial sin trabajar, no un valor confirmado.

6. **Quota de KAMs.** Las cuotas de KAM están en USD (90,000 y 70,000) mientras que las de AE y SDR están en número de deals. Son unidades diferentes y no se compararon directamente.

7. **Churn date nula = activo.** Las suscripciones sin `churn_date` se consideran activas a la fecha de corte (5 de agosto 2026).

8. **Experimento de descuento.** El flag `discount_experiment_march_flag = 1` no tiene descripción adicional en el dataset. Se interpretó como deals que participaron en el experimento de descuento de marzo 2026.

---

## Segunda iteración — Roadmap

Con el modelo y los 150 medidas construidos, la segunda iteración se enfoca en **profundidad analítica** sobre preguntas específicas que los datos permiten responder pero que no se abordaron en esta entrega.

### Análisis pendientes (datos disponibles)

| Pregunta | Dataset necesario | Complejidad |
|---|---|---|
| ¿A qué hora del día tienen mayor tasa de conexión los SDRs? | `call_logs[call_local_hour]`, `call_logs[connected_flag]` | Baja |
| ¿El delay de handoff del bot afecta el win rate? | `bot_interactions[handoff_to_rep_delay_minutes]`, `deals` | Media |
| ¿Qué perfil de lead (buyer_role, company_size) tiene mayor win rate? | `leads[buyer_role]`, `leads[company_size_band]`, `deals` | Media |
| ¿Los leads con PQL alto (≥70) cierran más rápido? | `product_signals[pql_score]`, `deals[deal_cycle_days]` | Media |
| ¿Los deals con term_months = 12 tienen menos churn? | `deals[term_months]`, `subscriptions[current_status]` | Media |
| Análisis de cohortes de retención (mes a mes) | `subscriptions[start_date]`, `subscriptions[churn_date]` | Alta |
| ¿Qué combinación de actividades (calls + meetings + emails) predice cierre? | `sales_activities`, `deals` | Alta |

### Mejoras técnicas del modelo

- **Seguridad a nivel de fila (RLS):** Configurar roles para que cada rep vea solo sus datos
- **Tablas de parámetros dinámicos:** Para comparar métricas entre sí en un solo visual (What-if)
- **Integración con fuente live:** Conectar a Snowflake o Salesforce en lugar de CSVs estáticos
- **Alertas automáticas:** Power BI con alertas cuando el Win Rate cae por debajo del umbral
- **Reporte móvil:** Versión optimizada para celular con KPIs principales

---

## Inventario completo del modelo

### Medidas DAX — 150 medidas en 13 carpetas

#### 01 Funnel (12 medidas)
| Medida | Descripción |
|---|---|
| Total Leads | Total de leads creados en el periodo |
| Leads con Deal | Leads que llegaron a tener un deal |
| Tasa Lead a Deal | Tasa de conversión de leads a deal |
| Total Deals | Total de deals cerrados (ganados + perdidos) |
| Deals Won | Deals cerrados como ganados |
| Deals Lost | Deals cerrados como perdidos |
| Win Rate | Porcentaje de deals ganados sobre total cerrados |
| Leads SQL | Leads que llegaron a etapa SQL |
| Leads Demo | Leads que completaron demo |
| Leads Propuesta | Leads que llegaron a propuesta |
| Conv SQL a Won | Conversión de SQL a Deal ganado |
| Conv Demo a Won | Conversión de Demo a Deal ganado |

#### 02 Revenue (11 medidas)
| Medida | Descripción |
|---|---|
| MRR Ganado | MRR total de deals ganados |
| MRR Perdido | MRR potencial perdido en deals no cerrados |
| MRR Promedio Won | MRR promedio por deal ganado |
| MRR Lista Won | Precio de lista total en deals ganados |
| Revenue Descuentos | Revenue dejado por descuentos en deals ganados |
| Descuento Promedio Won | % promedio de descuento en deals ganados |
| MRR Expansion 90d | MRR de expansión en primeros 90 días |
| MRR Partners | MRR total bajo gestión de partners |
| MRR Pipeline Esperado | MRR esperado de leads sin deal |
| Ciclo Venta Dias | Ciclo de venta promedio (todos los deals) |
| Ciclo Venta Won Dias | Ciclo de venta promedio (solo ganados) |

#### 03 Contactabilidad (11 medidas)
| Medida | Descripción |
|---|---|
| Total Llamadas | Total de llamadas realizadas |
| Llamadas Conectadas | Llamadas donde el contacto contestó |
| Tasa Conexion | % llamadas que logran conexión |
| Llamadas No Conectadas | Llamadas sin conexión (Busy, No Answer, etc.) |
| Duracion Promedio Llamada (seg) | Duración promedio de llamadas conectadas |
| Llamadas Agendadas | Llamadas programadas previamente |
| Tasa Conexion Agendadas | Tasa de conexión en llamadas agendadas |
| Total Actividades | Total de actividades de ventas |
| Actividades Humanas | Actividades realizadas por reps humanos |
| Actividades Bot | Actividades de bot o sistema automatizado |
| Actividades por Lead | Promedio de actividades por lead |

#### 04 Bot IA (10 medidas)
| Medida | Descripción |
|---|---|
| Leads con Bot | Leads con contacto con el bot |
| Cobertura Bot | % de leads que tuvieron contacto con bot |
| Bot Replied | Leads que respondieron al bot |
| Tasa Respuesta Bot | % que respondió al bot |
| Meetings por Bot | Meetings agendados por el bot |
| Tasa Meeting Bot | % de leads con bot que terminaron en meeting |
| Delay Handoff Bot (min) | Tiempo promedio bot→rep humano |
| Win Rate con Bot | Win Rate en deals con bot |
| Win Rate sin Bot | Win Rate en deals sin bot |
| Lift Win Rate Bot | Diferencia de Win Rate (con bot - sin bot) |

#### 05 Retención (8 medidas)
| Medida | Descripción |
|---|---|
| Suscripciones Activas | Total de suscripciones activas |
| Suscripciones Churned | Suscripciones canceladas |
| Churn Rate | % de suscripciones que cancelaron |
| Activas 30d | Activas en últimos 30 días |
| Activas 90d | Activas en últimos 90 días |
| Con Expansion MRR | Suscripciones con expansión en 90 días |
| Tasa Expansion | % que generó expansión en 90 días |
| Tasa Pago Anual | % de suscripciones con pago anual (12 meses) |

#### 06 Reps Performance (10 medidas)
| Medida | Descripción |
|---|---|
| Deals Won por Rep | Deals ganados por rep (USERELATIONSHIP) |
| MRR por Rep | MRR ganado por rep |
| Win Rate por Rep | Win Rate del rep específico |
| Llamadas por Rep | Total llamadas del rep (USERELATIONSHIP) |
| Tasa Conexion por Rep | Tasa de conexión del rep |
| Actividades por Rep | Total actividades del rep (USERELATIONSHIP) |
| Minutos Productivos Avg | Minutos productivos diarios promedio |
| Tasa Utilizacion Rep | Tiempo productivo / tiempo logueado |
| After Hours Avg (min) | Minutos fuera de horario laboral promedio |
| Admin Time Avg (min) | Minutos en admin promedio por día |

#### 07 Product Signals (10 medidas)
| Medida | Descripción |
|---|---|
| PQL Score Avg | PQL Score promedio (0-100) |
| PQL Alto (>=70) | Leads con PQL Score ≥ 70 |
| Tasa PQL Alto | % de leads con PQL alto |
| Onboarding Completado | Leads que completaron onboarding |
| Tasa Onboarding | % de completación de onboarding |
| Win Rate con Onboarding | Win Rate con onboarding completado |
| Facturas 14d Avg | Facturas promedio en primeros 14 días |
| Win Rate Guided Onboarding | WR variante Guided Onboarding |
| Win Rate Control | WR grupo Control (onboarding estándar) |
| Win Rate AI Setup | WR variante AI Setup Assistant |

#### 08 Partners KAM (10 medidas)
| Medida | Descripción |
|---|---|
| Total Partners | Partners activos en el modelo |
| Clientes Gestionados Partners | Total empresas cliente de todos los partners |
| Clientes Avg por Partner | Promedio de clientes por partner |
| Soporte Avg por Partner | Promedio de solicitudes de soporte/mes |
| Partners Alto Valor | Partners con MRR > $500 USD |
| MRR Avg por Partner | MRR promedio por partner |
| Partners por KAM | Partners del KAM (USERELATIONSHIP) |
| Clientes por KAM | Clientes del KAM (USERELATIONSHIP) |
| MRR Gestion por KAM | MRR bajo gestión del KAM (USERELATIONSHIP) |
| Soporte Avg por KAM | Soporte promedio del KAM (USERELATIONSHIP) |

#### 09 Tendencia Tiempo (7 medidas)
| Medida | Descripción |
|---|---|
| Leads MTD | Leads del mes en curso (Month-to-Date) |
| Deals Won MTD | Deals ganados MTD |
| MRR Ganado MTD | MRR ganado MTD |
| Deals Won by Close Date | Deals ganados por fecha de cierre |
| MRR by Close Date | MRR por fecha de cierre |
| Actividades by Date | Actividades por fecha de actividad |
| Llamadas by Date | Llamadas por fecha de llamada |

#### 10 Inteligencia de Tiempo (12 medidas)
| Medida | Descripción |
|---|---|
| Leads Mes Anterior | Leads del mes anterior (para MoM) |
| Leads MoM % | Variación % leads mes sobre mes |
| MRR Mes Anterior | MRR del mes anterior |
| MRR MoM % | Variación % MRR mes sobre mes |
| Won Mes Anterior | Deals ganados del mes anterior |
| Won MoM % | Variación % deals ganados MoM |
| MRR YTD | MRR acumulado año (Year-to-Date) |
| Won YTD | Deals ganados acumulados YTD |
| Win Rate by Close Date | Win Rate por fecha de cierre |
| Churn Mensual | Churn del periodo por start_date |
| Nuevas Suscripciones | Nuevas suscripciones del periodo |
| Leads YTD | Leads acumulados YTD |

#### 11 Ejecutivo (10 medidas)
| Medida | Descripción |
|---|---|
| MRR Pipeline Abierto | MRR de leads sin deal cerrado |
| Eficiencia Revenue | MRR ganado / (ganado + perdido) |
| Won SMB | Deals Won del segmento SMB |
| Won AccountantFirm | Deals Won del segmento Contadores |
| Win Rate SMB | Win Rate del segmento SMB |
| Win Rate AccountantFirm | Win Rate del segmento Contadores |
| MRR SMB | MRR ganado segmento SMB |
| MRR AccountantFirm | MRR ganado segmento Contadores |
| Paises Activos | Países con leads en el periodo |
| MRR por Dia | Velocidad de revenue por día |

#### 12 Sales Performance (16 medidas)
| Medida | Descripción |
|---|---|
| Funnel New | Leads en etapa New |
| Funnel Contacted | Leads en etapa Contacted |
| Funnel Qualified | Leads en etapa Qualified |
| Conv New a Contacted | Tasa de conversión New → Contacted |
| Conv Contacted a Qualified | Tasa de conversión Contacted → Qualified |
| Conv Qualified a SQL | Tasa de conversión Qualified → SQL |
| Conv SQL a Demo | Tasa de conversión SQL → Demo |
| Conv Demo a Propuesta | Tasa de conversión Demo → Propuesta |
| Conv Propuesta a Won | Tasa de conversión Propuesta → Won |
| Cuota Cumplida % | Deals Won / Quota mensual del rep |
| Leads por SDR | Leads gestionados por SDR en etapa New |
| Lost: Too Expensive | Deals perdidos por precio alto |
| Lost: Competencia | Deals perdidos por competencia |
| Lost: Sin Respuesta | Deals perdidos sin respuesta post-propuesta |
| Lost: Sin Presupuesto | Deals perdidos por falta de presupuesto |
| Lost: Feature Faltante | Deals perdidos por funcionalidad faltante |

#### 13 RevOps Efficiency (18 medidas)
| Medida | Descripción |
|---|---|
| Dias en New | Días promedio en etapa New |
| Dias en Attempted | Días promedio en etapa Attempted |
| Dias en Qualified | Días promedio en etapa Qualified |
| Dias en SQL | Días promedio en etapa SQL |
| Dias en Demo | Días promedio en etapa Demo |
| Dias en Proposal | Días promedio en etapa Proposal |
| WR Exp Descuento | Win Rate con experimento descuento |
| WR Sin Exp Descuento | Win Rate sin experimento descuento |
| Lift Exp Descuento | Diferencia de Win Rate (lift del descuento) |
| MRR Avg Exp Descuento | MRR promedio en experimento de descuento |
| Leads Partner Program | Leads del canal Partner Program |
| WR Partner Program | Win Rate del canal Partner Program |
| WR Webinar | Win Rate del canal Webinar |
| WR Paid Search | Win Rate del canal Paid Search |
| Actividades por Deal Won | Actividades humanas por deal ganado |
| Admin Time % | % del tiempo en tareas admin |
| Tiempo Productivo % | % del tiempo en venta real |
| After Hours Total (hrs) | Horas fuera de horario del equipo |

---

## Glosario — Términos, abreviaturas y medidas DAX

### Roles del equipo comercial

| Término | Significado | En HelixCore |
|---|---|---|
| **SDR** | Sales Development Representative | Hace primer contacto, llama, agenda reuniones. No cierra ventas. |
| **AE** | Account Executive (Ejecutivo de cuenta) | Cierra la venta. Hace demos, negocia, firma. |
| **KAM** | Key Account Manager (Gerente de cuentas clave) | Gestiona relación con firmas contables aliadas (partners). |
| **Inbound** | SDR que atiende leads que llegaron solos | Sofía Ríos, Mateo Silva |
| **Outbound** | SDR que sale a buscar prospectos | Ana Torres, Diego Valdez |

### Segmentos

| Término | Significado |
|---|---|
| **SMB** | Small & Medium Business — Empresas que compran para su propio uso |
| **AccountantFirm** | Firmas contables que usan HelixCore para gestionar clientes |
| **Contadores** | Nombre en español de AccountantFirm |

### Etapas del funnel

| Etapa | Descripción |
|---|---|
| **New** | Lead recibido, aún no contactado |
| **Attempted** | Se intentó contactar, aún no hay respuesta |
| **Working** | El bot o sistema está procesando el contacto |
| **Contacted** | Primer contacto exitoso |
| **Qualified** | El SDR confirmó que tiene perfil para comprar |
| **SQL** | Sales Qualified Lead — Pasó al AE para trabajar |
| **Demo Completed** | El AE hizo la demostración del producto |
| **Proposal** | Se envió la propuesta económica |
| **Closed Won** | Deal ganado, cliente firmó |
| **Closed Lost** | Deal perdido |

### Términos de métricas

| Término | Siglas | Descripción |
|---|---|---|
| **Win Rate** | WR | % de deals ganados sobre total cerrados |
| **Monthly Recurring Revenue** | MRR | Ingreso mensual recurrente por suscripciones |
| **Churn Rate** | — | % de clientes que cancelaron |
| **Pipeline** | — | Total de oportunidades de revenue disponibles |
| **Ciclo de venta** | — | Días desde creación del deal hasta cierre |
| **Lift** | — | Diferencia de rendimiento entre dos grupos |
| **Delay Handoff** | — | Tiempo bot → rep humano (minutos) |
| **Tasa de conexión** | TC | % de llamadas donde el prospecto contesta |
| **Product Qualified Lead Score** | PQL | Puntaje 0-100 de engagement con el producto |
| **Month-over-Month** | MoM | Variación porcentual vs mes anterior |
| **Year-to-Date** | YTD | Acumulado desde inicio del año |
| **Month-to-Date** | MTD | Acumulado desde inicio del mes |
| **Puntos porcentuales** | pp | Diferencia entre dos porcentajes (40%→43% = 3pp) |
| **Service Level Agreement** | SLA | Tiempo máximo acordado para completar una tarea |
| **Ideal Customer Profile** | ICP | Perfil del cliente con mayor probabilidad de comprar |

### Términos de tecnología

| Término | Descripción |
|---|---|
| **MCP Server** | Model Context Protocol — Protocolo que conecta Claude directamente con Power BI Desktop |
| **Star Schema** | Modelo de datos en estrella: tabla de dimensiones en el centro, hechos alrededor |
| **DAX** | Data Analysis Expressions — Lenguaje de fórmulas de Power BI |
| **Power Query (M)** | Lenguaje de transformación de datos en Power BI |
| **USERELATIONSHIP** | Función DAX para activar relaciones inactivas del modelo |
| **RutaOrigen** | Parámetro Power Query que define la ruta de los archivos fuente |
| **AI First** | Enfoque donde la IA es el copiloto principal del proceso de trabajo |
| **Rollout** | Despliegue o activación de una funcionalidad para todos los usuarios |

---

## Autor

**Fredys Caballero**  
Business Intelligence Analyst  
Experiencia en: Power BI, SQL, Python, DAX, Power Query, Google Apps Script  
Maestría en Ciencias de Datos con especialización en IA/ML — Universidad DaVinci (2026-2027)

---

*Documento generado como parte del Reto Técnico de Alegra — Abril 2026*  
*Construido con Claude AI (Sonnet 4.6) + Power BI MCP Server*
