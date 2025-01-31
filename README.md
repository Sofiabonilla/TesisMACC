# ARQUITECTURA DE PROTECCIÓN DE PRIVACIDAD DE DATOS PARA MODELOS DE LENGUAJE DE GRAN TAMAÑO (LLM) USANDO CHATGPT

Este proyecto se centra en la **detección y ofuscación de información e imágenes sensibles**. A continuación, se detallan los aspectos clave del sistema implementado.

---
## Información sensible
## Introducción
El propósito principal del sistema es garantizar la privacidad de los usuarios mediante la detección y ofuscación de información sensible antes de que los datos lleguen a un LLM. Esto incluye:

Identificación automática de datos confidenciales.
Clasificación entre texto sensible y no sensible.
Aplicación de técnicas de ofuscación a información crítica.
El sistema combina técnicas avanzadas de aprendizaje profundo con modelos de Procesamiento del Lenguaje Natural (NLP) para ofrecer una solución precisa y eficiente. Para el caso de los datos en imágenes, se emplea YOLO (You Only Look Once) como modelo de detección de objetos, permitiendo identificar información sensible como documentos de identidad, tarjetas de crédito y otros elementos con alta precisión en tiempo real. Además, se incorpora lógica difusa, lo que permite evaluar la sensibilidad de los datos y aplicar niveles de ofuscación de manera adaptativa según el nivel de riesgo detectado. Esta combinación de tecnologías proporciona una solución escalable y efectiva para la protección de la privacidad en entornos digitales.

---
## Metodologia propuesta 
![metodo](https://drive.google.com/uc?id=1uyXNMTD0fl0myrMS-oWrRMMaAPCj4ZAz)

## Arquitectura del Sistema

La elección de las capas de la arquitectura se fundamenta en los objetivos establecidos, buscando maximizar la efectividad y eficiencia del sistema. Se ha implementado una variedad de modelos, cada uno seleccionado por su capacidad para abordar diferentes aspectos del problema
En la siguiente imagen se puede evidenciar la arquitectura establecida para este proyecto:
![arquitectura](https://drive.google.com/uc?id=1uc-RHOu-tU1hcjGGUshoeaXMjFcSeivB)

---

## Modelos Utilizados

### Modelo GPT-3.5 - Turbo
El modelo GPT-3.5-turbo se seleccionó por sus capacidades avanzadas de:
- Procesar textos complejos.
- Proporcionar respuestas rápidas y precisas.
- Ser más económico en términos de costo por token en aplicaciones a gran escala.

### Modelo DistilBERT
DistilBERT se empleó como clasificador de texto sensible debido a:
- Su eficiencia computacional.
- Rendimiento similar a BERT con menor coste.
- Precisión y rapidez para identificar datos sensibles en tiempo real.

### Modelo YOLO (You Only Look Once)
Se utilizó YOLOv8 para la detección de información sensible en imágenes, permitiendo:

- Identificación precisa de objetos como documentos de identidad, tarjetas de crédito y rostros.
- Procesamiento en tiempo real, facilitando el análisis de grandes volúmenes de imágenes.
- Generación de bounding boxes, que delimitan la información sensible detectada.

### Modelo de Lógica Difusa
Se implementó un sistema basado en lógica difusa para la clasificación adaptativa de datos sensibles, permitiendo:

- Ajustar dinámicamente el nivel de ofuscación según el grado de sensibilidad de los datos.
- Reducir falsos positivos y negativos, asegurando una mayor precisión en la protección de la privacidad.

---

## Base de Datos

El conjunto de datos contiene **frases** etiquetadas como:
- **Sensibles (1):** Incluyen datos como contraseñas, direcciones físicas, números telefónicos, correos electrónicos, nombres completos, identificaciones personales, cuentas bancarias y tarjetas de crédito.
- **No sensibles (0):** Frases sin información privada.

La base de datos se creó con:
1. Datos generados aleatoriamente mediante Mockaroo.
2. Etiquetado manual para reflejar casos realistas.
3. Frases proporcionadas por el LLM
---

## Flujo de Datos

### Preprocesamiento y Tokenización
- Se dividió el conjunto de datos en:
  - **Entrenamiento:** 80% de las frases.
  - **Prueba:** 20% restante.
- Se utilizó el tokenizador **DistilBertTokenizer**:
  - Truncamiento y relleno (padding) para garantizar una longitud máxima de 512 tokens.
  - Representación numérica compatible con modelos NLP.

### Creación de Dataset para PyTorch
Se definió la clase `SensitiveDataDataset`, que extiende `torch.utils.data.Dataset`:
- Gestiona las secuencias tokenizadas y etiquetas.
- Optimiza la carga de datos durante el entrenamiento.

### Entrenamiento del Modelo
El entrenamiento se configuró mediante la clase `Trainer` de Hugging Face Transformers:
- **Épocas:** 3 pasadas completas sobre los datos de entrenamiento.
- **Tamaño de batch:** 4 ejemplos por iteración.
- **Framework:** Hugging Face Transformers.

---

## Detección de Información Sensible
La función `detect_sensitive_info` utiliza **expresiones regulares** para identificar patrones comunes, incluyendo:

- **Correos Electrónicos:** Detecta patrones estándar de emails.
- **Tarjetas de Crédito:** Encuentra secuencias de números válidos.
- **Teléfonos:** Identifica números en formato local o internacional.
- **Identificaciones:** Como números de DNI o SSN.
- **Direcciones:** Detección básica de ubicaciones físicas.
- **Fechas de Nacimiento:** Reconoce formatos comunes de fechas.
- **Datos Médicos y Financieros:** Ejemplo: cuentas bancarias e historiales clínicos.

---

## Ofuscación de Datos Sensibles
Una vez detectada la información sensible, se aplica un filtro para **ofuscar** los datos antes de enviarlos al LLM. Esto incluye:

- Reemplazo de números con máscaras: `1234-5678-9012-3456` → `****-****-****-3456`.
- Reemplazo de nombres y correos electrónicos por ***** anonimización parcial.
- Permutación de nombres


# Modulo imagenes

## Base de Datos

El conjunto de datos contiene **imagenes** está organizado en dos grandes grupos:

1. Imágenes Sensibles: Contienen información crítica como documentos de identidad (ID cards), tarjetas de crédito, contenido explícito, rostros y sustancias relacionadas con medicamentos. Estas imágenes representan casos en los que la privacidad del usuario podría verse comprometida si la información no es protegida.
2. Imágenes No Sensibles: Incluyen objetos y elementos sin información privada, como paisajes, animales, y otros elementos generales que no requieren ofuscación.
Para el entrenamiento y validación, las imágenes están organizadas en carpetas de ‘train’, ‘test’ y ‘valid’, asegurando un proceso de aprendizaje estructurado y una correcta evaluación del modelo.

**Origen y Construcción del Conjunto de Datos** 
El conjunto de datos fue construido combinando diferentes fuentes para garantizar su diversidad y representatividad:

- Bases de datos de Roboflow y otras fuentes abiertas: Se utilizaron datasets preexistentes para obtener imágenes etiquetadas y estructuradas.
- Etiquetado automático con un programa en Python: Se desarrolló un script en Python que facilitó la asignación y reetiquetado de las imágenes, asegurando coherencia en la nomenclatura y compatibilidad con los modelos de detección.
- Revisión y normalización manual: Se verificaron las etiquetas para corregir posibles errores y garantizar una distribución equilibrada de las clases.
---

## Flujo de Datos

### Entrenamiento
En el caso de las *imagenes* Para el entrenamiento y validación, el conjunto de datos se dividió en:
- **Entrenamiento:** 70% de las imágenes.
- **Validación:** 15% de las imágenes.
- **Prueba:** 15% de las imágenes restantes.

### Creación de Dataset para PyTorch
Se definió la clase `SensitiveDataDataset`, que extiende `torch.utils.data.Dataset`:
- Gestiona las secuencias tokenizadas y etiquetas.
- Optimiza la carga de datos durante el entrenamiento
---
## Resultado de uso

![Resultado de la detección y ofuscación](https://drive.google.com/uc?id=1f5Q5b9kAfd2uum0HKVDljvabjmXXqTr4)

---

## Cómo Ejecutar el Proyecto

### Requisitos Previos
- Python 3.8 o superior.
- Google colab
- Bibliotecas:
  - Transformers (Hugging Face)
  - PyTorch
  - Scikit-learn
  - openai
  - datasets
  - YOLOV8

### Instalación
1. Clona este repositorio:
   ```bash
   git clone (https://github.com/Sofiabonilla/TesisMACC.git)
  
2. **Subir los archivos de datos**:  
   - Asegúrate de subir al entorno de Google Colab los siguientes archivos:
     - Dataset de entrenamiento y prueba.
     - Archivo de validación para evaluar el modelo.

3. **Configurar las claves de acceso**:  
   - No olvides establecer las claves necesarias:
     - **Clave de la API de OpenAI**: Necesaria para acceder a ChatGPT.  
       ```python
       openai.api_key = "TU_CLAVE_API_OPENAI"
       ```
     - **Clave de Hugging Face**: Para cargar el modelo de Transformers.  
       ```python
       HUGGING_FACE_TOKEN = "TU_CLAVE_HUGGING_FACE"
       ```

4. **Ejecutar el código**:  
   - Sigue el flujo del código en Colab, desde la carga de los datos hasta el procesamiento del texto y la interacción con ChatGPT.


---
## Preguntas Frecuentes.

1. ## **¿Cómo se están entrenando los modelos?**
Los modelos en este proyecto se entrenan siguiendo un proceso estructurado que combina técnicas avanzadas de procesamiento del lenguaje natural y aprendizaje automático. A continuación, se detalla el proceso basado en la información proporcionada:

### Preparación de los Datos:

Se utiliza una base de datos etiquetada con 839 frases, clasificadas como sensibles (1) o no sensibles (0). Estas frases fueron generadas y ajustadas manualmente para garantizar que representen casos realistas.
Los datos sensibles incluyen categorías como contraseñas, números de tarjetas de crédito, correos electrónicos y direcciones físicas.
División de los Datos:

### Los datos se dividen en dos conjuntos utilizando la función train_test_split:
Conjunto de entrenamiento: Para ajustar los parámetros del modelo.
Conjunto de prueba: Para evaluar el rendimiento y prevenir el sobreajuste.
Tokenización:

Se utiliza el DistilBertTokenizer para convertir las frases en una representación numérica adecuada para los modelos.
Se configuran parámetros como truncamiento y relleno (padding) para asegurar que todas las entradas tengan una longitud uniforme (máximo 512 tokens).

### Creación del Dataset:
Se implementa una clase personalizada SensitiveDataDataset para manejar las secuencias tokenizadas y etiquetas. Esto permite que el modelo procese los datos eficientemente durante el entrenamiento.

### Configuración del Entrenamiento:
Se emplea el framework Hugging Face Transformers y la clase Trainer, que simplifica el proceso.

### Los parámetros principales incluyen:
Épocas: 3 pasadas completas sobre el conjunto de entrenamiento.
Tamaño de batch: 4 ejemplos por iteración (un balance entre memoria y eficiencia).
El entrenamiento ajusta los pesos del modelo en base a las entradas y etiquetas.

### Modelos Utilizados:
DistilBERT: Clasificador principal para identificar información sensible, elegido por su rendimiento eficiente con menor costo computacional.
GPT-3.5 Turbo: LLM encargado de procesar y generar respuestas después de la clasificación y ofuscación de datos sensibles.
Este proceso asegura que el sistema sea robusto, preciso y capaz de manejar datos en tiempo real mientras protege la privacidad del usuario. 

2.  ## **¿La solución es capaz de detectar y ofuscar cualquier tipo de información sensible?**

Sí, la solución descrita es capaz de detectar y ofuscar diversos tipos de información sensible. El proceso incluye un sistema que utiliza un enfoque híbrido para identificar datos privados, integrando un modelo de aprendizaje profundo basado en GPT-3.5-turbo y DistilBERT para la clasificación de secuencias. La base de datos utilizada contiene categorías diversas de información sensible como claves de acceso, direcciones físicas, números de teléfono, correos electrónicos, nombres completos, identificaciones personales, cuentas bancarias, y tipos de tarjetas de crédito, lo que asegura que el sistema pueda manejar una amplia gama de riesgos de privacidad. Además de la capacidad para detectar información sensible mediante clasificación y formatos específicos, el sistema también incluye una parte que detecta patrones específicos dentro del texto. Este enfoque es crucial para identificar datos sensibles que podrían no estar explícitamente etiquetados como tales, pero que siguen un patrón común. Por ejemplo, números de teléfono, direcciones de correo electrónico, o incluso estructuras de claves de acceso que siguen una secuencia predecible.

![Ejemplo de uso de ofuscación](https://drive.google.com/uc?id=17dfjJhN1lcdsu37UmJiUrDKBAUgG37uF)
---

3. ## **¿Cómo se realiza el proceso de ofuscación?**

El proceso de ofuscación se lleva a cabo en varias etapas para proteger tanto la entrada del usuario como la respuesta generada por el modelo. A continuación se detalla el proceso paso a paso:

### Entrada del Usuario: 
Cuando el usuario ingresa su texto, el primer paso es procesar la entrada mediante un modelo de clasificación entrenado para identificar si el texto contiene información sensible. Este modelo utiliza la función predict para clasificar el texto como "sensible" (1) o "no sensible" (0). Además, se aplica una detección basada en patrones utilizando la función detect_sensitive_info, que identifica información sensible como direcciones, números de teléfono, o identificaciones personales, basándose en patrones predefinidos.

### Ofuscación de la Entrada: 
Una vez que el texto ha sido procesado, y si se detecta que contiene información sensible (o simplemente para proteger la privacidad del usuario), se aplica un proceso de ofuscación utilizando la función obfuscate. Esta función transforma el texto original en una forma alterada que oculta cualquier dato confidencial, asegurando que no se expongan detalles privados cuando se envía al modelo para su procesamiento.

### Respuesta del Modelo: 
**texto** Una vez que el texto ofuscado es enviado al modelo GPT-3.5-turbo de OpenAI, el modelo genera una respuesta basada en la entrada recibida. Sin embargo, como el modelo también puede generar respuestas que incluyan información sensible, se aplica un segundo proceso de ofuscación sobre la respuesta generada. Esto asegura que cualquier detalle confidencial que pudiera haber sido mencionado en la respuesta también quede oculto antes de ser presentado al usuario.

### Resultados Finales: 
El sistema devuelve tres resultados clave: primero, se indica si la entrada del usuario contenía información sensible. En segundo lugar, se muestra la entrada ofuscada que fue procesada por el modelo. Por último, se proporciona la respuesta ofuscada generada por el modelo. De esta manera, el proceso garantiza que tanto la entrada como la salida estén protegidas contra la exposición de datos sensibles.

En resumen, el proceso de ofuscación en este sistema es doble: se aplica tanto al texto ingresado por el usuario como a la respuesta generada por el modelo. Esto asegura que la privacidad y la seguridad de la información sensible se mantengan a lo largo de toda la interacción, desde el ingreso de datos hasta la obtención de respuestas. 


## Modulo imagenes 
# Preguntas Frecuentes

## **1. ¿Cómo se están entrenando los modelos?**

Los modelos se entrenan con un conjunto de imágenes etiquetadas en **Python**, dividiéndolas en **70% entrenamiento, 15% validación y 15% prueba**. Se usa **YOLOv8** para generar **bounding boxes** en imágenes sensibles, mejorando la precisión del modelo en la detección de datos confidenciales como documentos de identidad y tarjetas de crédito. El entrenamiento se optimiza con varias épocas para minimizar falsos positivos, y se integra **lógica difusa** para ajustar dinámicamente la ofuscación según el nivel de riesgo detectado.

---

## **2. ¿La solución es capaz de detectar y ofuscar cualquier tipo de información sensible?**

Sí, la solución es capaz de detectar y ofuscar diversos tipos de información sensible en imágenes. Se ha entrenado un modelo de **visión por computadora basado en YOLOv8** para identificar elementos visuales que contienen datos privados, incluyendo:

- **Documentos de identidad (ID Cards)**
- **Tarjetas de crédito y datos financieros**
- **Rostross**
- **Contenido explícito o inapropiado**

El sistema detecta estos elementos con **bounding boxes**, clasificándolos según su nivel de sensibilidad y aplicando **ofuscación dinámica basada en lógica difusa**. Este enfoque permite adaptar el grado de protección según el tipo de dato detectado, evitando la exposición de información sensible en entornos donde la privacidad es una preocupación crítica.

---

## **3. ¿Cómo se realiza el proceso de ofuscación?**

El proceso de ofuscación se lleva a cabo en varias etapas para garantizar que la información sensible no quede expuesta en imágenes procesadas por el sistema.

### **1. Análisis y Detección**
- Se cargan las imágenes en el sistema, donde son procesadas por **YOLOv8**.
- El modelo genera **bounding boxes** alrededor de los elementos identificados como datos sensibles.
- Cada detección se clasifica según su nivel de riesgo utilizando **lógica difusa**, lo que permite aplicar ofuscación personalizada.

### **2. Aplicación de Ofuscación**
- Dependiendo del nivel de sensibilidad, el sistema aplica diferentes métodos de ofuscación, como **borrado, difuminado o pixelado** sobre los elementos detectados.
- La ofuscación se adapta dinámicamente para ocultar la información sin afectar la comprensión general de la imagen.

### **3. Generación de Resultados**
- Se generan tres versiones de la imagen:
  1. **Imagen original con bounding boxes** que indican las áreas sensibles.
  2. **Imagen ofuscada** donde los elementos sensibles han sido ocultados.
  3. **Informe de detección** con detalles sobre los datos identificados y el tipo de ofuscación aplicada.

Este enfoque garantiza que cualquier imagen procesada por el sistema **mantenga la privacidad del usuario** sin comprometer la integridad de la información visual no sensible.
---

## Contribuciones
¡Todas las contribuciones son bienvenidas! Por favor, abre un [issue] o envía un pull request con tus propuestas.

---

##  ¿A quién puedo contactar si tengo alguna pregunta adicional?

- Sofia Luisa Carolina Bonilla Beltrán at sofia.bonilla@urosario.edu.co
- Danna Natalia Ocampo Candela at dannan.ocampo@urosario.edu.co
- Daniel Díaz-Lopez at danielo.diaz@urosario.edu.co
- Pedro Mario Wightman Rojas at pedro.wightman@urosario.edu.co

----------
© 2025 -  Todos los derechos reservados.
