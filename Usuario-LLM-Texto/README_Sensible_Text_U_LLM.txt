#Detección de Información Sensible Desde el Usuario hacia el LLM

Este proyecto implementa un sistema que combina detección de información sensible, ofuscación de datos, y consulta a ChatGPT utilizando un modelo de lenguaje entrenado y la API de OpenAI.

## Instrucciones

1. **Subir los archivos de datos**:  
   - Asegúrate de subir al entorno de Google Colab los siguientes archivos:
     - Dataset de entrenamiento y prueba.
     - Archivo de validación para evaluar el modelo.

2. **Configurar las claves de acceso**:  
   - No olvides establecer las claves necesarias:
     - **Clave de la API de OpenAI**: Necesaria para acceder a ChatGPT.  
       ```python
       openai.api_key = "TU_CLAVE_API_OPENAI"
       ```
     - **Clave de Hugging Face**: Para cargar el modelo de Transformers.  
       ```python
       HUGGING_FACE_TOKEN = "TU_CLAVE_HUGGING_FACE"
       ```

3. **Ejecutar el código**:  
   - Sigue el flujo del código en Colab, desde la carga de los datos hasta el procesamiento del texto y la interacción con ChatGPT.

## Funcionalidades
- Detecta información sensible en texto (correos, números de tarjetas, etc.).
- Clasifica texto como sensible o no sensible usando un modelo entrenado.
- Ofusca información sensible para proteger la privacidad.
- Consulta a ChatGPT y devuelve respuestas con la información ofuscada.

## Requisitos
- Google Colab con Python.
- Paquetes necesarios instalados, como `transformers`, `datasets`, `openai`, y otros mencionados en el código.
