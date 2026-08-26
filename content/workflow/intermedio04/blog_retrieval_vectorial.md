# Retrieval Vectorial: La Búsqueda Semántica en Acción  

**Artículo #4 del bloque "Intermedio" de la serie "RAG: De la teoría a la práctica"**  

---

## Introducción: De los embeddings a la búsqueda semántica  

En la serie de artículos sobre Retrieval-Augmented Generation (RAG), los artículos anteriores han abordado la preparación de datos, el chunking inteligente y la generación de embeddings. Ahora, en el cuarto paso de esta serie, exploramos cómo estos embeddings se convierten en la base de una búsqueda semántica eficaz. La *búsqueda vectorial* es la técnica que permite a los modelos de RAG encontrar información relevante en documentos no estructurados, basándose en la similitud semántica en lugar de coincidencias exactas de palabras.  

Este enfoque es especialmente útil en contextos B2B como el legal, RRHH o soporte técnico, donde las consultas suelen ser complejas, con términos técnicos, sinónimos raras o referencias a documentos específicos. La búsqueda vectorial transforma los textos en vectores numéricos (embeddings) y compara estos vectores para identificar contenido similar, sin depender de la coincidencia léxica.  

---

## ¿Qué es la búsqueda vectorial y su conexión con los embeddings?  

Los *embeddings* son representaciones numéricas de texto que capturan su significado en un espacio multidimensional. En el artículo anterior sobre embeddings, vimos cómo estos vectores se generan mediante modelos como BERT o Sentence Transformers, que mapean palabras, frases o documentos en vectores de dimensión fija.  

La *búsqueda vectorial* se basa en el principio de que textos similares tendrán vectores cercanos en este espacio. Por ejemplo, las frases "El contrato se firma en papel" y "El acuerdo se firma físicamente" tendrían embeddings muy similares, incluso si no comparten palabras exactas.  

Este enfoque se conecta directamente con los embeddings: al transformar documentos en vectores, se puede construir un índice vectorial que permita buscar documentos mediante la similitud entre sus representaciones. En RAG, este índice se combina con modelos de generación para ofrecer respuestas contextualizadas.  

---

## Similitud coseno vs. distancia euclídea: ¿Cuál es mejor?  

La eficacia de la búsqueda vectorial depende de la métrica de similitud elegida. Dos opciones comunes son la **similitud coseno** y la **distancia euclídea**.  

- **Similitud coseno**: Mide el ángulo entre dos vectores, ignorando su magnitud. Es ideal para comparar la orientación de los vectores, lo que refleja la similitud semántica. Por ejemplo, dos frases con el mismo significado, pero diferentes longitudes, obtendrían una alta similitud coseno.  
- **Distancia euclídea**: Mide la distancia directa entre vectores, lo que puede capturar diferencias en la magnitud. Es más sensible a variaciones en la intensidad de las palabras, pero menos efectiva para comparar significados abstractos.  

En RAG, la similitud coseno es más común, ya que prioriza la semántica sobre la forma exacta. Sin embargo, la elección entre ambas métricas puede depender del dominio: por ejemplo, en documentos técnicos con términos específicos, la distancia euclídea podría mejorar la precisión en ciertos casos.  

---

## Optimizando los parámetros de búsqueda vectorial  

La efectividad de la búsqueda vectorial no se limita a la métrica de similitud, sino también a la configuración de parámetros críticos:  

1. **Top-k**: El número de resultados que se devuelven. Un top-k elevado puede mejorar la cobertura, pero también aumenta el riesgo de incluir información irrelevante. En entornos B2B, un top-k de 5-10 suele equilibrar precisión y eficiencia.  
2. **Umbral de similitud**: Un valor mínimo que los resultados deben superar para ser considerados relevantes. Por ejemplo, un umbral de 0.7 en similitud coseno asegura que solo los resultados más cercanos se incluyan.  
3. **Normalización**: Ajustar los vectores para evitar sesgos en la métrica (como la longitud de los documentos).  

Optimizar estos parámetros requiere experimentación. En un caso práctico, ajustar el top-k de 10 a 5 y el umbral de 0.7 a 0.8 puede mejorar la precisión en consultas sobre políticas legales, reduciendo resultados irrelevantes.  

---

## Limitaciones de la búsqueda puramente vectorial  

Aunque la búsqueda vectorial es poderosa, tiene limitaciones en contextos donde la coincidencia léxica exacta es crítica:  

- **Falta de coincidencia exacta**: Si una consulta contiene un término raro o un sinónimo no común, los embeddings pueden no capturar su relación. Por ejemplo, una consulta sobre "contratos de no competencia" podría no encontrar documentos que usen "acuerdos de no competencia".  
- **Nombres propios y términos específicos**: Personas, marcas o términos técnicos pueden no ser detectados correctamente si no están presentes en el corpus de entrenamiento.  
- **Sensibilidad a la ambigüedad**: Frases con múltiples significados (como "código" en un contexto legal vs. técnico) pueden generar resultados imprecisos.  

Estas limitaciones destacan la necesidad de complementar la búsqueda vectorial con métodos basados en palabras, como BM25, para cubrir casos donde la semántica no es suficiente.  

---

## Ejemplo práctico: Búsqueda en documentos legales  

Imagina una consulta sobre "requisitos para la validez de un contrato digital". El modelo de RAG transforma esta consulta en un vector y compara con el índice de documentos. Los top-k=5 resultados más relevantes podrían incluir:  

1. "Reglas de firma electrónica en el GDPR"  
2. "Estructura de un contrato válido en el derecho civil"  
3. "Certificaciones de firma digital en el sector financiero"  
4. "Condiciones de validez de contratos en la UE"  
5. "Casos de contratos digitales inválidos"  

Este ejemplo ilustra cómo la búsqueda vectorial prioriza la semántica, incluso si las palabras no coinciden exactamente.  

---

## Conclusión: ¿Qué sigue?  

La búsqueda vectorial es un pilar fundamental de RAG, pero no es infalible. En el próximo artículo de la serie, exploraremos cómo combinar la búsqueda vectorial con métodos basados en palabras (como BM25) para crear un enfoque híbrido que equilibre precisión y cobertura. Este enfoque será clave para resolver casos complejos donde la semántica y la coincidencia léxica son ambos necesarios.  

¿Listos para descubrir cómo la hibridación puede elevar la eficacia de los sistemas RAG? ¡Sigue leyendo!