# Retrieval Vectorial en Profundidad: Índices, Algoritmos y Ajuste de Parámetros

## 1. Fundamentos matemáticos: similitud coseno vs distancia euclídea vs producto punto

La elección de la métrica de similitud es fundamental en sistemas de búsqueda vectorial. Cada métrica refleja una interpretación diferente de la relación entre vectores, y su uso depende del modelo de embeddings y el contexto de aplicación.

### Similitud coseno
- **Fórmula**: $ \text{cosine}(A, B) = \frac{A \cdot B}{\|A\| \|B\|} $
- **Interpretación**: Mide la orientación relativa de los vectores, ignorando su magnitud. Ideal para embeddings normalizados (como BERT o Sentence Transformers).
- **Ventajas**: Invariante a escalas, útil para comparar documentos de longitud variable.
- **Desventajas**: No captura información sobre magnitud, lo que puede ser relevante en aplicaciones como recomendación de productos.

### Distancia euclídea
- **Fórmula**: $ \text{distance}(A, B) = \sqrt{\sum_{i} (A_i - B_i)^2} $
- **Interpretación**: Mide la distancia absoluta entre vectores. Útil cuando se requiere precisión en la proximidad numérica (ej. imágenes o sensores).
- **Ventajas**: Captura variaciones en magnitud, sensible a cambios pequeños en los vectores.
- **Desventajas**: Sensible a escalas, requiere normalización de datos.

### Producto punto
- **Fórmula**: $ A \cdot B = \sum_{i} A_i B_i $
- **Interpretación**: Similar a la similitud coseno, pero sin normalizar los vectores. Útil en embeddings no normalizados (como algunos modelos de lenguaje).
- **Ventajas**: Rápido de calcular, compatible con sistemas de búsqueda en tiempo real.
- **Desventajas**: Sensible a escalas, requiere cuidado en normalización.

**Tabla comparativa:**
| Métrica         | Normalización | Sensibilidad a escalas | Aplicaciones típicas         |
|------------------|---------------|------------------------|------------------------------|
| Similitud coseno | Sí            | No                     | Texto, documentos             |
| Distancia euclídea | No          | Sí                     | Imágenes, sensores            |
| Producto punto   | No            | Sí                     | Embeddings no normalizados   |

## 2. Estructuras de índice para búsqueda vectorial aproximada (ANN)

La búsqueda vectorial aproximada (ANN) es esencial para manejar grandes volúmenes de datos. Cada estructura de índice equilibra velocidad, memoria y recall de forma diferente.

### HNSW (Hierarchical Navigable Small World)
- **Principio**: Grafo de vecinos cercanos con niveles de resolución.
- **Ventajas**: Alto recall, baja latencia, escalable para millones de vectores.
- **Desventajas**: Alto consumo de memoria, complejo de ajustar.
- **Usos**: Aplicaciones críticas donde recall > velocidad.

### IVF (Inverted File Index)
- **Principio**: Indices de bloques (subespacios) con un índice de clave.
- **Ventajas**: Baja memoria, rápido para consultas.
- **Desventajas**: Bajo recall, requiere PQ para mejorar eficiencia.
- **Usos**: Volúmenes grandes con tolerancia a baja precisión.

### IVF-PQ (Inverted File Index con Product Quantization)
- **Principio**: Comprime vectores en bloques con cuantización.
- **Ventajas**: Equilibrio entre velocidad y recall, eficiente en memoria.
- **Desventajas**: Configuración compleja, sensibilidad a la calidad de la cuantización.
- **Usos**: Aplicaciones industriales con balance entre rendimiento y precisión.

### LSH (Locality-Sensitive Hashing)
- **Principio**: Hashing que preserva proximidad de vectores.
- **Ventajas**: Baja latencia, escalable.
- **Desventajas**: Bajo recall, dependiente de parámetros de hashing.
- **Usos**: Búsquedas rápidas en sistemas de recomendación.

**Tabla comparativa de índices ANN:**
| Índice     | Velocidad | Memoria | Recall | Complejidad |
|------------|-----------|---------|--------|-------------|
| HNSW       | Alta      | Alta    | Alta   | Alta        |
| IVF        | Alta      | Baja    | Baja   | Baja        |
| IVF-PQ     | Alta      | Media   | Media  | Media       |
| LSH        | Alta      | Baja    | Baja   | Baja        |

## 3. Bases de datos vectoriales reales: pros y contras

La elección de la base de datos vectorial depende del volumen de datos, presupuesto y requisitos de escalabilidad. Aquí se comparan las principales soluciones:

### pgvector
- **Pros**: Integración con PostgreSQL, escalabilidad horizontal, soporte SQL.
- **Contras**: Menor rendimiento en consultas vectoriales comparado con especializados.
- **Usos**: Aplicaciones B2B con base de datos existente.

### Pinecone
- **Pros**: Escalabilidad automática, API simple, soporte para múltiples modelos.
- **Contras**: Costos altos para grandes volúmenes.
- **Usos**: Prototipos y MVPs con presupuesto limitado.

### Weaviate
- **Pros**: Soporte de grafos, integración con GraphQL, escalabilidad.
- **Contras**: Configuración compleja, dependencia de infraestructura.
- **Usos**: Aplicaciones con necesidad de razonamiento semántico.

### Qdrant
- **Pros**: Soporte para múltiples índices, API REST, escalabilidad.
- **Contras**: Menor soporte de comunidad comparado con otros.
- **Usos**: Aplicaciones con alta personalización.

### Milvus
- **Pros**: Escalabilidad horizontal, soporte para múltiples modelos, clusterización.
- **Contras**: Configuración compleja, alta demanda de recursos.
- **Usos**: Grandes corpora de datos en entornos empresariales.

### Chroma
- **Pros**: Fácil de usar, soporte para múltiples backends, integración con LangChain.
- **Contras**: Menor escalabilidad comparado con otros.
- **Usos**: Aplicaciones de prototipo y desarrollo rápido.

### FAISS
- **Pros**: Alto rendimiento, soporte para múltiples algoritmos, optimizado para CPU/GPU.
- **Contras**: Complejo de configurar, no ofrece API REST.
- **Usos**: Aplicaciones con necesidad de control total sobre el índice.

**Tabla comparativa de bases de datos vectoriales:**
| Base de datos | Escalabilidad | Soporte SQL | API REST | Costo | Usos típicos               |
|---------------|---------------|-------------|----------|-------|---------------------------|
| pgvector      | Alta          | Sí          | No       | Medio | Integración con PostgreSQL |
| Pinecone      | Alta          | No          | Sí       | Alto  | MVPs y prototipos         |
| Weaviate      | Alta          | No          | Sí       | Medio | Razonamiento semántico    |
| Qdrant        | Alta          | No          | Sí       | Medio | Personalización           |
| Milvus        | Alta          | No          | Sí       | Alto  | Grandes corpora           |
| Chroma        | Media         | No          | Sí       | Bajo  | Desarrollo rápido         |
| FAISS         | Alta          | No          | No       | Bajo  | Control total sobre índices |

## 4. Ajuste de parámetros: optimización del rendimiento

Los parámetros de búsqueda vectorial determinan el equilibrio entre velocidad, precisión y recursos. Su configuración requiere experiencia y pruebas.

### top-k
- **Definición**: Número máximo de resultados devueltos por consulta.
- **Impacto**: Mayor top-k aumenta recall pero reduce velocidad.
- **Recomendación**: Ajustar según necesidades de la aplicación (ej. top-k=10 para recomendaciones).

### ef_search/ef_construction (HNSW)
- **ef_search**: Controla la precisión de las consultas. Mayor valor mejora recall pero reduce velocidad.
- **ef_construction**: Afecta la construcción del índice. Mayor valor mejora la calidad del índice pero aumenta tiempo de construcción.
- **Recomendación**: Usar ef_search=100 y ef_construction=200 para equilibrio entre velocidad y recall.

### Umbrales de score mínimo
- **Definición**: Umbral de similitud coseno o distancia euclídea para incluir resultados.
- **Impacto**: Filtra resultados irrelevantes, mejora eficiencia.
- **Recomendación**: Establecer umbrales basados en análisis de datos históricos.

### Filtrado por metadatos (pre-filter vs post-filter)
- **Pre-filter**: Aplicar filtros en el índice (ej. categorías, fechas). Mejora velocidad pero reduce flexibilidad.
- **Post-filter**: Filtrar resultados después de la búsqueda. Más flexible pero consume más recursos.
- **Recomendación**: Usar pre-filter para condiciones fijas y post-filter para dinámicas.

## 5. Problemas habituales en producción

La implementación de sistemas de búsqueda vectorial enfrenta desafíos críticos que requieren atención técnica.

### Curse of dimensionality
- **Descripción**: Aumento de la sparsidad y la distancia entre vectores en dimensiones altas.
- **Solución**: Reducir dimensiones con PCA o autoencoders, o usar embeddings de baja dimensionalidad.

### Drift del índice
- **Descripción**: Cambios en los documentos afectan la calidad del índice.
- **Solución**: Reindexar periódicamente, usar índices dinámicos, o integrar nuevos documentos en el proceso de construcción.

### Necesidad de reindexado
- **Descripción**: Actualizaciones de datos requieren reconstrucción del índice.
- **Solución**: Implementar pipelines de reindexado automatizados, o usar índices incrementales.

### Latencia en índices grandes
- **Descripción**: Consultas en millones de vectores pueden ser lentas.
- **Solución**: Optimizar parámetros, usar múltiples índices, o distribuir el índice en clusters.

## 6. Métricas para evaluar la calidad del retrieval vectorial

Antes de pasar a reranking, es esencial medir la calidad del retrieval vectorial con métricas precisas.

### Recall@k
- **Definición**: Proporción de consultas donde al menos un resultado relevante está en los primeros k resultados.
- **Fórmula**: $ \text{Recall@k} = \frac{\text{Número de consultas con al menos un hit en top-k}}{\text{Total de consultas}} $
- **Uso**: Evalúa la capacidad del sistema para encontrar documentos relevantes.

### MRR (Mean Reciprocal Rank)
- **Definición**: Promedio de la inversa de la posición del primer resultado relevante en cada consulta.
- **Fórmula**: $ \text{MRR} = \frac{1}{N} \sum_{i=1}^{N} \frac{1}{\text{rank}_i} $
- **Uso**: Prioriza la precisión de los primeros resultados, útil en aplicaciones donde la relevancia inmediata es crítica.

## Resumen

La implementación de sistemas de búsqueda vectorial requiere un equilibrio entre algoritmos, parámetros y infraestructura. Los índices ANN como HNSW y IVF-PQ ofrecen soluciones escalables, mientras que las bases de datos vectoriales como Milvus y Pinecone se adaptan a distintos presupuestos. El ajuste de parámetros y la evaluación con métricas como Recall@k y MRR garantizan la calidad del retrieval. Sin embargo, problemas como el curse of dimensionality y el drift del índice exigen atención técnica continua. Este conocimiento prepara la base para el siguiente artículo sobre retrieval híbrido, donde se combinarán métodos vectoriales y basados en texto para optimizar la precisión y la eficiencia.