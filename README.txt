# Análisis Integral de E-Commerce y Rendimiento Logístico: Caso de Estudio Olist

## Resumen Ejecutivo
Este proyecto consiste en un análisis de datos integral (End-to-End) basado en el ecosistema de Olist, la principal tienda departamental de comercio electrónico en Brasil. El objetivo central de este estudio es transformar un conjunto de datos transaccionales en bruto en información procesable, permitiendo a los departamentos de Dirección Comercial y Operaciones/Logística tomar decisiones basadas en datos (Data-Driven) para optimizar sus estrategias de ventas y mejorar el nivel de servicio al cliente.

## Arquitectura y Stack Tecnológico
Para el desarrollo de este proyecto se ha implementado una arquitectura de análisis en dos fases, utilizando las siguientes tecnologías:
* **Procesamiento y ETL:** Python 3 (entorno Jupyter Notebook), empleando las librerías `pandas` y `os` para la manipulación masiva de datos y gestión de directorios.
* **Modelado y Visualización:** Microsoft Power BI.
* **Lenguaje Analítico:** DAX (Data Analysis Expressions) para la creación de medidas dinámicas y normalización geoespacial.

## Metodología y Pipeline de Datos

### 1. Extracción y Transformación (Pipeline ETL)
El proyecto comenzó con la ingesta de múltiples fuentes de datos relacionales (`orders`, `order_items`, `products`, `customers`). Se procedió a consolidar estas tablas mediante operaciones de cruce (Merge/Join) para construir un modelo tabular desnormalizado, optimizado para el análisis analítico.

Durante esta fase, se aplicó ingeniería de características (Feature Engineering) para generar métricas de negocio críticas:
* **Tiempo de Entrega Real (`delivery_days`):** Calculado a partir de la diferencia temporal entre la confirmación de compra y la recepción por parte del cliente.
* **Cumplimiento de SLA (`is_late`):** Variable booleana generada mediante la comparación de la fecha real de entrega frente a la fecha estimada prometida al cliente, fundamental para evaluar el Service Level Agreement.
* **Agrupación Temporal (`month_year`):** Extracción del periodo en formato de texto estandarizado para asegurar un análisis de series temporales robusto, evitando las agregaciones automáticas de las herramientas de BI.

### 2. Calidad de Datos (Data Quality) y Resolución de Anomalías
Se aplicaron estrictos criterios de calidad de datos para asegurar la fiabilidad del modelo final:
* **Tratamiento de Valores Atípicos (Outliers):** Se identificaron y excluyeron registros con errores de captura en los sistemas de origen, tales como tiempos de entrega negativos o plazos logísticamente imposibles (superiores a 100 días), los cuales distorsionaban severamente las medias aritméticas.
* **Estandarización de Tipos de Datos y Conflictos de Configuración Regional (Locale):** Se resolvió un problema crítico de interoperabilidad entre Python y Power Query. Se forzó la conversión de variables numéricas flotantes (ej. `7.0`) al formato nativo entero (`Int64`) mediante Pandas. Esta intervención previno que el motor de Power Query, influenciado por la configuración regional de Windows, interpretara erróneamente los separadores decimales como separadores de miles, garantizando la integridad numérica en el entorno de visualización.

### 3. Modelado Semántico y Enriquecimiento Geoespacial (DAX)
En el entorno de Power BI, se aplicaron transformaciones adicionales mediante lenguaje DAX para optimizar la representación visual:
* **Normalización Geoespacial:** Se detectó una ambigüedad nativa en el motor de renderizado de Bing Maps, el cual ubicaba erróneamente ciertas siglas estatales brasileñas (como "AP" o "PB") en territorios de Asia o Norteamérica. Para mitigarlo, se desarrolló una estructura de control condicional masiva (`SWITCH`) en DAX que tradujo dinámicamente cada sigla a su denominación geográfica completa (ej. "Amapá, Brazil"), asegurando un ploteo de coordenadas con un 100% de precisión dentro del territorio brasileño.

## Hallazgos Clave y Recomendaciones Estratégicas

A partir de la explotación del modelo de datos interactivo, se han consolidado los siguientes insights para la toma de decisiones directivas:

### Visión Comercial y Tendencias de Ingresos
1. **Alta Sensibilidad a la Estacionalidad Promocional:** El análisis longitudinal revela un comportamiento de ventas estándar a lo largo del año, interrumpido por un crecimiento exponencial durante el mes de noviembre de 2017. Este pico, directamente correlacionado con la campaña de Black Friday, indica que la base de clientes es altamente reactiva a las estrategias de precios y descuentos agresivos.
2. **Concentración Demográfica del Valor:** Existe una pronunciada disparidad en la aportación de ingresos por región. El estado de São Paulo (SP) actúa como el epicentro financiero y logístico del negocio, dominando el volumen de transacciones con un margen sustancial sobre el resto de las regiones, seguido a gran distancia por Río de Janeiro (RJ) y Minas Gerais (MG).

### Rendimiento de Operaciones y Logística
1. **Evaluación General del Nivel de Servicio:** A nivel nacional, la red logística de Olist mantiene un tiempo medio de entrega consolidado en torno a los 12 días, una métrica que debe servir como línea base (baseline) para futuras auditorías de eficiencia.
2. **Identificación de Cuellos de Botella Geográficos:** La distribución de los tiempos de entrega por estado expone una deficiencia estructural severa en la región Norte de Brasil. Estados como Roraima (RR), Amapá (AP) y Amazonas (AM) registran los peores promedios logísticos, superando de forma regular los 25 días de tránsito.
3. **Recomendación Estratégica:** Ante esta brecha de servicio, se recomienda a la Dirección de Operaciones realizar un estudio de viabilidad (Business Case) para la apertura de almacenes de tránsito (Cross-Docking) en el norte del país, o en su defecto, llevar a cabo una renegociación de los Acuerdos de Nivel de Servicio (SLAs) y rutas de distribución con los operadores logísticos (carriers) que cubren dicha zona, con el fin de proteger la retención y satisfacción del cliente en estas áreas.

