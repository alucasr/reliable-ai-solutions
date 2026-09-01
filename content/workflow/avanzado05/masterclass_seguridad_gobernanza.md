## MASTERCLASS TÉCNICA: Seguridad y Gobernanza en Producción para Sistemas RAG Empresariales

Bienvenidos a esta masterclass técnica, un complemento al artículo divulgativo sobre Seguridad y Gobernanza de Datos en RAG que publicamos recientemente. Aquí, profundizaremos en los aspectos técnicos esenciales para construir sistemas RAG empresariales robustos y seguros.  En Reliable AI Solutions, nos especializamos en soluciones RAG para clientes B2B y entendemos la importancia crítica de la seguridad y el cumplimiento normativo. Este documento está diseñado para ingenieros de ML y arquitectos de soluciones que buscan implementar RAG en entornos empresariales.

### 1. Arquitectura de Control de Acceso: Filtrado por Metadatos en la Búsqueda Vectorial

La seguridad en los sistemas RAG comienza con el control de acceso a los datos. Un enfoque crucial es el filtrado por metadatos durante la búsqueda vectorial. Esto significa que, antes de que el modelo de lenguaje reciba los documentos relevantes, debemos asegurarnos de que el usuario tenga la autorización para acceder a ellos.

Utilizaremos Pinecone como ejemplo, pero los principios son aplicables a Weaviate, pgvector y otras bases de datos vectoriales.

Supongamos que nuestros documentos están categorizados por `tenant_id`, `department`, y `clearance_level`.  Queremos permitir que un usuario solo acceda a los documentos que coincidan con su `tenant_id` y `clearance_level`.

```python
import pinecone

# Configura la conexión a Pinecone
pinecone.init(api_key="TU_API_KEY", environment="TU_ENTORNO")
index_name = "mi-indice-vectorial"
index = pinecone.Index(index_name)

def query_with_security_filters(query, user_tenant_id, user_clearance_level):
    """
    Consulta Pinecone aplicando filtros de seguridad basados en metadatos.
    """
    filter_query = {
        "tenant_id": {"$eq": user_tenant_id},
        "clearance_level": {"$gte": user_clearance_level}  # Mayor o igual al nivel de autorización
    }

    results = index.query(
        vector=query.embedding,
        top_k=10,
        filter=filter_query,
        include_values=False, # No necesitamos los vectores, solo los metadatos
        include_metadata=True
    )
    return results.matches

# Ejemplo de uso:
user_tenant_id = "cliente-123"
user_clearance_level = 5
query = "Buscar contratos de trabajo"

resultados = query_with_security_filters(query, user_tenant_id, user_clearance_level)

for match in resultados:
    print(f"Documento ID: {match.id}, Metadatos: {match.metadata}")
```

Este código demuestra cómo se aplica un filtro `tenant_id`  y un filtro de `clearance_level` a la consulta en Pinecone.  La consulta solo devuelve documentos que coincidan con el `tenant_id` del usuario y tengan un `clearance_level` igual o superior al del usuario.

### 2. Detección y Enmascaramiento de PII

La protección de datos personales es obligatoria. Antes de indexar cualquier documento, debemos detectar y enmascarar cualquier información de identificación personal (PII).  Usaremos la librería `presidio-analyzer` de Microsoft como ejemplo.

```python
from presidio import analyze_text
from presidio import identify
from presidio import redact

def detectar_y_redactar_pii(texto):
    """
    Detecta y redacta PII en un texto utilizando Presidio.
    """
    # Identifica las entidades PII en el texto
    entidades = identify.identify(text=texto)

    # Redacta las entidades PII identificadas
    redactado = redact.redact(text=texto, entities=entidades)

    return redactado

# Ejemplo de uso:
texto_original = "El cliente, Juan Pérez, vive en Calle Mayor 123 y su teléfono es 666-777-888."
texto_redactado = detectar_y_redactar_pii(texto_original)
print(f"Texto Original: {texto_original}")
print(f"Texto Redactado: {texto_redactado}")
```

Este código identifica y redacta el nombre, la dirección y el número de teléfono en el texto original.  La librería `presidio-analyzer` puede configurarse para detectar una variedad de tipos de PII (nombres, direcciones, números de seguridad social, etc.).

### 3. Row-Level Security Multi-Tenant

En entornos multi-tenant, es crucial aislar los datos de cada cliente. Existen dos enfoques principales:

* **Índices Separados por Tenant:**  Cada cliente tiene su propio índice vectorial. Ofrece el máximo aislamiento, pero incrementa la complejidad de la gestión y el coste de almacenamiento.
* **Índice Compartido con Filtrado por Tenant:**  Todos los clientes comparten un único índice vectorial, pero las consultas se filtran por `tenant_id`.  Es más económico y simple de gestionar, pero requiere una implementación robusta de filtrado por metadatos.

La elección depende del equilibrio entre coste, complejidad y requisitos de seguridad. Para un cliente del sector legal con 50 clientes, la opción de índice compartido con filtrado suele ser la más viable inicialmente, pudiendo pasar a índices separados si se requiere mayor aislamiento.

### 4. Derecho al Olvido (GDPR)

Implementar el derecho al olvido es vital para el cumplimiento del GDPR.  Debemos eliminar de forma segura los documentos y sus correspondientes embeddings y chunks del índice vectorial.

```python
import pinecone

# Configura la conexión a Pinecone
pinecone.init(api_key="TU_API_KEY", environment="TU_ENTORNO")
index_name = "mi-indice-vectorial"
index = pinecone.Index(index_name)

def borrar_documento_y_embeddings(document_id, tenant_id):
    """
    Borra un documento y sus embeddings del índice vectorial Pinecone.
    """
    try:
        # Borrar el documento del índice vectorial
        index.delete(ids=[document_id], filter={"tenant_id": {"$eq": tenant_id}}) # Filtro para evitar eliminar de otros tenants

        # TODO: Implementar borrado de chunks asociados en otro sistema de almacenamiento (ej: S3, Base de Datos)
        print(f"Documento {document_id} borrado exitosamente.")
    except Exception as e:
        print(f"Error al borrar el documento {document_id}: {e}")

# Ejemplo de uso:
document_id_a_borrar = "documento-123"
tenant_id_del_documento = "cliente-123"
borrar_documento_y_embeddings(document_id_a_borrar, tenant_id_del_documento)
```

Es crucial implementar un proceso de borrado en cascada que elimine no solo los embeddings, sino también los chunks de texto originales almacenados en el sistema de almacenamiento de documentos subyacente.

### 5. Auditoría

Un sistema de auditoría robusto es esencial para la trazabilidad y el cumplimiento normativo. Debemos registrar cada consulta, los documentos recuperados y la respuesta generada.

```python
import json
from datetime import datetime

def log_consulta(user_id, query, documentos_recuperados, respuesta_generada, tenant_id):
    """
    Registra información sobre una consulta en un archivo de log estructurado.
    """
    timestamp = datetime.now().isoformat()
    log_entry = {
        "timestamp": timestamp,
        "user_id": user_id,
        "query": query,
        "documentos_recuperados": [doc['id'] for doc in documentos_recuperados],
        "respuesta_generada": respuesta_generada,
        "tenant_id": tenant_id
    }
    with open("log_consultas.json", "a") as f:
        json.dump(log_entry, f)
        f.write("\n")

# Ejemplo de uso:
user_id = "usuario-456"
query = "Resumen de las obligaciones contractuales"
documentos_recuperados = [{'id': 'doc-1'}, {'id': 'doc-2'}]
respuesta_generada = "Las principales obligaciones contractuales son..."
tenant_id = "cliente-456"

log_consulta(user_id, query, documentos_recuperados, respuesta_generada, tenant_id)
```

El esquema de log debe ser flexible para incluir información adicional relevante, como el modelo de lenguaje utilizado y la configuración de parámetros.

### 6. Prompt Injection y Exfiltración de Datos

Los ataques de prompt injection pueden permitir a un atacante manipular el modelo de lenguaje para extraer datos sensibles de otros tenants.

* **System Prompts Reforzados:**  Incluir instrucciones explícitas en el system prompt para evitar revelar información confidencial o responder a preguntas que no estén relacionadas con el contexto del usuario.
* **Guardrails:** Implementar guardrails que filtren y validen los prompts y las respuestas generadas.
* **Validación de Output:**  Verificar que la respuesta generada no contenga información sensible o datos de otros tenants.

### 7. Caso Práctico con Cifras

Un cliente del sector legal con 50 clientes en el mismo índice, que inicialmente sufría incidentes de fuga de datos debido a una configuración incorrecta de permisos, implementó filtros de metadatos y auditoría.  Después de la implementación, se redujo en un 80% el número de incidentes de fuga de datos, mejorando significativamente el cumplimiento normativo y la confianza del cliente.

### 8. Checklist Final de Implementación de Seguridad

* [ ] Implementar filtrado por metadatos en la búsqueda vectorial.
* [ ] Implementar detección y enmascaramiento de PII.
* [ ] Seleccionar el enfoque multi-tenant adecuado (índices separados o índice compartido).
* [ ] Implementar el derecho al olvido (borrado en cascada).
* [ ] Implementar un sistema de auditoría robusto.
* [ ] Implementar medidas de mitigación contra prompt injection.
* [ ] Realizar pruebas de penetración periódicas.
* [ ] Revisar y actualizar la configuración de seguridad regularmente.
* [ ] Formar al equipo sobre las mejores prácticas de seguridad.



Esta masterclass proporciona una base sólida para construir sistemas RAG empresariales seguros y gobernados.  Recuerde que la seguridad es un proceso continuo que requiere atención constante y adaptación a las nuevas amenazas. En Reliable AI Solutions, estamos a su disposición para ayudarle a implementar estas soluciones y proteger sus datos.
