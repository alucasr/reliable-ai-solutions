# Reliable AI Solutions — Estado de la empresa

> Documento vivo de inventario y decisiones. Actualizar cada vez que cambie algo relevante (dominios, email, decisiones de producto/SEO, etc.)

## Estado legal
- Empresa **aún no constituida legalmente** — fase de validación de mercado (PoC).

## Dominios registrados

| Dominio | Registrador | Estado | Uso | Email (MX) |
|---|---|---|---|---|
| **reliableai.solutions** | Porkbun | Activo, desplegado (Cloudflare Pages) | Landing "vibrante" — candidato a **hub de contenido/blog** (más brandeable) | No configurado |
| **smartgenai.solutions** | Porkbun | Activo, desplegado (Cloudflare Pages) | Landing "corporativa" | No configurado |
| **aireliable.solutions** | Porkbun | Activo, desplegado (Cloudflare Pages) | Landing "dark/tech" | No configurado |
| **reliablesolutions.ai** | Namecheap | Registrado, **aparcado a proposito** (sin tocar nameservers) | Sin uso nuevo definido | Tenia MX/hosting PREVIO activo (ver nota) |

Los 4 se registraron en fechas muy próximas entre sí (posiblemente el mismo día), como parte de la fase de brainstorming inicial del proyecto.

**Nota sobre reliablesolutions.ai (recuperado de conversacion, 17-ago-2026)**: este dominio YA TENIA hosting activo en Namecheap (email "Your Hosting Account Details for reliablesolutions.ai") con 3 registros MX propios detectados por Cloudflare. Por eso se decidio dejarlo **sin tocar nameservers** en vez de migrarlo a Cloudflare como los otros 3 — para no arriesgar romper el hosting/correo que ya tenia configurado. Se presentaron 2 opciones al usuario (mover tambien a Cloudflare ya que los registros estaban copiados, o dejarlo tal cual) — **no consta que se cerrara la decision final** en el historico revisado; sigue pendiente de confirmar.

## Registro Mercantil Central (RMC) — Denominacion Social

Tramite realizado antes de comprar los dominios, para verificar/reservar el nombre legal de la empresa.

| Fecha | Evento | Referencia |
|---|---|---|
| 03-mar-2026 20:28 | Consulta "RELIABLE SOLUTIONS" -> NO disponible | 260303200037 |
| 03-mar-2026 20:58 | Consulta "AI RELIABLE SOLUTIONS" -> Disponible | 260303200996 |
| 03-mar-2026 21:26 | Solicitud formal de Certificacion de Denominacion Social | 5395974 |
| 04-mar-2026 14:01 | Certificacion emitida: "AI RELIABLE SOLUTIONS, SOCIEDAD LIMITADA" | Cert. 26044948 |

- Emails de origen: `no-reply@rmc.es` (sin enlace publico compartible, notificaciones directas a alucasrio@gmail.com). IDs Gmail: 49934, 49935, 49936, 49944 (este ultimo con los 2 PDFs adjuntos: certificado + factura).
- Validez legal de la certificacion: 6 meses desde emision (venceria ~04-sep-2026).
- Notaria (Maria Vico, Notaria Isabel Cobos, San Fernando de Henares) pidio el certificado el 26-mar-2026 para constitucion — pendiente verificar si la constitucion se completo dentro de plazo.
- **12-sep-2026**: certificado confirmado caducado. Segun rmc.es/privado/CertificacionesDenominaciones.aspx, el procedimiento es: (1) nueva solicitud en la web con nuevo numero de referencia, (2) enviar email a solicitudes@rmc.es adjuntando el certificado caducado + nuevo numero de referencia. Se preparo borrador en Gmail (Borradores, id 52138) con los 2 PDFs originales adjuntos y el texto explicativo, pendiente de completar con el nuevo numero de referencia antes de enviar.
- **Usuario ya tramito la nueva solicitud en la web (12-sep-2026)** — pendiente respuesta del RMC. Seguimiento: tarea HP en curso, a la espera.

Nota de precio: Porkbun avisó (email 6-sep-2026) de subida de precio de renovación .solutions a **$31.41/año** a partir del 6-oct-2026 — revisar si renovar antes o dejar caducar alguno si no se usa.

## Formularios de contacto
Los 3 dominios de Cloudflare comparten el mismo endpoint hash de FormSubmit.co (`2c63d5b3755b7c7d0cbf6ee6e8c4d9ef`), pero **cada dominio requiere activación individual la primera vez que se envía desde él** (llega un email "Action Required: Activate FormSubmit").

Estado de activación (a 12-sep-2026):
- ✅ **aireliable.solutions**: activado (llegó email de activación 12-sep 14:03, y ya se han recibido envíos reales después)
- ❓ **reliableai.solutions**: sin confirmar si está activado (no se ha probado ni ha llegado email de activación)
- ❓ **smartgenai.solutions**: sin confirmar si está activado (no se ha probado ni ha llegado email de activación)
- N/A **reliablesolutions.ai**: aparcado, sin formulario desplegado

**Pendiente**: probar el formulario de reliableai.solutions y smartgenai.solutions (requiere confirmación explícita del usuario antes de hacer envíos de prueba, ya que genera acciones externas irreversibles tipo email).

## Decisiones de estrategia SEO (20-ago-2026)
Fuente: plan enviado por email "(Hermes) Plan de posicionamiento SEO - 4 webs Reliable AI".

- **Rechazado**: red de enlaces PBN (interlinking recíproco con desfase temporal) entre los 4 sites — Google lo detecta por patrón de propiedad común, no solo por timing; genera riesgo de penalización y deuda técnica.
- **Decisión adoptada**: concentrar el blog/contenido de autoridad en **un solo dominio hub** en vez de repartirlo en los 4, para evitar canibalización de keywords.
- **Hub recomendado**: `reliableai.solutions` (nombre más genérico/brandeable).
- Los otros 2 sites activos (smartgenai, aireliable) quedan como **landings de conversión** con ángulos/segmentos de venta distintos, con contenido tipo FAQ/casos de uso corto actualizado cada 2 semanas, sin blog paralelo propio.
- Contenido prioritario: RAG aplicado a empresas (casos de uso, comparativas, guías prácticas) — NO trivia/curiosidades de IA (bajo valor comercial, no genera leads cualificados).
- Calendario editorial inicial (6 posts, hub): qué es RAG y por qué tu empresa lo necesita → RAG vs fine-tuning → cómo preparar documentos para RAG → seguridad/privacidad → casos de uso por sector → coste real RAG vs alternativas.
- **Pendiente de decisión del usuario**: confirmación final del dominio hub antes de estructurar el blog en el repo.

## Producto — opciones de servicio (definidas 12-sep-2026)
Para propuestas comerciales (ver borrador de respuesta a leads del formulario):

1. **RAG** — consulta en tiempo real a la documentación, sin reentrenar. Opción más rápida/económica.
2. **Agentic RAG** — evolución del RAG con agente que planifica/itera/refina antes de responder. Más robusto en consultas complejas, más coste computacional. Término estándar de industria confirmado a fecha 2026.
3. **Fine-tuning** — reentrenar el modelo con datos propios. Opción menos habitual por el salto de coste: requiere alojar y mantener el modelo propio, y reentrenamiento periódico recurrente (no es coste único). Tiene sentido con conocimiento muy estable y alto volumen de uso.

Todas las opciones desplegables en **local** (modelos propios/terceros open-weight, máximo control de datos) o **cloud** (modelos propios o de OpenAI/Anthropic/Google, menor coste inicial).

Idiomas soportados actualmente: **inglés y español**.

## Leads recibidos (formulario)
- **12-sep-2026**: Brian (madamtaisia@mail.ru), vía aireliable.solutions — "RAG fiable sobre documentación interna". Respuesta en preparación (borrador en Gmail, carpeta Borradores).

## Email corporativo
**Ningún dominio tiene email propio configurado todavía** (sin MX en ninguno de los 3 de Cloudflare; reliablesolutions.ai aparcado).

Necesidad identificada (12-sep-2026): solución que permita **recibir desde varios dominios** y **enviar con varias direcciones** (genéricas tipo contacto@, por departamento, nominales) **y varios dominios de origen** (no solo uno).

Opciones evaluadas:
- **Google Workspace**: ~6€/usuario/mes, soporta múltiples dominios (alias de dominio) y múltiples direcciones de envío/recepción bajo una misma bandeja Gmail. Integración directa con el flujo actual (himalaya/IMAP/SMTP).
- **Cloudflare Email Routing** (gratis): solo reenvío de recepción a Gmail, no permite enviar nativamente con esas direcciones sin truco adicional ("Enviar como" en Gmail).
- **Zoho Mail / Migadu / Proton Business**: alternativas de pago más económicas, multi-dominio, requieren configurar MX/SPF/DKIM en Cloudflare igualmente.

**Pendiente de decisión**: elegir proveedor definitivo para email multi-dominio/multi-dirección.
