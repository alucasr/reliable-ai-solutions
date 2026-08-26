# **Masterclass Técnica: Embeddings en Profundidad – Modelos, Selección y Evaluación**

## **1. Fundamentos Matemáticos: Qué es un Embedding**

Un **embedding** es una representación vectorial de texto (o de cualquier dato) en un espacio de **dimensionalidad reducida**. Cada palabra, frase o documento se mapea a un vector real de dimensión *d* (ej: 768, 1024, 3072), donde cada componente representa una característica semántica del texto.

### **Dimensionalidad**
La **dimensionalidad** del embedding define la cantidad de parámetros necesarios para representar el contenido. Cuanto mayor sea la dimensión, más información se puede capturar, pero también mayor será el costo computacional y de almacenamiento. Por ejemplo:
- **text-embedding-3-small**: 1024 dimensiones
- **BGE-M3**: 768 dimensiones
- **E5-large-v2**: 1024 dimensiones

### **Similitud entre Vectores**
La **similitud entre embeddings** se mide mediante tres métricas principales:
- **Similitud coseno**: Mide el ángulo entre dos vectores. Útil para comparar direcciones.
- **Producto escalar**: Mide la magnitud de la proyección de un vector sobre otro. Más sensible a magnitudes.
- **Distancia euclídea**: Mide la longitud del segmento entre dos vectores. Más sensible a desviaciones absolutas.

En sistemas RAG, la **similitud coseno** es la métrica más común debido a su invariancia a escalas.

---

## **2. Panorama de Modelos de Embeddings en 2026**

En 2026, el ecosistema de embeddings se ha diversificado significativamente, con modelos open-source y closed-source compitiendo en calidad, eficiencia y especialización.

| Modelo | Proveedor | Coste (USD/token) | Dimensión | Contexto máximo | Rendimiento MTEB (aprox.) | Multilingüe | Dominio Específico |
|--------|-----------|------------------|-----------|------------------|---------------------------|-------------|---------------------|
| `text-embedding-3-small` | OpenAI | 0.0001 | 1024 | 8192 | 68.2% | ✅ | ❌ |
| `text-embedding-3-large` | OpenAI | 0.0003 | 1024 | 8192 | 72.5% | ✅ | ❌ |
| `BGE-M3` | BERT-Base, Multilingual | 0.00002 | 768 | 512 | 62.8% | ✅ | ❌ |
| `E5-large-v2` | Alibaba | 0.00005 | 1024 | 512 | 66.3% | ✅ | ❌ |
| `Nomic Embed` | Nomic AI | 0.00002 | 768 | 512 | 65.1% | ✅ | ❌ |
| `GTE` | Zhipu AI | 0.00002 | 768 | 512 | 64.7% | ✅ | ❌ |
| `Legal-BERT` | Custom | - | 768 | 512 | 75.4% | ❌ | ✅ |
| `Med-BERT` | Custom | - | 768 | 512 | 73.6% | ❌ | ✅ |

### **Multilingüe**
Los modelos multilingües como `BGE-M3` y `E5-large-v2` permiten embeddings de texto en múltiples idiomas (ej: inglés, español, francés). Son esenciales para empresas B2B con clientes internacionales.

### **Dominio Específico**
Los modelos de dominio específico (ej: `Legal-BERT`, `Med-BERT`) son entrenados con corpus técnicos, lo que mejora la capacidad de capturar términos especializados, pero requieren datos privados o de terceros.

---

## **3. Trade-offs Prácticos: Coste vs Calidad**

La elección de un embedding no es solo una cuestión de precisión, sino también de **eficiencia computacional** y **coste**.

### **Coste por Token**
Los modelos de embeddings tienen un **coste por token** que varía entre 0.00002 USD y 0.0003 USD. Los modelos open-source suelen ser más económicos, pero pueden requerir más recursos de procesamiento.

### **Latencia**
La latencia varía según el modelo y el hardware. Por ejemplo:
- `BGE-M3`: 10 ms (CPU)
- `text-embedding-3-large`: 25 ms (GPU)

### **Dimensionalidad y Almacenamiento**
Un embedding de 1024 dimensiones consume más espacio que uno de 768. Para índices grandes, esto puede multiplicar el costo de almacenamiento.

### **Contexto Máximo**
El **contexto máximo soportado** es un factor crítico. Modelos como `text-embedding-3-large` soportan hasta 8192 tokens, mientras que otros solo alcanzan 512. Esto es crucial para documentos muy largos.

---

## **4. Cómo Evaluar Embeddings para un Caso de Uso**

La evaluación de embeddings es esencial para asegurar que el modelo se adapte al caso de uso específico.

### **Benchmark MTEB**
El **Benchmark MTEB** es un conjunto de pruebas estándar que mide la capacidad de embeddings en tareas como:
- Retrieval
- Clustering
- Similarity

Sus límites incluyen:
- No refleja bien el contexto de los casos de uso B2B
- No evalúa la capacidad de capturar términos técnicos

### **Dataset Propio de Evaluación**
Para casos de uso específicos (ej: legal, médico), es mejor construir un **dataset propio** con pares de (query, documento) relevantes. Esto permite medir métricas como:
- **NDCG** (Normalized Discounted Cumulative Gain): Mide la calidad del ranking de resultados.
- **Recall@k**: Porcentaje de consultas donde al menos un documento relevante aparece en los primeros *k* resultados.
- **MRR** (Mean Reciprocal Rank): Mide la posición del primer documento relevante.

### **Ejemplo de Métricas**
| Métrica | Valor (ejemplo) |
|--------|----------------|
| NDCG@10 | 0.45 |
| Recall@5 | 68% |
| MRR | 0.22 |

---

## **5. Fine-tuning de Embeddings: Cuándo y Cómo**

El **fine-tuning de embeddings** puede mejorar significativamente la calidad en dominios muy especializados, pero tiene costes.

### **Cuándo es Útil**
- Dominios con vocabulario técnico o propio
- Necesidad de capturar términos específicos (ej: cláusulas legales, términos médicos)
- Desempeño del modelo base insuficiente en el caso de uso

### **Técnicas de Fine-tuning**
- **Contrastive Learning**: Entrena al modelo para distinguir entre pares relevantes e irrelevantes.
- **Hard Negative Mining**: Selecciona ejemplos difíciles para mejorar la discriminación.

### **Coste-Beneficio**
El fine-tuning requiere datos etiquetados y recursos computacionales. Para casos de uso B2B, es rentable si el modelo base no alcanza los niveles de precisión necesarios.

---

## **6. Errores Comunes y Cómo Evitarlos**

La elección y uso incorrecto de embeddings puede llevar a errores graves en el pipeline RAG.

### **1. Mezclar Embeddings de Diferentes Modelos**
Los embeddings de distintos modelos no son compatibles y pueden generar resultados incoherentes. Debe usarse un único modelo en el índice.

### **2. No Normalizar Vectores**
Los embeddings no normalizados pueden afectar la similitud coseno. Es recomendable normalizar los vectores antes de calcular la similitud.

### **3. Ignorar el Límite de Tokens de Entrada**
Los modelos tienen límites de contexto. Usar documentos más largos puede llevar a truncamiento o resultados inexactos.

### **4. No Re-embeber Tras Cambiar de Modelo**
Al cambiar de modelo, es fundamental re-embeber los documentos para asegurar coherencia en el índice.

---

## **Resumen Final**

La elección de embeddings define la calidad del **retrieval** y el **reranking** en todo el pipeline RAG. Un embedding mal elegido puede llevar a resultados inexactos, incluso si el resto del sistema es perfecto. Debe seleccionarse cuidadosamente según el **caso de uso**, el **coste computacional**, y la **calidad requerida**. Evaluar con métricas específicas y evitar errores comunes son pasos esenciales para garantizar un sistema RAG eficiente y preciso.