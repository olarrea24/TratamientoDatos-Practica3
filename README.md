# TratamientoDatos-Practica3
Tratamiento de Datos - Ejercicio práctico clase 3 - grupo 8

# Descripción del propósito del dataset
El conjunto de datos Cyberattacks Detection disponible en Kaggle fue diseñado para entrenar y evaluar modelos de detección de ciberataques mediante técnicas de aprendizaje automático. Contiene 100,000 registros, cada uno representando un evento único de ciberataque, con una variedad de tipos de ataques, protocolos y sistemas afectados. 
Kaggle

Este dataset es ideal para tareas de clasificación supervisada, permitiendo la creación de modelos capaces de identificar y clasificar distintos tipos de ciberataques en entornos de redes. Su estructura y etiquetado lo hacen adecuado para prácticas en cursos de ciberseguridad y análisis de datos.

# Explicación de los pasos de limpieza y transformación
## Eliminación de la columna de tiempo
Notamos que unicamente el campo de tiempo tiene gran cantidad de datos vacios, sin embargo esta columna no se la utilizara, ademas notamos que en base a https://cybermap.kaspersky.com/es el numero de ataques se da cada 41 segundos en promedio, con lo cual concluimos que no es relevante.

df = df.drop(columns=['Timestamp'])

La columna Timestamp no aporta información útil para el aprendizaje del modelo en este caso. Reduce ruido y la dimensionalidad del dataset.

## Inspección de la distribución de ataques

df.groupby('Attack Type').size()

Ayuda a ver cuántos registros hay por cada tipo de ataque. Detecta clases desbalanceadas que podrían afectar el entrenamiento.

## Eliminación de duplicados y valores infinitos
df = df.dropna()
df = df.replace([np.inf, -np.inf], np.nan)
df = df.dropna()

dropna(): elimina filas con valores nulos.

replace([np.inf, -np.inf], np.nan): convierte valores infinitos en NaN para poder eliminarlos.

Garantiza que el dataset no tenga valores que puedan romper el entrenamiento del modelo.

## Clasificación de IPs (pública o privada)
ef tipo_ip(ip):
    try:
        return "Privada" if ipaddress.ip_address(ip).is_private else "Pública"
    except ValueError:
        return "Inválida"

Toma una dirección IP y devuelve si es Privada, Pública o Inválida. Crea una nueva feature que podría ser relevante para detectar patrones de ataques según la procedencia de las IPs.

## Insertar nuevas columnas de tipo de IP
df_ips.insert(df_ips.columns.get_loc("Source IP") + 1,
              "Source IP Tipo",
              df_ips["Source IP"].apply(tipo_ip))

df_ips.insert(df_ips.columns.get_loc("Destination IP") + 1,
              "Destination IP Tipo",
              df_ips["Destination IP"].apply(tipo_ip))

insert: agrega la nueva columna justo después de Source IP y Destination IP.

apply(tipo_ip): aplica la función creada a cada fila para clasificar las IPs.

Enriquecer el dataset con información derivada que puede ayudar al modelo a diferenciar patrones de ataque según IP origen/destino

# Principales hallazgos del análisis

Tras la limpieza y transformación del dataset de ciberataques, se eliminaron duplicados, valores nulos e infinitos, y se descartó la columna de tiempo por ser irrelevante. Se creó información derivada mediante la clasificación de IPs en públicas y privadas, generando nuevas columnas Source IP Tipo y Destination IP Tipo. El análisis reveló un desbalance de clases, con algunos tipos de ataque mucho más frecuentes que otros, así como diferencias en la proporción de IPs públicas y privadas involucradas. Estas transformaciones y hallazgos son fundamentales para preparar el dataset, permitiendo entrenar modelos de Machine Learning robustos y confiables para la detección y clasificación de ataques en entornos de red.

# Cualquier insight o conclusión relevante

El análisis del dataset de ciberataques mostró que un tratamiento cuidadoso de los datos es esencial para entrenar modelos confiables de detección y clasificación. La limpieza de valores nulos, duplicados e inconsistentes, junto con la creación de variables derivadas como la clasificación de IPs en públicas y privadas, permitió generar información relevante para los modelos. Estos pasos destacan patrones importantes, como el desbalance de tipos de ataque y la relación entre la procedencia de las IPs y la detección de eventos. En ciberseguridad, donde las decisiones deben ser rápidas y precisas, un preprocesamiento adecuado garantiza que los modelos de aprendizaje automático puedan detectar amenazas de manera robusta, minimizando falsos positivos y optimizando la capacidad de respuesta ante ataques reales.
