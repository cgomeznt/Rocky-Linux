===================================================
Asistente de IA para Documentos con Docker - RST
===================================================

----------------------------
Imagen Docker Recomendada
----------------------------

**DocsGPT** (solución todo-en-uno):

.. code-block:: bash

   docker pull ghcr.io/arc53/docsgpt:7.0
   docker run -d \
     -p 5000:5000 \
     -v $(pwd)/mis_documentos:/app/storage \
     -e DEFAULT_MODEL="gpt-3.5-turbo" \
     ghcr.io/arc53/docsgpt:7.0

----------------------------
Características Principales
----------------------------

+---------------------------+-----------------------------------------+
| Función                   | Descripción                             |
+===========================+=========================================+
| Formatos soportados       | PDF, DOCX, PPTX, XLSX, TXT, Markdown   |
+---------------------------+-----------------------------------------+
| Procesamiento automático  | Crea embeddings y base vectorial        |
+---------------------------+-----------------------------------------+
| Interfaz Web              | Chat interactivo en http://localhost:5000|
+---------------------------+-----------------------------------------+
| Modelos soportados        | GPT-3.5/4, Llama2, Claude (configurable)|
+---------------------------+-----------------------------------------+

----------------------------
Configuración Básica
----------------------------

1. **Preparar documentos**:
   - Colocar archivos en la carpeta ``./mis_documentos``
   - Estructura recomendada:

     .. code-block:: bash

        /mis_documentos
        ├── manual_operaciones.pdf
        ├── politicas.docx
        └── procedimientos/

2. **Variables de entorno opcionales**:

.. code-block:: bash

   -e EMBEDDINGS_MODEL="text-embedding-ada-002" \
   -e PERSIST_DIRECTORY="/app/vector_stores" \
   -e MAX_FILE_SIZE=1000000  # Tamaño máximo en bytes

----------------------------
Uso Avanzado con API
----------------------------

Ejemplo de consulta via cURL:

.. code-block:: bash

   curl -X POST http://localhost:5000/api/ask \
   -H "Content-Type: application/json" \
   -d '{
       "question": "¿Qué dice el manual sobre seguridad?",
       "collection": "default"
   }'

----------------------------
Alternativas
----------------------------

1. **PrivateGPT** (para CPU):

.. code-block:: bash

   docker pull imartinez/privategpt
   docker run -d \
     -p 8000:8000 \
     -v $(pwd)/data:/app/data \
     imartinez/privategpt

2. **LLamaIndex + FastAPI**:

.. code-block:: dockerfile

   FROM python:3.9
   RUN pip install llama-index transformers fastapi uvicorn
   COPY app.py /app/
   WORKDIR /app
   CMD ["uvicorn", "app:app", "--host", "0.0.0.0"]

----------------------------
Solución de Problemas
----------------------------

+-------------------------------+---------------------------------------+
| Error                         | Solución                              |
+===============================+=======================================+
| "Unsupported file format"     | Convertir a PDF/TXT con pandoc        |
+-------------------------------+---------------------------------------+
| "Model not loaded"            | Verificar variables de entorno        |
+-------------------------------+---------------------------------------+
| "CUDA out of memory"          | Usar modelos más pequeños o CPU       |
+-------------------------------+---------------------------------------+

----------------------------
Recomendaciones Clave
----------------------------

1. Para documentos técnicos: Usar ``text-embedding-ada-002``
2. Para mejor rendimiento: 
   - Limitar documentos a <50 páginas c/u
   - Usar estructura de carpetas lógica
3. Monitorear uso de memoria:
   .. code-block:: bash

      docker stats

----------------------------
Recursos Adicionales
----------------------------

- `Documentación Oficial DocsGPT <https://docsgpt.arc53.com/>`_
- `Repositorio GitHub PrivateGPT <https://github.com/imartinez/privateGPT>`_
- `Guía Rápida de LlamaIndex <https://docs.llamaindex.ai/>`_

Pasos Clave:
--------------------
Descargar imagen DocsGPT

Mapear carpeta con documentos

Acceder a la interfaz web en localhost:5000

Realizar preguntas en lenguaje natural
