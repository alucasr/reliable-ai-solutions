# Reliable AI Solutions — Estado de la empresa

> Documento vivo de inventario y decisiones. Actualizar cada vez que cambie algo relevante (dominios, email, decisiones de producto/SEO, etc.)

## Estado legal
- Empresa **aún no constituida legalmente** — fase de validación de mercado (PoC).

## Dominios registrados

| Dominio | Registrador | Coste registro (IVA incl.) | Estado | Uso | Email (MX) |
|---|---|---|---|---|---|
| **reliableai.solutions** | **EuroDNS S.A.** (Luxemburgo) | 13,30€/año (10,99€ + IVA 21%: incluye 5,99€ tasa .solutions con descuento + 5€ privacidad WHOIS) — factura E-1791988, pedido 21470085, 3-mar-2026 | Migrado a cPanel (13-sep-2026) | Landing "vibrante" — candidato a **hub de contenido/blog** | No configurado |
| **smartgenai.solutions** | **EuroDNS S.A.** (Luxemburgo) | 13,30€/año (mismo desglose) — factura E-1791987, pedido 21470084, 3-mar-2026 | Migrado a cPanel (13-sep-2026) | Landing "corporativa" | No configurado |
| **aireliable.solutions** | **Porkbun** | $3.60/año (registro simple, sin privacidad WHOIS aparte) — orden 9675457, 4-mar-2026 | Sigue en Cloudflare Pages (bloqueo LaLiga activo) | Landing "dark/tech" | No configurado |
| **reliablesolutions.ai** | Namecheap | No verificado en este repaso | Registrado, aparcado a proposito (sin tocar nameservers) | Hosting reutilizado para la migracion de los otros 2 | Tenia MX/hosting PREVIO activo (ver nota) |

Los 4 se registraron en fechas muy próximas (2-4 marzo 2026), como parte de la fase de brainstorming inicial del proyecto — pero en **2 registradores distintos** (EuroDNS para los 2 primeros, Porkbun para aireliable), probablemente por comparar precios/promos del momento.

**Verificación del registrador**: confirmado vía [ICANN Lookup](https://lookup.icann.org/en/lookup) (RDAP), que muestra el registrador oficial IANA de cada dominio directamente desde el registry (más fiable que asumir por el email de bienvenida). Tambien util: `dig`, `whois`, y el panel de "Registration" en Cloudflare (aunque este ultimo solo aplica si el dominio esta transferido a Cloudflare Registrar, que no es el caso aqui).

## Estado de registros DNS (Cloudflare, gestor de DNS de los 3 .solutions)

| Dominio | Registros DNS | Proxy | Notas |
|---|---|---|---|
| reliableai.solutions | A (@) → 198.177.120.192; TXT (_simpledcver, validacion ya usada) | DNS only (gris) | 2 de 200 registros usados. Sin `www`, sin MX (recomendaciones de Cloudflare pendientes si se quiere email o subdominio www) |
| smartgenai.solutions | A (@) → 198.177.120.192; TXT (_simpledcver) | DNS only (gris) | 2 de 200 registros usados. Mismas recomendaciones pendientes (www, MX) |
| aireliable.solutions | 1 registro tipo "Worker" (ruta bloqueada/candado) → proyecto Pages `dry-king-4e0e` | **Proxied (naranja)** — esta es la causa del bloqueo LaLiga | Unico registro, gestionado desde Cloudflare Pages, no editable directamente en DNS Records |



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

## 🌐 Cómo funciona la resolución de la URL (para no técnicos)

Cuando alguien escribe `reliableai.solutions` en el navegador, ocurre esta cadena:

1. **Registrador** (EuroDNS/Porkbun/Namecheap): es solo el "propietario administrativo" del nombre — quién puede modificarlo, renovarlo, etc. NO decide dónde vive la web.
2. **Nameservers** (delegados desde el registrador hacia Cloudflare: `bowen.ns.cloudflare.com` / `sarah.ns.cloudflare.com`): le dicen a internet "para saber la configuración de este dominio, pregunta a Cloudflare". Este cambio de nameservers se hizo el 18-ago-2026 (ver historial más abajo) y es gratuito.
3. **Registros DNS en Cloudflare** (la "libreta de direcciones" del dominio): el registro **A** (`reliableai.solutions → 198.177.120.192`) le dice al navegador la IP exacta del servidor a contactar. Antes apuntaba (de forma oculta, vía un "Worker route") a la infraestructura compartida de Cloudflare Pages; ahora apunta directo al hosting cPanel de Namecheap.
4. **Proxy (nube naranja/gris)**: con nube **naranja** (proxied), el tráfico pasa primero por los servidores de Cloudflare (de ahí el problema de IP compartida y el bloqueo LaLiga). Con nube **gris** (DNS only, lo que se configuró en la migración), el navegador va DIRECTO a la IP del hosting — Cloudflare solo actúa de "listín telefónico", no de intermediario.
5. **Servidor de hosting** (cPanel, IP 198.177.120.192): recibe la petición HTTP(S), busca la carpeta correspondiente al dominio (`/home/reliudxl/reliableai.solutions/`) y devuelve el `index.html`.

En resumen: **registrador = dueño del nombre**, **nameservers = a quién preguntar**, **registro DNS = la dirección real**, **proxy = si hay intermediario o no**.

## 📧 Email: estado actual y configuración necesaria para evitarlo ir a spam

**Estado actual (13-sep-2026): ningún dominio `.solutions` tiene email configurado.** Se verificó en Cloudflare → Email Routing en los 3 dominios: sin actividad, sin registros MX. Cualquier email dirigido a `@reliableai.solutions`, `@smartgenai.solutions` o `@aireliable.solutions` rebota (no llega a ningún sitio).

Si en el futuro se quiere recibir/enviar correo desde estos dominios (ej. `contacto@reliableai.solutions`), hacen falta estos registros DNS, en este orden de importancia:

1. **MX (Mail Exchanger)**: dice a quién entregar el correo entrante. Con **Cloudflare Email Routing** (gratis) se puede reenviar automáticamente a un Gmail existente sin pagar un buzón nuevo — Cloudflare genera el MX automáticamente al activarlo.
2. **SPF (Sender Policy Framework)**: registro TXT que lista qué servidores tienen permiso de enviar correo "en nombre" del dominio. Sin esto, cualquiera podría enviar emails falsificando `@tudominio.solutions` y llegarían más fácilmente a la bandeja de otros — o tus propios envíos legítimos acabarán en spam.
3. **DKIM (DomainKeys Identified Mail)**: firma criptográfica en cada email saliente que demuestra que no fue alterado y que salió de un servidor autorizado. Se activa automáticamente si usas Cloudflare Email Routing + "enviar como" desde Gmail, o lo proporciona el servicio de envío que se use (ej. Google Workspace).
4. **DMARC (Domain-based Message Authentication)**: registro TXT que le dice a los servidores receptores (Gmail, Outlook, etc.) qué hacer si un email falla SPF/DKIM (rechazar, cuarentena, o nada) — y opcionalmente manda informes de intentos de suplantación a un email tuyo.

**Sin estos 4 registros bien configurados, el riesgo principal es**: (a) no recibir correo dirigido al dominio (falta MX), (b) que tus envíos legítimos caigan en spam del destinatario (falta SPF/DKIM/DMARC), y (c) que alguien pueda hacer phishing suplantando tu dominio con más facilidad (falta SPF/DMARC estrictos).

Nota: `reliablesolutions.ai` SÍ tenía MX configurado desde origen (hosting Namecheap) — pendiente de confirmar si sigue activo y si vale la pena replicar esa config en los otros 3 si se decide usarlos para correo corporativo.

## 📜 Historial: cómo se pasó del registrador original a Cloudflare (17-18 ago 2026)

Antes de la migración a cPanel (13-sep-2026, este informe), hubo un paso previo: mover la **gestión DNS** de los 3 dominios `.solutions` desde sus registradores originales hacia Cloudflare (sin cambiar el registrador en sí, solo delegando los nameservers).

- **17-ago-2026**: se creó una cuenta Cloudflare vía "Continue with Google" (`alucasrio@gmail.com`), sin contraseña nueva que recordar. Se añadieron los 4 dominios como "sitios" en Cloudflare (plan Free en los 4).
- Para `reliableai.solutions` y `smartgenai.solutions` (EuroDNS) y `aireliable.solutions` (Porkbun): se cambiaron los **nameservers** desde el panel de cada registrador hacia `bowen.ns.cloudflare.com` y `sarah.ns.cloudflare.com`. Este cambio tuvo **coste $0** (es una operación de configuración, no una compra) y Cloudflare escaneó automáticamente los registros DNS existentes antes del cambio para preservarlos.
- **18-ago-2026, 09:58-10:40 UTC**: llegaron los emails de confirmación de Cloudflare ("... is now active on Cloudflare (Free plan)") para los 3 dominios, confirmando que la propagación de nameservers se completó (tardó ~horas, no los 24-48h que se advertía como margen máximo).
- **`reliablesolutions.ai` (Namecheap)**: se añadió también a Cloudflare como "sitio", pero **deliberadamente sin cambiar sus nameservers** — se quedó gestionado 100% por Namecheap, porque ya tenía hosting activo (contratado por 2 años) y registros MX propios funcionando, y no se quiso arriesgar a romperlo. Cloudflare emitió un aviso ("[Action required] Update nameservers...") que se ignoró intencionadamente por este motivo.
- Una vez con nameservers en Cloudflare, se desplegaron las 3 landing pages en **Cloudflare Pages** (SSL automático y gratuito vía Universal SSL) — esta fue la configuración que posteriormente causó el problema de bloqueo LaLiga, resuelto en la migración a cPanel documentada en la sección siguiente.



Motivo: los 3 dominios .solutions en Cloudflare Pages sufren bloqueo de IP compartida cada partido de LaLiga. reliablesolutions.ai (IP propia en Namecheap) no sufre esto. Decision: migrar los sitios estaticos al mismo hosting cPanel que ya se paga (server706.web-hosting.com, cuenta reliudxl), como Addon/Create Domain, MANTENIENDO Cloudflare solo como gestor DNS (proxy desactivado, DNS-only) -- no se cambian nameservers.

**Resultado final:**
- ✅ **reliableai.solutions**: MIGRADO. Addon domain creado (validado via TXT `_simpledcver`), custom domain eliminado del proyecto Cloudflare Pages `shy-mud-8dc4`, registro A creado en Cloudflare (198.177.120.192, DNS-only), index.html subido y extraido en `/home/reliudxl/reliableai.solutions/`. Verificado: `curl` devuelve HTTP 200 con contenido correcto. SSL: certificado generico `*.web-hosting.com` de momento (funcional), AutoSSL propio pendiente de emitirse automaticamente.
- ✅ **smartgenai.solutions**: MIGRADO igual que el anterior (proyecto Pages `smartgenai-solutions` eliminado, A record 198.177.120.192 DNS-only, index.html en `/home/reliudxl/smartgenai.solutions/`). Verificado HTTP 200.
- ⏸️ **aireliable.solutions**: NO migrado. Bloqueante: el plan de hosting Namecheap tiene limite de **2 Addon Domains** (ya alcanzado con los 2 anteriores; ver cPanel Home -> Statistics -> Addon Domains). Usuario decidio dejarlo en Cloudflare por ahora (sigue afectado por el bloqueo LaLiga) en vez de subir de plan o usar subdominio. zip `aireliable.solutions.zip` dejado en `/home/reliudxl/` (raiz) por si se retoma.
- Usuario cPanel: reliudxl (contraseña la del email original de Namecheap "Your Hosting Account Details" -- PENDIENTE cambiarla, salio en claro en chat de Telegram varias veces durante la migracion).
- IP de destino usada para los registros A: **198.177.120.192** (misma que reliablesolutions.ai, mismo servidor).
- Guia paso a paso completa del proceso (para repetir con aireliable.solutions u otros dominios futuros): `docs/GUIA_MIGRACION_WEBS_CLOUDFLARE_A_CPANEL.md` (mismo repo).
- Screenshots del proceso: `~/Documents/Screenshots/migracion-webs/` (local, no en repo).

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
