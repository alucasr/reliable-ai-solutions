# Retreival Hibrido en Profundidad: BM25, Fusion de Rankings y Ajuste Fino

El retrieval híbrido combina el poder de los algoritmos basados en palabras clave (como BM25) con los modelos semánticos (como embeddings). Este enfoque permite equilibrar precisión y cobertura, adaptándose a casos de uso complejos donde la búsqueda exacta y la comprensión contextual son críticas. A continuación, exploramos los fundamentos, arquitecturas, algoritmos de fusión, implementaciones reales y desafíos de producción de este enfoque.

---

## 1. Fundamentos de BM25: TF-IDF Mejorado

BM25 (Best Match First) es una variante mejorada de TF-IDF (Term Frequency-Inverse Document Frequency) que resuelve limitaciones de la version original. Su formula está definida como:

$$
\text{BM25}(d, q) = \sum_{i=1}^{n} \frac{(k_1 + 1) \cdot \text{TF}(q_i, d)}{(k_1(1 - b) + \text{TF}(q_i, d))} \cdot \left( \frac{1 - b + b \cdot \frac{|d|}{\text{avgdl}}}{1 - b + b \cdot \frac{|d|}{\text{avgdl}}} \right)
$$

Donde:
- $ k_1 $: parámetro que controla la influencia de la frecuencia de términos (TF) en el documento. Valores altos favorecen documentos con más ocurrencias del término.
- $ b $: parámetro que normaliza la longitud del documento. Valores cercanos a 0 priorizan documentos cortos, mientras que valores cercanos a 1 priorizan documentos de longitud promedio.
- $ |d| $: longitud del documento.
- $ \text{avgdl} $: longitud promedio de documentos en el corpus.

**¿Por qué sigue siendo relevante?**
A pesar de la popularidad de los embeddings, BM25 sigue siendo eficaz en casos donde:
- Se requieren **exact matches** (ej.: búsqueda de códigos legales).
- Los términos son **altamente específicos** y raramente ambigüos.
- El **coste computacional** de los modelos semánticos es crítico (ej.: en sistemas de baja latencia).

BM25 no depende de datos de entrenamiento y es robusto en entornos con vocabularios limitados, lo que lo hace ideal para dominios B2B con terminología precisa.

---

## 2. Arquitecturas de Retrieval Hibrido

### **A. Búsqueda Paralela + Fusión**
En este enfoque, BM25 y el modelo semántico (ej.: cosine similarity) se ejecutan en paralelo, generando dos listas de resultados. Las puntuaciones se combinan mediante un algoritmo de fusión (ej.: RRF o weighted scores).

**Ventajas:**
- Mayor cobertura: Combina resultados de ambas técnicas.
- Flexibilidad: Permite ajustar pesos según el caso de uso.

**Desventajas:**
- Mayor complejidad de implementación.
- Coste adicional de mantener dos índices.

### **B. Búsqueda Secuencial (Filtering)**
Aquí, uno de los métodos actúa como filtro del otro. Por ejemplo, BM25 se usa para preseleccionar documentos, y luego el modelo semántico refina los resultados.

**Ventajas:**
- Menor latencia: Reducción de la cantidad de documentos que el modelo semántico debe procesar.
- Eficiencia: Ideal para sistemas con limitaciones de recursos.

**Desventajas:**
- Menor flexibilidad: La dependencia entre los métodos reduce la capacidad de adaptación.

**Comparación de arquitecturas:**

| Característica         | Búsqueda Paralela + Fusión | Búsqueda Secuencial (Filtering) |
|------------------------|-----------------------------|----------------------------------|
| Latencia               | Alta                        | Baja                             |
| Complejidad            | Alta                        | Media                            |
| Cobertura              | Alta                        | Media                            |
| Adaptabilidad          | Alta                        | Baja                             |
| Requerimiento de índices | 2 índices                  | 2 índices                        |

---

## 3. Algoritmos de Fusión

### **A. Reciprocal Rank Fusion (RRF)**
RRF combina puntuaciones de múltiples algoritmos mediante una función que prioriza documentos con altas puntuaciones en al menos un algoritmo. Su formula es:

$$
\text{RRF}(d) = \sum_{i=1}^{m} \frac{1}{\text{rank}_i + \text{offset}}
$$

Donde:
- $ \text{rank}_i $: posición del documento en el resultado del algoritmo $ i $.
- $ \text{offset} $: constante que evita divisiones por cero (ej.: 100).

**Ejemplo numérico:**
Supongamos dos algoritmos (BM25 y cosine) que devuelven resultados para un documento:
- BM25: puntuación = 0.8, posición = 1.
- Cosine: puntuación = 0.7, posición = 2.

Si offset = 100:
- RRF = $ \frac{1}{1 + 100} + \frac{1}{2 + 100} = 0.0099 + 0.0098 = 0.0197 $.

Este documento se posiciona alto en la fusión, reflejando su relevancia en al menos un algoritmo.

### **B. Weighted Score Fusion**
Este método asigna pesos a las puntuaciones de cada algoritmo. La formula es:

$$
\text{Score}(d) = \alpha \cdot \text{BM25}(d) + (1 - \alpha) \cdot \text{Cosine}(d)
$$

Donde $ \alpha \in [0, 1] $ es el peso asignado a BM25. Por ejemplo, $ \alpha = 0.7 $ prioriza los resultados de BM25.

### **C. Normalización de Puntuaciones Heterogéneas**
Al fusionar puntuaciones de BM25 (rango [0,1]) y cosine (rango [0,1]), es necesario normalizar para evitar sesgos. Técnicas comunes incluyen:
- **Min-Max Normalization**: Escalar puntuaciones a un rango común (ej.: [0,1]).
- **Z-score Normalization**: Ajustar puntuaciones según la media y desviación estándar de cada algoritmo.

---

## 4. Implementaciones Reales

| Sistema               | Mecánica de Hybrid Search                     | Parámetros Clave                          | Ejemplo de Uso                        |
|----------------------|-----------------------------------------------|-------------------------------------------|---------------------------------------|
| **Elasticsearch/OpenSearch** | BM25 + vector search (multi-tenant index) | `function_score` + `rank_feature`         | Búsqueda de productos en e-commerce   |
| **Weaviate**          | BM25 (alpha) + vector search                | `alpha` (0-1) para balancear ambos scores | Búsqueda de clientes B2B             |
| **Qdrant**            | Sparse vectors (BM25) + dense vectors        | `alpha` (0-1) para combinar ambos        | Búsqueda en bases de datos de clientes |
| **pgvector + pg_trgm** | Vector search + texto (trigramas)            | `similarity` (cosine) + `pg_trgm`        | Búsqueda de documentos legales        |

**Nota:** Weaviate y Qdrant usan un parámetro `alpha` para ajustar el peso entre BM25 y embeddings. Por ejemplo, `alpha=0.5` equilibra ambos enfoques.

---

## 5. Decisión de Pesos Óptimos por Dominio

### **Caso de Uso:**
- **Codigos legales exactos**: Priorizar BM25 (peso alto) para garantizar coincidencias exactas.
- **Preguntas conceptuales (RRHH)**: Priorizar embeddings (peso alto) para comprensión semántica.

### **Métrica de Evaluación:**
- **Recall@k**: Medir la proporción de resultados relevantes en los primeros k resultados.
- **A/B Testing**: Comparar variantes de pesos (ej.: alpha=0.3 vs alpha=0.7) en conjuntos de prueba.

**Tabla de Pesos por Caso de Uso:**

| Dominio               | Peso BM25 | Peso Embeddings | Justificación                              |
|-----------------------|-----------|------------------|---------------------------------------------|
| Codigos legales       | 0.8       | 0.2              | Exact matches son críticos                 |
| Preguntas de RRHH     | 0.3       | 0.7              | Comprensión semántica para conceptos abstractos |
| Productos e-commerce  | 0.6       | 0.4              | Balance entre exactitud y relevancia       |

---

## 6. Problemas de Producción

### **A. Coste Computacional**
- **Dos índices**: Mantener índices BM25 y vectorizados aumenta el espacio en disco y la complejidad de gestión.
- **Solución**: Usar sistemas multi-tenant (ej.: Elasticsearch) que soporten múltiples índices en una sola instancia.

### **B. Latencia Adicional**
- **Fusión de resultados**: El proceso de combinación de puntuaciones añade tiempo de procesamiento.
- **Optimización**: Priorizar algoritmos de fusión eficientes (ej.: RRF) y minimizar el número de documentos procesados.

### **C. Sincronización de Indices**
- **Consistencia**: Cambios en los documentos deben reflejarse en ambos índices.
- **Solución**: Implementar sistemas de sincronización en tiempo real o batch, usando herramientas como Kafka o cron jobs.

---

## Resumen

El retrieval híbrido combina la eficiencia de BM25 con la semántica de embeddings, ofreciendo un equilibrio entre exactitud y cobertura. La elección de algoritmos de fusión (RRF, weighted scores) y el ajuste de pesos por dominio son clave para optimizar resultados. Sin embargo, la implementación en producción implica desafíos como el coste computacional y la sincronización de índices. Este enfoque prepara el terreno para el **reranking**, donde los resultados finales se refinan mediante modelos de machine learning, completando el ciclo de búsqueda híbrida.