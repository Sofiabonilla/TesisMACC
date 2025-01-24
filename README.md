# Detección de Información Sensible

Este proyecto se centra en la **detección y ofuscación de información e imágenes sensibles**. A continuación, se detallan los aspectos clave del sistema implementado.

---
## Información sensible
## Introducción
El propósito principal del sistema es garantizar la **privacidad de los usuarios** mediante la detección y ofuscación de información sensible antes de que los datos lleguen a un LLM. Esto incluye:

- Identificación automática de datos confidenciales.
- Clasificación entre texto sensible y no sensible.
- Aplicación de técnicas de ofuscación a información crítica.

El sistema combina técnicas avanzadas de **aprendizaje profundo** con modelos de Procesamiento del Lenguaje Natural (NLP) para ofrecer una solución precisa y eficiente.

---

## Arquitectura del Sistema

El sistema tiene una arquitectura híbrida que incluye los siguientes componentes principales:
1. **Clasificación de Datos Sensibles:** Utiliza un modelo basado en Transformers (DistilBERT) para identificar información sensible.
2. **LLM Principal:** El modelo GPT-3.5-turbo de OpenAI para manejar consultas y datos procesados.
3. **Ofuscación de Datos:** Módulo encargado de reemplazar información sensible con versiones seguras antes del procesamiento por el LLM.

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

---
## Resultado de uso

![Resultado de la detección y ofuscación](https://drive.google.com/file/d/1f5Q5b9kAfd2uum0HKVDljvabjmXXqTr4/view?usp=sharing)

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

![Ejemplo de uso de ofuscación](https://drive.google.com/file/d/17dfjJhN1lcdsu37UmJiUrDKBAUgG37uF/view?usp=sharing)
---

3. ## **¿Cómo se realiza el proceso de ofuscación?**

El proceso de ofuscación se lleva a cabo en varias etapas para proteger tanto la entrada del usuario como la respuesta generada por el modelo. A continuación se detalla el proceso paso a paso:

### Entrada del Usuario: 
Cuando el usuario ingresa su texto, el primer paso es procesar la entrada mediante un modelo de clasificación entrenado para identificar si el texto contiene información sensible. Este modelo utiliza la función predict para clasificar el texto como "sensible" (1) o "no sensible" (0). Además, se aplica una detección basada en patrones utilizando la función detect_sensitive_info, que identifica información sensible como direcciones, números de teléfono, o identificaciones personales, basándose en patrones predefinidos.

### Ofuscación de la Entrada: 
Una vez que el texto ha sido procesado, y si se detecta que contiene información sensible (o simplemente para proteger la privacidad del usuario), se aplica un proceso de ofuscación utilizando la función obfuscate. Esta función transforma el texto original en una forma alterada que oculta cualquier dato confidencial, asegurando que no se expongan detalles privados cuando se envía al modelo para su procesamiento.

### Respuesta del Modelo: 
Una vez que el texto ofuscado es enviado al modelo GPT-3.5-turbo de OpenAI, el modelo genera una respuesta basada en la entrada recibida. Sin embargo, como el modelo también puede generar respuestas que incluyan información sensible, se aplica un segundo proceso de ofuscación sobre la respuesta generada. Esto asegura que cualquier detalle confidencial que pudiera haber sido mencionado en la respuesta también quede oculto antes de ser presentado al usuario.

### Resultados Finales: 
El sistema devuelve tres resultados clave: primero, se indica si la entrada del usuario contenía información sensible. En segundo lugar, se muestra la entrada ofuscada que fue procesada por el modelo. Por último, se proporciona la respuesta ofuscada generada por el modelo. De esta manera, el proceso garantiza que tanto la entrada como la salida estén protegidas contra la exposición de datos sensibles.

En resumen, el proceso de ofuscación en este sistema es doble: se aplica tanto al texto ingresado por el usuario como a la respuesta generada por el modelo. Esto asegura que la privacidad y la seguridad de la información sensible se mantengan a lo largo de toda la interacción, desde el ingreso de datos hasta la obtención de respuestas. 

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
