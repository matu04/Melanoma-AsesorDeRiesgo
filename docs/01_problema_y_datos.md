# 1. Problema y análisis inicial de datos

## 1.1 Objetivo del proyecto

El objetivo de este proyecto es desarrollar un sistema de **Computer Vision** capaz de analizar imágenes dermatoscópicas de lesiones cutáneas y estimar el **riesgo de melanoma**.

El problema se plantea como una **clasificación binaria**:

* **0 → lesión benigna**
* **1 → melanoma (lesión maligna)**

El modelo recibirá como entrada una imagen de una lesión cutánea y deberá predecir la probabilidad de que dicha lesión corresponda a un melanoma.

El proyecto busca además comparar dos enfoques distintos:

* entrenamiento de una red neuronal convolucional **desde cero**
* uso de **transfer learning** a partir de modelos previamente entrenados

---

# 1.2 Dataset utilizado

Se utilizó un subconjunto del dataset **ISIC (International Skin Imaging Collaboration)**, compuesto por imágenes dermatoscópicas de lesiones cutáneas.

El dataset descargado contiene:

* una carpeta con imágenes:

```
data/train-image/
```

* un archivo de metadata:

```
data/train-metadata.csv
```

Cada fila del archivo de metadata contiene la información asociada a una imagen:

| columna      | descripción                                      |
| ------------ | ------------------------------------------------ |
| `isic_id`    | identificador de la imagen                       |
| `patient_id` | identificador del paciente                       |
| `target`     | etiqueta de clasificación (0 benigno, 1 maligno) |

Las imágenes están almacenadas como:

```
ISIC_xxxxxx.jpg
```

y el identificador `isic_id` permite relacionar cada imagen con su etiqueta.

---

# 1.3 Validación inicial del dataset

Durante el análisis inicial se detectó una inconsistencia entre el archivo de metadata y las imágenes disponibles.

El archivo `train-metadata.csv` contenía **más filas que imágenes presentes en el dataset descargado**. Esto significa que algunas filas hacían referencia a imágenes que no estaban disponibles localmente.

Para evitar errores durante el entrenamiento del modelo, se realizó un proceso de **filtrado del metadata**, conservando únicamente aquellas filas cuyo archivo de imagen correspondiente existía en el directorio de imágenes.

El dataset final utilizado contiene:

```
7496 imágenes etiquetadas
```

El metadata limpio fue guardado como:

```
data/metadata_clean.csv
```

Esto permite mantener separado:

* el dataset original
* el dataset procesado utilizado en el proyecto

Este paso es importante para garantizar **reproducibilidad y consistencia en el pipeline de datos**.

---

# 1.4 Distribución de clases

Se analizó la distribución de la variable objetivo (`target`) en el dataset limpio.

La proporción entre clases se mantiene aproximadamente constante respecto al dataset original, lo que indica que el proceso de limpieza no introdujo un sesgo significativo.

Esto permite utilizar el dataset sin necesidad de aplicar técnicas especiales de balanceo en esta etapa inicial.

---

# 1.5 Pacientes únicos

Se analizó también la cantidad de pacientes únicos presentes en el dataset.

El resultado mostró que:

```
cada imagen corresponde a un paciente distinto
```

Esto implica que **no existen múltiples imágenes del mismo paciente** dentro del dataset.

Este aspecto es relevante porque evita un problema frecuente en datasets médicos llamado **data leakage por paciente**, donde imágenes del mismo paciente pueden aparecer tanto en el conjunto de entrenamiento como en el de validación.

En este caso, al no existir pacientes repetidos, el dataset puede dividirse directamente por imágenes sin riesgo de introducir este tipo de fuga de información.

---

# 1.6 Inspección visual de las imágenes

Se realizó una inspección visual manual de muestras aleatorias del dataset con el objetivo de comprender las características visuales de las lesiones.

Durante esta inspección se observaron varios patrones relevantes.

## Lesiones benignas

Las lesiones benignas tienden a presentar:

* formas relativamente regulares
* bordes más definidos
* coloración homogénea
* apariencia más uniforme o rellena

## Lesiones malignas

Las lesiones malignas observadas presentan con mayor frecuencia:

* bordes irregulares
* contornos menos definidos
* variación significativa de color
* sombreado heterogéneo
* zonas internas con estructuras más complejas o discontinuas

Estas observaciones coinciden con algunos criterios dermatológicos clásicos utilizados para evaluar melanomas, como la irregularidad del borde y la **variación de color.

---

# 1.7 Posicionamiento de las lesiones

En la mayoría de las imágenes observadas, la lesión se encuentra aproximadamente **centrada dentro de la imagen**.

Esto sugiere que el dataset fue capturado bajo condiciones relativamente controladas, lo cual facilita el aprendizaje del modelo.

---

# 1.8 Variaciones en las imágenes

Durante la inspección visual también se detectaron algunas variaciones relevantes entre imágenes:

* diferencias en el nivel de zoom
* presencia ocasional de pelos
* algunas líneas o artefactos de iluminación
* variaciones en la iluminación general de la imagen

Estas variaciones representan ruido realista que el modelo deberá aprender a manejar.

En etapas posteriores del proyecto se utilizarán técnicas de **data augmentation** para mejorar la robustez del modelo frente a estas variaciones.

---

# 1.9 Conclusión del análisis inicial

El análisis inicial del dataset permitió confirmar que:

* el dataset es **consistente tras el proceso de limpieza**
* contiene **suficientes ejemplos para un primer modelo**
* las clases presentan **patrones visuales distinguibles**
* no existe riesgo de **data leakage por paciente**

Con esta validación completada, el siguiente paso del proyecto será construir el **pipeline de procesamiento de imágenes** para PyTorch, incluyendo:

* definición del **Dataset**
* aplicación de **transformaciones**
* creación de **DataLoaders** para entrenamiento y validación.

---

# Notas del desarrollador

* Debo profundizar en conceptos como data augmentation, aplicacion de transformaciones, y creacion de DataLoaders.
* Codigo bastante interpretable e intuitivo, interesante el segmento en el que se muestran las imagenes.
* Debo recordar que en ocasiones se generan archivos ocultos por la mac, como el `.DS_Store`, que era por lo que `train-image` tenia un archivo extra.
* Fundamental chequear la disposicion de los datos antes de los datos en si, casos como este que habia mas archivos de metadata que imagenes deben ser considerados, el espectro es mas amplio que tan solo ver la informacion que proporciona el dato frente a la problematica.