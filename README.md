Economic Intelligence & Financial Analytics
Desarrollo de un pipeline ETL con Python, PostgreSQL y Power BI
- Versión: 1.0
- Tipo de proyecto: Business Intelligence / Data Analytics / ETL
- Cliente: Economic Insights Consulting
- Responsable de datos: Data Analyst
- Fuente principal: Federal Reserve Economic Data (FRED)
- Tecnologías: Python, Pandas, SQL, PostgreSQL, SQLAlchemy, Power
  BI y Excel
1. Contexto empresarial
Economic Insights Consulting es una firma de consultoría que presta
servicios de análisis económico y financiero a empresas que desean
comprender el comportamiento de la economía y anticiparse a posibles
cambios en el mercado.

Actualmente, la compañía consulta indicadores económicos manualmente
desde diferentes fuentes. El proceso presenta los siguientes
inconvenientes:
- Descarga manual de datos.
- Información distribuida en diferentes archivos.
- Ausencia de una base de datos centralizada.
- Falta de un proceso estandarizado de limpieza y transformación.
- Actualización manual de reportes.
- Falta de historial de las actualizaciones.
- Demasiado tiempo dedicado a preparar información y poco a
  interpretarla.
La solución propuesta busca automatizar este proceso mediante un
pipeline ETL. La ejecución será manual mediante un script de Python; no
se requiere una actualización automática en segundo plano.
2. Objetivo general
Desarrollar una solución de integración y análisis de datos que obtenga
indicadores económicos desde FRED, los transforme mediante Python, los
almacene en PostgreSQL y los visualice mediante dashboards interactivos
en Power BI.

La solución debe garantizar:
- Integridad y calidad de los datos.
- Actualización incremental.
- Trazabilidad de las ejecuciones.
- Prevención de registros duplicados.
- Disponibilidad de información histórica.
- Facilidad para realizar análisis económicos.
3. Objetivos específicos
1. Consumir información económica desde la API de FRED.
2. Descargar datos históricos durante la primera ejecución.
3. Implementar un proceso ETL con Python.
4. Limpiar y estandarizar los datos.
5. Validar la calidad de la información.
6. Diseñar una base de datos relacional en PostgreSQL.
7. Implementar cargas incrementales mediante INSERT y UPSERT.
8. Registrar cada ejecución del proceso ETL.
9. Crear consultas SQL para responder preguntas de negocio.
10. Construir dashboards en Power BI.
11. Crear un reporte complementario en Excel.
4. Fuente de información e indicadores
La fuente principal es Federal Reserve Economic Data (FRED).
- Sitio web: https://fred.stlouisfed.org/
- Documentación de la API: https://fred.stlouisfed.org/docs/api/fred/
FRED proporciona series históricas de indicadores económicos y permite
consultar observaciones mediante una API. Para utilizarla, se debe
obtener una API Key.
Indicadores económicos requeridos
  Indicador                                   Código FRED   Frecuencia habitual
  Tasa de desempleo                           UNRATE      Mensual
Índice de precios al consumidor (IPC)       CPIAUCSL    Mensual
Tasa de fondos federales                    FEDFUNDS    Mensual
Rendimiento del bono del Tesoro a 10 años   DGS10       Diaria
Producto interno bruto (PIB)                GDP         Trimestral

Las frecuencias corresponden a la periodicidad habitual de publicación.
Los datos pueden publicarse con retraso y estar sujetos a revisiones.

En cada ejecución, el ETL debe consultar FRED e identificar
observaciones nuevas, modificadas, existentes y datos que todavía no
hayan sido publicados. No debe asumir que todos los indicadores tendrán
datos nuevos en cada ejecución.
5. Requerimientos funcionales del ETL
5.1. Extracción de datos
El sistema deberá:
- Conectarse a la API de FRED.
- Consultar las cinco series económicas seleccionadas.
- Obtener información histórica en la primera ejecución.
- Consultar observaciones recientes en ejecuciones posteriores.
- Manejar errores de conexión.
- Registrar la fecha y hora de extracción.
5.2. Transformación de datos
El sistema deberá:
- Estandarizar nombres de columnas.
- Convertir fechas a formato datetime.
- Convertir valores numéricos.
- Identificar valores nulos y observaciones no numéricas.
- Eliminar duplicados.
- Estandarizar los identificadores de series.
- Incorporar metadatos de cada indicador.
- Calcular variaciones porcentuales cuando corresponda.
Se deberá conservar el valor original obtenido de FRED. Los valores
faltantes no deberán convertirse automáticamente en cero.
5.3. Validación de calidad
El proceso deberá validar:
- Que las fechas sean válidas.
- Que los identificadores de indicadores existan.
- Que los registros no estén duplicados.
- Que los valores sean numéricos o estén identificados como faltantes.
- Que los datos correspondan a una serie válida.
- Que los registros cargados coincidan con los registros aceptados.
Los registros rechazados deberán quedar documentados.
5.4. Carga a PostgreSQL
El sistema deberá:
- Crear las tablas necesarias.
- Cargar los datos históricos inicialmente.
- Insertar observaciones nuevas.
- Actualizar observaciones modificadas.
- Evitar duplicados.
- Mantener el histórico.
- Registrar los resultados de cada ejecución.
La combinación de identificador de serie y fecha de observación deberá
ser la clave única de las observaciones.
5.5. Registro de ejecuciones
Cada ejecución deberá registrar:
- Identificador de ejecución.
- Fecha y hora de inicio y finalización.
- Cantidad de registros extraídos.
- Cantidad de registros transformados.
- Cantidad de registros nuevos.
- Cantidad de registros actualizados.
- Cantidad de registros rechazados.
- Estado de la ejecución.
- Descripción del error, si existe.
6. Diseño propuesto de la base de datos
Nombre de la base de datos: ECONOMIC_INTELLIGENCE_DB

Se propone utilizar PostgreSQL con un modelo analítico dimensional.
6.1. DIM_SERIES
Almacena la información descriptiva de cada indicador.

Campos sugeridos:
- series_id
- series_name
- description
- frequency
- units
- source
- active_status
6.2. FACT_ECONOMIC_OBSERVATIONS
Almacena las observaciones económicas.

Campos sugeridos:
- observation_id
- series_id
- observation_date
- observation_value
- previous_value
- percentage_change
- extraction_timestamp
- last_updated_timestamp
Clave única: series_id + observation_date.
6.3. ETL_RUN_LOG
Almacena el historial de ejecuciones.

Campos sugeridos:
- run_id
- start_time
- end_time
- records_extracted
- records_inserted
- records_updated
- records_rejected
- execution_status
- error_message
6.4. ETL_ERROR_LOG
Almacena los errores detectados durante el proceso.

Campos sugeridos:
- error_id
- run_id
- series_id
- error_type
- error_description
- error_timestamp
El modelo podrá ampliarse con una dimensión calendario si resulta
conveniente para el análisis.
7. Actualización incremental
Primera ejecución
1. Consultar el histórico disponible de cada indicador.
2. Descargar las observaciones.
3. Transformar y validar la información.
4. Cargar los registros en PostgreSQL.
5. Registrar la ejecución.
Segunda ejecución y posteriores
1. Consultar la última fecha registrada por indicador.
2. Consultar nuevamente la fuente desde una ventana de fechas anterior
   a la última observación.
3. Obtener registros nuevos y posibles revisiones.
4. Comparar la información con PostgreSQL.
5. Insertar registros nuevos.
6. Actualizar registros modificados.
7. Evitar duplicados.
8. Registrar el resultado de la ejecución.
Se recomienda volver a consultar una ventana de observaciones recientes
en lugar de consultar únicamente fechas posteriores a la última
observación. Esto permite detectar revisiones de datos publicados
anteriormente.

La carga deberá ser idempotente: ejecutar dos veces el proceso sin
cambios en la fuente no deberá generar registros duplicados ni
resultados inconsistentes.
8. Preguntas de negocio
8.1. Análisis de inflación
1. ¿Cómo ha evolucionado la inflación a través del tiempo?
2. ¿En qué períodos se presentaron los mayores incrementos del IPC?
3. ¿Cuál es la variación porcentual interanual del IPC?
4. ¿La inflación está aumentando o disminuyendo?
5. ¿Cómo ha cambiado la tendencia durante los últimos cinco años?
8.2. Análisis del desempleo
1. ¿Cómo ha evolucionado la tasa de desempleo?
2. ¿Cuáles fueron los períodos con mayor desempleo?
3. ¿Qué relación temporal se observa entre inflación y desempleo?
4. ¿Cómo ha cambiado el mercado laboral en los últimos años?
5. ¿Se observan períodos de recuperación económica?
8.3. Análisis de tasas de interés
1. ¿Cómo ha evolucionado la tasa de fondos federales?
2. ¿En qué períodos se registraron los mayores incrementos?
3. ¿Cómo se relacionan los cambios en las tasas de interés con la
   inflación?
4. ¿Cómo ha evolucionado el rendimiento del bono del Tesoro a 10 años?
5. ¿Qué diferencias existen entre ambas tasas?
8.4. Análisis del crecimiento económico
1. ¿Cómo ha evolucionado el PIB de Estados Unidos?
2. ¿Qué períodos presentan crecimiento o contracción?
3. ¿Cuál es la variación porcentual trimestral?
4. ¿Cuál es el crecimiento interanual?
5. ¿Qué relación temporal existe entre crecimiento económico, desempleo
   e inflación?
8.5. Análisis macroeconómico integrado
1. ¿Cómo se comportan conjuntamente inflación, desempleo, tasas de
   interés y PIB?
2. ¿Qué períodos presentan señales de desaceleración económica?
3. ¿Qué cambios importantes se observan en los últimos cinco años?
4. ¿Qué indicadores presentan cambios más significativos?
5. ¿Qué información debería monitorear periódicamente la dirección?
Estas preguntas deberán responderse mediante consultas SQL, análisis con
Python y visualizaciones en Power BI. Las relaciones observadas deberán
interpretarse como asociaciones y tendencias, no como pruebas
automáticas de causalidad.
9. Dashboard de Power BI
El dashboard ejecutivo deberá dividirse en cuatro páginas.
Página 1. Executive Economic Overview
Indicadores principales:
- Último valor disponible del PIB.
- Última tasa de desempleo.
- Último IPC disponible.
- Última tasa de fondos federales.
- Rendimiento del bono a 10 años.
- Fecha de última observación por indicador.
Visualizaciones:
- Evolución histórica de los indicadores.
- Variación respecto al período anterior.
- Resumen de tendencias.
- Filtros por período.
Página 2. Inflation Analysis
Indicadores:
- IPC.
- Variación mensual.
- Variación interanual.
- Tendencia de inflación.
Visualizaciones:
- Evolución histórica del IPC.
- Variación porcentual interanual.
- Comparación por períodos.
- Identificación de máximos y mínimos.
Página 3. Labor Market & Interest Rates
Indicadores:
- Tasa de desempleo.
- Tasa de fondos federales.
- Rendimiento del bono a 10 años.
Visualizaciones:
- Evolución del desempleo.
- Evolución de tasas de interés.
- Comparación histórica.
- Análisis de cambios relevantes.
Página 4. Economic Growth
Indicadores:
- PIB.
- Variación trimestral.
- Variación interanual.
Visualizaciones:
- Evolución del PIB.
- Crecimiento económico.
- Períodos de contracción.
- Comparación con otros indicadores macroeconómicos.
El dashboard deberá mostrar la fecha de última actualización de los
datos y diferenciar las frecuencias de publicación.
10. Reporte complementario en Excel
El archivo de Excel deberá permitir:
- Revisar las últimas observaciones cargadas.
- Consultar variaciones de indicadores.
- Generar tablas dinámicas.
- Visualizar tendencias históricas.
- Revisar resultados del proceso ETL.
- Comparar los datos obtenidos desde la API con los almacenados en
  PostgreSQL.
El archivo deberá incluir al menos las siguientes hojas:
1. Economic Indicators
2. Monthly Summary
3. Data Quality Report
4. ETL Execution History
11. Requerimientos técnicos
  Componente                 Tecnología
  Extracción                 Python / Requests
Transformación             Pandas / NumPy
Conexión a base de datos   SQLAlchemy
Base de datos              PostgreSQL
Consultas                  SQL
Visualización              Power BI
Reportes                   Excel
Control de versiones       Git / GitHub

El proceso deberá poder ejecutarse manualmente desde la terminal:
python run_etl.py
Se deberá utilizar un archivo .env para almacenar la API Key y las
credenciales de PostgreSQL. Las credenciales no deberán publicarse en
GitHub.
12. Entregables esperados
1. Código fuente completo del ETL.
2. Script de extracción.
3. Script de transformación.
4. Script de validación.
5. Script de carga.
6. Script principal de ejecución.
7. Scripts SQL de creación de tablas.
8. Consultas SQL de análisis.
9. Base de datos PostgreSQL.
10. Dashboard de Power BI.
11. Reporte complementario en Excel.
12. Registro de ejecuciones.
13. Documentación técnica.
14. README de GitHub.
13. Criterios de aceptación
El proyecto se considerará exitoso cuando:
- El sistema obtenga datos directamente desde FRED.
- La primera ejecución realice la carga histórica.
- Las ejecuciones posteriores consulten datos recientes.
- Los registros nuevos se inserten correctamente.
- Las observaciones revisadas se actualicen.
- No se generen duplicados.
- Los errores queden registrados.
- Se conserve la información histórica.
- PostgreSQL contenga información consistente.
- Power BI pueda consultar la base de datos.
- El dashboard responda las preguntas de negocio.
- Se identifique la última actualización de cada indicador.
- El proyecto esté documentado en GitHub.
14. Restricciones y consideraciones
- Los datos de FRED no necesariamente se actualizan diariamente.
- Cada indicador tiene su propia frecuencia de publicación.
- Algunas observaciones históricas pueden ser revisadas.
- La disponibilidad de nuevos datos depende de la publicación de la
  fuente.
- No se deben inventar observaciones faltantes.
- No se deben interpretar correlaciones como causalidad.
- Las proyecciones económicas quedan fuera del alcance inicial.
- El proyecto tendrá ejecución manual, no programación automática.
- El dashboard mostrará datos hasta la última observación disponible,
  que no necesariamente corresponde a la fecha actual.
15. Resultado esperado
Economic Insights Consulting espera recibir una solución integral de
análisis de datos que reduzca el trabajo manual de recopilación y
preparación de información económica.

La solución deberá permitir que cualquier analista autorizado ejecute el
proceso ETL, actualice la base de datos y consulte los indicadores
económicos mediante Power BI.

El resultado final deberá demostrar el desarrollo de un proceso
completo, desde la adquisición de datos hasta la generación de
información útil para la toma de decisiones.




config.py	Leer .env (API key, DB URL, ventanas por frecuencia)
models/	Definición de tablas con SQLAlchemy ORM
schemas/	Validación y serialización con Pydantic
clients/	Solo HTTP: llamar a FRED y devolver JSON validado
repositories/	Solo SQL: INSERT/UPDATE/UPSERT/consultas
services/	Lógica: ventana, comparación, orquestación
main.py / scripts/	Punto de entrada (CLI, cron, scheduler)# An-lisis-de-indicadores-macroecon-micos-EEUU

