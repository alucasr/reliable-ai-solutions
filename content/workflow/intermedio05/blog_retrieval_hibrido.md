# Retrieval Hibrido: Combinando lo Mejor de Dos Mundos  

En la serie de artículos sobre Retrieval-Augmented Generation (RAG), hemos explorado cómo la búsqueda vectorial puede transformar la forma en que los sistemas de IA acceden a información interna. Sin embargo, al profundizar en sus limitaciones, se vuelve evidente que no siempre es suficiente depender exclusivamente de la semántica para encontrar respuestas precisas. En este artículo, analizamos el **retrieval híbrido**, una estrategia que combina la eficacia de la búsqueda lexica (BM25) con la profundidad semántica de la búsqueda vectorial. Este enfoque permite a los sistemas RAG equilibrar la exactitud con la generalidad, adaptándose a los desafíos complejos de los casos de uso B2B.  

---

## ¿Qué es el retrieval híbrido y por qué surge como respuesta a las limitaciones del retrieval puramente vectorial?  

La búsqueda vectorial, aunque potente, tiene ciertas limitaciones. Por ejemplo, en documentos con términos específicos (como códigos legales, identificadores de empleados o cláusulas técnicas), la semántica puede fallar al no capturar exactitud. Además, en dominios con vocabulario técnico denso, los modelos pueden perder relevancia al ignorar términos clave que no están bien representados en los embeddings.  

Estas brechas se agravan cuando se requiere un equilibrio entre **precisión** (encontrar exactamente lo que se busca) y **recuperación** (encontrar información relacionada, incluso si no es idéntica). El **retrieval híbrido** resuelve este dilema al integrar dos métodos de búsqueda:  
1. **Búsqueda lexica (BM25)**: Basada en coincidencias exactas de términos, ideal para consultas con palabras clave específicas.  
2. **Búsqueda vectorial (semántica)**: Basada en la similitud entre vectores, capaz de capturar relaciones conceptuales.  

Al combinar ambos, el sistema puede priorizar resultados precisos para consultas exactas y ampliar su alcance para consultas más abstractas.  

---

## ¿Cómo combinar búsqueda lexica (BM25) con búsqueda vectorial (semántica)?  

La integración de ambos métodos requiere un enfoque estructurado. Aquí hay dos estrategias comunes:  

### 1. **Búsqueda híbrida por etapas**  
En esta aproximación, se realizan dos consultas separadas:  
- La primera, usando BM25, identifica documentos que contienen términos exactos.  
- La segunda, usando búsqueda vectorial, encuentra documentos semánticamente relacionados.  
Los resultados se combinan, y se aplican criterios de filtrado (como umbral de relevancia) para evitar redundancias.  

### 2. **Búsqueda híbrida en tiempo real**  
En este caso, los modelos procesan la consulta simultáneamente con ambos métodos. Por ejemplo, un sistema podría asignar pesos a los términos de la consulta y calcular una puntuación híbrida que combine ambos enfoques.  

La elección entre estas estrategias depende del equilibrio entre latencia, complejidad y necesidad de precisión.  

---

## Métodos de fusión de resultados: Reciprocal Rank Fusion (RRF) y ponderación lineal  

Una vez obtenidos los resultados de ambos métodos, es crucial fusionarlos de manera efectiva. Dos técnicas destacadas son:  

### **Reciprocal Rank Fusion (RRF)**  
RRF combina resultados de múltiples métodos al aplicar una función que transforma las puntuaciones de cada método en una escala común. Por ejemplo, si un documento obtiene una puntuación de 0.8 en BM25 y 0.6 en vectorial, RRF calcula una puntuación híbrida que refleja el equilibrio entre ambos. Este método es especialmente útil cuando se buscan resultados que priorizan la relevancia general.  

### **Ponderación lineal de scores**  
En este enfoque, se asigna un peso a cada método según su importancia en el caso de uso. Por ejemplo, si la consulta incluye términos específicos, se podría asignar un peso mayor al BM25, mientras que para consultas conceptuales, se prioriza la búsqueda vectorial. La fórmula general es:  
$$
\text{Score\_hibrido} = w_1 \times \text{BM25} + w_2 \times \text{Vectorial}
$$  
Donde $w_1 + w_2 = 1$.  

La elección entre RRF y ponderación lineal depende de si se busca una combinación equilibrada (RRF) o un enfoque más flexible (ponderación lineal).  

---

## ¿Cómo equilibrar la importancia relativa de cada método según el caso de uso?  

La eficacia del retrieval híbrido depende de la configuración adecuada de sus componentes. Aquí hay ejemplos de cómo ajustar la prioridad según el contexto:  

1. **Casos con nombres propios o códigos exactos**:  
   - Ejemplo: Una consulta sobre el "código de cumplimiento fiscal 2023" requiere coincidencias exactas.  
   - Enfoque: Priorizar BM25 con un peso alto, y usar vectorial como complemento para términos relacionados.  

2. **Casos con consultas conceptuales**:  
   - Ejemplo: Una pregunta sobre "responsabilidades de los empleados en proyectos internacionales" no incluye términos específicos.  
   - Enfoque: Priorizar búsqueda vectorial, complementada con BM25 para términos clave como "responsabilidades" o "empleados".  

3. **Casos con ambigüedad**:  
   - Ejemplo: Una consulta general como "¿qué dice el acuerdo de confidencialidad?" puede beneficiarse de ambos métodos para cubrir tanto coincidencias exactas como contextos semánticos.  

La clave es probar y ajustar los pesos en función de métricas como la precisión o la recall, asegurando que el sistema responda eficazmente a las necesidades del usuario.  

---

## Ejemplo práctico: ¿Por qué el retrieval híbrido rescata resultados que el vectorial puro se pierde?  

Imagina una consulta en un sistema de soporte técnico:  
**"¿Qué pasa si un usuario no cumple con el código de uso de la API 1.2.3?"**  

Un sistema basado únicamente en búsqueda vectorial podría ignorar el término "código de uso de la API 1.2.3" si no está bien representado en los embeddings. Sin embargo, un enfoque híbrido:  
1. Usa BM25 para identificar documentos que contengan "código de uso de la API 1.2.3".  
2. Usa búsqueda vectorial para encontrar documentos relacionados con "incumplimiento de acuerdos" o "sanciones por no cumplir".  

Este enfoque asegura que el usuario reciba información precisa sobre el código específico, además de contexto sobre las consecuencias del incumplimiento.  

---

## ¿Qué viene después?  

El retrieval híbrido es un paso crucial para optimizar la calidad de las respuestas en sistemas RAG, pero no es el fin. En el próximo artículo de la serie, exploraremos cómo **re-ranking** (reordenamiento de resultados) puede refinar aún más la relevancia, priorizando documentos que no solo coinciden, sino que también son más pertinentes al contexto del usuario.  

¿Listos para descubrir cómo el re-ranking eleva la precisión de tus respuestas RAG? ¡Sigue leyendo!