# 🧠 MASTERCLASS TÉCNICA: **Chunking Inteligente: Estrategias de Segmentación de Documentos para RAG**

El **chunking** es la base de todo sistema de **Retrieval-Augmented Generation (RAG)**. No es un paso técnico secundario: es una decisión de diseño críticamente importante que define la eficacia del pipeline completo. En esta masterclass, exploraremos en profundidad cómo estructurar el chunking para optimizar el rendimiento del RAG, desde estrategias de segmentación hasta herramientas reales y técnicas de evaluación.

---

## 1. ¿Por qué el chunking es una decisión crítica en RAG?

El **chunking** consiste en dividir documentos grandes en fragmentos más pequeños, llamados **chunks**, que luego se indexan y se utilizan para el **retrieval**. La elección de cómo y cuándo dividir los documentos tiene un impacto directo en:

- La **capacidad del modelo para encontrar información relevante**.
- La **precisión y coherencia de las respuestas generadas**.
- La **velocidad y eficiencia del sistema**.

### Ejemplos de fallos por mal chunking:

- **Chunking por tamaño fijo sin overlap**: Un contrato legal de 100 páginas dividido en chunks de 1000 caracteres puede resultar en que una sección clave sea cortada al mitad, lo que impide que el modelo entienda el contexto completo.
- **Chunking por tokens sin considerar estructura**: Un manual técnico con tablas se divide en chunks que incluyen solo una parte de la tabla, lo que hace que el modelo no pueda interpretar correctamente los datos.
- **Chunking semántico inadecuado**: Un FAQ con preguntas muy similares se divide en chunks que no reflejan la relación semántica entre ellas, lo que reduce la capacidad de recuperación.

En todos estos casos, el chunking no solo afecta la **precisión del retrieval**, sino también la **integridad semántica** de la información disponible.

---

## 2. Estrategias de chunking: ¿Cuál elegir?

Existen múltiples estrategias para segmentar documentos, cada una con sus ventajas y limitaciones. A continuación, revisamos las más comunes:

| Estrategia de chunking | Descripción | Ventajas | Desventajas |
|------------------------|-------------|----------|-------------|
| **Chunking fijo (fixed-size)** | Divide el documento en chunks de tamaño fijo (ej. 1000 caracteres), con **overlap** opcional | Sencillo de implementar | Puede cortar frases o conceptos al mitad |
| **RecursiveCharacterSplitter** | Divide el texto recursivamente por caracteres, permitiendo chunks más pequeños | Flexibilidad | Puede generar muchos chunks, reduciendo la eficiencia |
| **Semantic Chunking** | Basado en embeddings: agrupa frases con alta similitud semántica | Mejora la relevancia del retrieval | Requiere un modelo de embeddings y computación adicional |
| **Chunking consciente de estructura (Markdown/HTML)** | Identifica y preserva estructuras como headers, tablas, listas | Mejora la legibilidad y relevancia | Requiere análisis de estructura del documento |
| **Chunking por tokens** | Divide el texto por tokens del modelo (ej. BERT tokenizer) | Alineado con el modelo | Puede no reflejar el sentido humano |

### Ejemplo práctico: Chunking fijo vs. chunking semántico

- **Chunking fijo (1000 caracteres)**:  
  Puede cortar una frase como *"El contrato se considera válido si el cliente acepta las condiciones"* al mitad, lo que hace que el modelo no entienda el contexto completo.

- **Chunking semántico**:  
  Agrupa frases con similaridad semántica, manteniendo la coherencia del mensaje. Es especialmente útil en documentos con preguntas frecuentes o listas de definiciones.

---

## 3. Parámetros clave: ¿Cómo elegir chunk_size y chunk_overlap?

La elección de `chunk_size` y `chunk_overlap` es crítica para el rendimiento del sistema. Estos parámetros deben ajustarse según el tipo de documento:

| Tipo de documento | Recomendación de chunk_size | Recomendación de chunk_overlap | Notas |
|-------------------|-----------------------------|--------------------------------|-------|
| **Contratos legales (largos)** | 500–1000 caracteres | 200–500 caracteres | Evita cortar frases legales al mitad |
| **FAQs (cortas)** | 100–300 caracteres | 50–100 caracteres | Mantiene la coherencia de las preguntas |
| **Manuales técnicos (con tablas)** | 500–1000 caracteres | 200–500 caracteres | Evita cortar tablas o listas |

### Ejemplo de ajuste:

- **Contrato**: `chunk_size=1000`, `chunk_overlap=200`  
  Esto asegura que cada sección legal se divida en chunks que incluyen el contexto necesario para entender el significado.

- **Manual técnico**: `chunk_size=800`, `chunk_overlap=300`  
  Permite que las tablas se dividan en chunks completos, preservando su integridad.

---

## 4. Herramientas y librerías reales

Existen varias herramientas y librerías que ofrecen implementaciones de chunking. A continuación, se detallan las más relevantes:

| Herramienta | Descripción | Ventajas | Desventajas |
|-------------|-------------|----------|-------------|
| **LangChain TextSplitters** | Incluye `RecursiveCharacterTextSplitter`, `MarkdownHeaderTextSplitter`, etc. | Flexible, integrado con LangChain | Requiere configuración manual |
| **LlamaIndex NodeParsers** | Permite definir parsers personalizados para chunking | Alta personalización | Requiere conocimiento de LlamaIndex |
| **unstructured.io chunking by_title** | Divide documentos por secciones con títulos | Facilidad para documentos estructurados | Requiere análisis de metadatos |
| **semantic-chunkers** | Base de chunking semántico basado en embeddings | Mejora la relevancia del retrieval | Requiere modelos de embeddings y computación adicional |

### Ejemplo de implementación:

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter

text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    length_function=len
)

chunks = text_splitter.split_text(long_document)
```

---

## 5. Problemas habituales y cómo mitigarlos

Aunque el chunking es esencial, existen problemas comunes que pueden afectar la calidad del sistema:

| Problema | Causa | Solución |
|---------|------|----------|
| **Pérdida de contexto al cortar tablas/listas** | Chunking fijo corta en medio de una tabla o lista | Usar chunking consciente de estructura o aumentar el `chunk_overlap` |
| **Chunks demasiado pequeños** | Chunk_size demasiado pequeño | Aumentar `chunk_size` o reducir `chunk_overlap` |
| **Chunks demasiado grandes** | Chunk_size demasiado grande | Reducir `chunk_size` o usar chunking semántico para dividir lógicamente el contenido |
| **Pérdida de relevancia en retrieval** | Chunks grandes o sin contexto | Usar metadata (parent document, sección, página) para mejorar la recuperación |

### Metadata de chunk

Incluir metadata como:

- `parent_document`: nombre del documento original
- `section`: sección del documento
- `page_number`: página en la que se encuentra el chunk

Esto permite al modelo contextualizar mejor los resultados del retrieval.

---

## 6. Evaluación empírica de estrategias de chunking

Para comparar distintas estrategias de chunking, se recomienda usar métricas de **retrieval** como:

- **Recall@k**: porcentaje de preguntas que se responden correctamente al recuperar los k chunks más relevantes.
- **Precision@k**: porcentaje de chunks recuperados que son relevantes para la pregunta.
- **ROUGE**: para medir la coherencia entre la respuesta generada y el contenido del chunk.

### Ejemplo de evaluación:

```python
from langchain.evaluation import load_evaluator

evaluator = load_evaluator("retrieval_metrics", metrics=["recall@k", "precision@k"])
results = evaluator.evaluate([query1, query2, query3], [chunks1, chunks2, chunks3])
```

Este tipo de evaluación permite identificar qué estrategia de chunking genera mejores resultados para el RAG.

---

## 📌 Resumen y conexión con el siguiente tema

El chunking es la base del RAG, y su implementación adecuada define el éxito del sistema. Por eso, es esencial entender cómo segmentar documentos según su estructura y contenido. Con una buena estrategia de chunking, el sistema puede recuperar información relevante y generar respuestas coherentes.

En la próxima masterclass, exploraremos cómo **embeddings** y **similitud semántica** pueden mejorar aún más la eficacia del retrieval, integrando el chunking con técnicas de representación de texto. ¡No te lo pierdas!