# **Honduras Basic Food Basket Analytics**


## **Descripción y Objetivos**

Se realizó la construcción de un pipeline de datos end to end, utilizando como fuente de información el monitoreo de los precios de la canasta básica en Honduras, la fuente de información es recolectada de la página web gubernamental de la SDE (_Secretaria del Desarrollo Económico_), quienes hacen la medición de los precios en los mercados de las 2 ciudades más importantes del país, se aplicó uso de la IA para procesar los documentos publicados mes a mes para ser consumidos a través de una API, aplicando el formato de Arquitectura Medallion para su procesamiento.

### **Objetivos**

- Implementar un sistema automatizado de control mediante el Dashboard de Variación Semanal para rastrear los precios de los productos mas importantes que componen la canasta básica en Honduras, logrando identificar y alertar sobre variaciones de precios atípicas (superiores un porcentaje semanal) en los principales mercados del país a lo largo del año 2025-2026.
- Establecer un modelo predictivo y de análisis histórico para evaluar el comportamiento inflacionario y estacional de la canasta básica en Honduras, logrando automatizar reportes de tendencias a mediano plazo conectados a la arquitectura Medallion de Databricks durante el periodo 2025-2026.

## **Arquitectura**

Para garantizar datos confiables, escalables y limpios, se construyó el pipeline bajo una arquitectura moderna basada en capas (Medallion Architecture).

<img width="2152" height="487" alt="Arch-Medallion" src="https://github.com/user-attachments/assets/80062b78-346f-427d-ac5f-a1716493ba85" />


- **Capa Landing:** Es la primera capa en la cual se alojan los registros en DataBricks, se reciben de la API desde DeepSeek, por cada uno de los archivos PDF provenientes de la fuente.
- **Capa Bronze:** Se recopilan los datos en formato STRING, son provenientes de la capa Landing, en el cual se aplica el EDA correspondiente para conocer la calidad de los datos y aplicar los tratamientos correspondientes para mantener la integridad, limpieza y trazabilidad de la información.
- **Capa Silver:** Donde se guardan la información que ha sido tratada con los procesos y estándares de limpieza, transformación basada en el tipo de dato.
- **Capa Gold:** Creación del modelo dimensional utilizado, los datos fueron construidos bajo el modelo Star Schema, en el cual se cuenta con 3 Tablas dimensionales (Tiempo, Producto y Mercados) en cual la información se ha simplificado y armado las relaciones con la tabla de Hechos para almacenar el histórico de los precios de los productos
- **Capa Semántica:**: Esta capa se crearon las vistas para simplificar las consultas de los datos, para tener disponibles a los usuarios interesados en las posibles áreas de negocio, así como también otras áreas para su tratamiento y análisis.

Se manejó el conteo de datos entre cada capa dando como resultado:
Capa	    
- 1. Bronze: ==> 6571
- 2. Silver: ==> 5733
- 3. Gold: ==> 5578


## **Tecnologías Utilizadas**
* **Motor de Procesamiento:** Databricks / Apache Spark
* **Lenguaje:** SQL (Spark SQL), Python
* **Almacenamiento:** Delta Lake & Databricks
* **Orquestación:** Databricks Workflows (Schedule quincenal)
* **Control de Versiones:** Git / GitHub
* **Generación de Reportes:** PowerBi conexión a DataBricks
* **Otras Tecnologías:** DeepSeek

## **Modelado de Datos**

Se aplicó en la Capa Gold, el diseño de **Star Schema**, el cual permite mantener la granularidad de los datos, optimizado para las consultas y las relaciones con su tabla de hechos.
**Tablas Dimensionales: **
Dim_Time: Utilizada bajo SCD_0, por su nulo cambio de los datos, el cual almacena los periodos de tiempo del análisis de los precios 
Dim_Product: Utilizada para almacenar el listado de los productos de la canasta, utilizando el SCD_1
Dim_Market: Almacena el nombre de los mercados en los que se aplica el análisis de los precios de los productos
Fact_Products:Tabla de hechos, diseñada con la granularidad de mantener la estructura de entidad/relación con las tablas DIM, bajo la cardinalidad de sus llaves foráneas (FK) hacia las llaves principales (PK) de las tablas dimensión

## **Dashboard Análitico**

Se realizó la elaboración del dashboard, en PowerBI, estableciendo una conexión con DataBricks a traves de la configuración necesaria y realizar el consumo de las vistas creadas de la capa Semántica.

Se manejan 4 Reportes con sus correspondientes KPI, los cuales permite
- Comparar el costo de vida y los precios de los productos entre las diferentes ciudades y departamentos de la semana analizada.
- Permitir monitorear la variación porcentual de los precios semana a semana a nivel de producto, ciudad y departamento, detectando de inmediato qué productos presentan anomalías o alzas críticas
- Analizar el comportamiento temporal de los precios históricos a lo largo de las semanas y los años utilizando el eje cronológico de la fluctuación de los precios en las distintas semanas en cada año.
- Evaluar los ciclos macroeconómicos a nivel trimestral 

## **Fuente de los Datos**

Se tomó los boletines en formato PDF, que son publicados por la SDE(Secretaría de Desarrollo Económico) de Honduras a través de su portal [https://sde.gob.hn/proteccion-al-consumidor/boletines/](url). 

En el cual realizan las publicaciones aproximadamente cada 15 días o mensual con los datos de las mediciones de los 30 productos de la canasta básica en Honduras. En el cual analizan un estimado de 8 mercados, entre los 2 departamentos mas grandes de Honduras.

## **Estructura del Repositorio**
El repositorio alojado y versionado en GitHub, cada una de estas carpetas contienen 1 archivo notebook y por capa de cada objeto creado o ejecutado.

- DDL: Para la elaboración de los objetos tales como tablas Delta, vistas, esquemas.
- EDA: Contiene el análisis correspondiente de la exploración de los datos
- ETL: Para la extracción de los datos, Transformación y Carga de dichos registros para la siguiente capa.
- Dashboard: Se maneja el guardado del archivo de PowerBI como respaldo en nube
- Jobs: Versionamiento para los Jobs que se ejecutan, así como las parametrizaciones correspondientes.
- PDF_Files: Se almacena los PDF fuente de la información mensual de los precios, dicha carpeta es leída por el JOB para pasar los datos a DeepSeek para su retorno como un formato válido.



