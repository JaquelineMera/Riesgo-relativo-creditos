# Laboratoria: Riesgo Relativo - Super Caja
Este proyecto aborda la creación de un score crediticio con el fin de gestionar el riesgo de incumplimiento en préstamos. 
![pikaso_caratula2](https://github.com/user-attachments/assets/bcd4c9ff-3f51-46ff-9467-08289f94ec6c)

# 📊Temas 
- [Introducción](#introducción)
- [Objetivo](#objetivo)
- [Equipo](#equipo)
- [Herramientas](#herramientas)
- [Lenguajes](#lenguajes)
- [Procesamiento y análisis](#procesamiento-y-análisis)
  - [Procesamiento de los datos](#procesamiento-de-los-datos)
  - [Análisis exploratorio](#análisis-exploratorio)
  - [Segmentación de variables](#segmentación-de-variables)
  - [Cálculo de riesgo relativo](#cálculo-de-riesgo-relativo)
  - [Validación de hipótesis](#validación-de-hipótesis)
  - [Creación de variables dummies](#creación-de-variables-dummies)
  - [Cálculo de score crediticio](#cálculo-de-score-crediticio)
  - [Clasificación de cliente](#clasificación-de-cliente)
  - [Validación de modelo](#validación-de-modelo)
  - [Enlaces de procesos](#enlaces-de-procesos)
    - [SQL](/SQL/)  
    - [Jupyter Notebook](/Jupyter_Notebook/README.md)
- [Resultados](#resultados)
- [Conclusiones](#conclusiones)
- [Recomendaciones](#recomendaciones)
- [Pasos a seguir](#pasos-a-seguir)
- [Enlaces](#enlaces)


## Introducción
El proyecto aborda el desafío que enfrenta la institución financiera "Super Caja" debido al aumento en las solicitudes de crédito, provocado por la disminución de las tasas de interés. El proceso manual de análisis de crédito resulta ineficiente y lento, afectando la capacidad de la institución para procesar solicitudes y gestionar el riesgo de incumplimiento. Con el fin de mejorar la eficiencia y precisión, se propone automatizar el análisis de crédito mediante técnicas avanzadas de análisis de datos, con el objetivo de crear un sistema de scoring crediticio que clasifique a los solicitantes según su riesgo, optimizando así las decisiones de otorgamiento de crédito y fortaleciendo la estabilidad financiera de "Super Caja".

## Objetivo
Mejorar la eficiencia y la precisión en la evaluación del riesgo crediticio, permitiendo al banco tomar decisiones informadas sobre la concesión de crédito y reducir el riesgo de préstamos no reembolsables. A través, de la creación de score crediticio de acuerdo al riesgo relativo, basado en la probabilidad de incumplimiento. 

## Equipo
- [Jaqueline Mera](https://github.com/JaquelineMera)

## Herramientas
+ BigQuery
+ Google Colab
+ Looker Studio

## Lenguajes
+ SQL
+ Python

## Procesamiento y análisis
El flujo de trabajo incluyó varias etapas, el procesamiento de los datos, análisis exploratorio, segmetación de variables, cálculo de riesgo relativo, validación de hipótesis, creación de variables dummies, cálculo de score crediticio, clasificación de cliente, validación de modelo.

## Procesamiento de los datos
+ **Conectar/importar datos a BigQuery.**
  + Se han importado los datos en tablas dentro del ambiente de BigQuery (user_info, loans_outstanding, loans_details y default).
  + Se conectaron de forma paralela los datos a Google Colab. 
+ **Identificar valores nulos a través IS NULL.**
  + Se identificaron los nulos solo en: last_month_salary (7199), number_dependents (943).
+ **Imputación simple de nulos a través de IFNULL, CASE, WHEN y ELSE.**
  + Se hizo una imputación simple.
    + last_month_salary, se observó su distribución por histograma y boxplot en Google Colab, además se calculó la moda por flag 0 y 1, por lo que resultó 2,500 para flag 1 y 5,000 flag 0.
    + number_dependents, se observó su distribución por histograma y boxplot en Google Colab, además se calculó la moda por flag 0 y 1, por lo que resultó 0 para ambas flags.
      + La moda se cálculo sin outliers. 
+ **Identificar duplicados a través de COUNT, GROUP BY, HAVING.**
  + Se han buscado valores duplicados utilizando comandos SQL, para las variables user_id y loan_id, considerando son las únicas variables que no deberían duplicarse a lo largo de los registros. No se encontraron duplicados.
    + Solo la tabla loans_outstanding cuenta con la duplicación de user_id, sin embargo, esto no significa sea un error, solo es la estructura de la tabla, ya que contiene todos los préstamos. 
+ **Identificar y manejar datos fuera del alcance del análisis a través de EXCEPT, CORR, STDDEV**
  + Se elimino la variable "sex", puesto que el género no es una variable permitida en los modelos de crédito.
  + Se hicieron correlaciones para observar la relación estadística entre las variables de la base de datos.
    + *Correlación en BigQuery*:
      + Correlación de retrasos entre 30-59/ retrasos entre 60-89: 0.98655364549866, correlación positiva extremadamente fuerte.
      + Correlación de retrasos más de 90 días/ retrasos entre 30-59: 0.98291680661459857, correlación positiva extremadamente fuerte.
      + Correlación de retrasos más de 90 días/ retrasos entre 60-89: 0.99217552634075257, correlación positiva extremadamente fuerte.
    + *Cálculo de la desviación estándar*:
      + stddev_number_times_30_59_days: 4.144020438225871
      + stddev_number_times_60_89_days: 4.1055147551019706
      + stddev_more_90: 4.12136466842672
    + La alta correlación entre dos variables indica que se debe elegir solo una variable para el análisis (modelo). La redundancia en la información de cada variable puede ser indicativo de multicolinealidad.
  + *Correlación en Google Colab*:
    + Se creo una matriz de correlación con el consolidado de las 4 tablas.
+ **Identificar y manejar datos inconsistentes en variables categóricas a través de SELECT DISTINCT y LOWER**
  + Se ha identificado variaciones en las categorías loan_type, a partir de un SELECT DISTINCT.
  + Se han estandarizado la variable loan_type, han quedado homologadas las categorías “real estate” y “others”, utilizando comandos SQL como LOWER.
+ **Identificar y manejar datos inconsistentes en variables numéricas**
  + Se han identificado y tratado los outliers de la siguiente manera:
  + *Tabla user_info:*
    + age: Se quitaron los usuarios mayores a 96 años.
    + last_month_salary: Se quitaron los salarios mayores a 400 mil.
    + number_dependents: Tiene outliers, sin embargo se quedan por la mínima cantidad de casos. 
  + *Tabla loan_detail:*
    + more_90_days_overdue: Se quitaron los valores de 96 y 98 veces de retrasos.
    + number_times_delayed_payment_loan_30_59_days: Se quitaron los valores de 96 y 98 veces de retrasos.
    + number_times_delayed_payment_loan_60_89_days: Se quitaron los valores de 96 y 98 veces de retrasos.
    + using_lines_not_secured_personal_assets: Tiene outliers, sin embargo se quedan por ser información crítica.
    + debt_ratio: Tiene outliers, sin embargo se quedan por ser información crítica.


+ *Tablas loan outstanding y default:*
    + No tienen outliers
   

Number dependents: Tiene outliers, sin embargo se quedan



## Enlaces
### [Presentación]()
### [Dashboard]()
### [Video Loom]()
