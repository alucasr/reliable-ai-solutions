# Guía: Migrar una web de Cloudflare Pages a hosting cPanel (Namecheap)

**Objetivo**: mover un sitio estático que actualmente vive en Cloudflare Pages (IP compartida, susceptible al bloqueo "LaLigaGate" que aplican los ISPs españoles) a un hosting con IP propia, sin cambiar de dominio ni perder el sitio en producción durante la migración.

**Contexto del caso real (13-sep-2026)**: 3 dominios (`reliableai.solutions`, `smartgenai.solutions`, `aireliable.solutions`) servidos vía Cloudflare Pages sufrían bloqueos intermitentes los fines de semana con fútbol. El hosting cPanel de Namecheap (que ya aloja `reliablesolutions.ai` con IP propia) no tenía ese problema. Se migraron 2 de los 3 (el tercero quedó bloqueado por límite de plan — ver sección de errores).

## Requisitos previos
- Acceso a cPanel del hosting (usuario + contraseña — ver email "Your Hosting Account Details" de Namecheap)
- Acceso a Cloudflare (cuenta donde está gestionado el DNS del dominio)
- El fichero(s) del sitio (ej. `index.html`) comprimido en un `.zip`

## Paso 1 — Crear el "Addon Domain" en cPanel
1. Entra en cPanel → **Domains** → **Create A New Domain**
2. Escribe el dominio completo (ej. `midominio.com`) en el campo de dominio
3. Deja **"Share document root"** SIN marcar (así el sitio tiene su propia carpeta, no comparte con el dominio principal)
4. Pulsa **Submit** (o "Submit And Create Another" si vas a migrar varios seguidos)

### ⚠️ Error esperado: "pointed to remote nameservers"
Si el dominio sigue gestionado por Cloudflare (nameservers de Cloudflare, no de Namecheap), cPanel no puede verificar automáticamente que controlas el dominio y lanza este error. cPanel ofrece 2 vías de validación sin tener que cambiar nameservers:
- **TXT-based validation** (la usada aquí): añadir un registro TXT específico
- HTTP-based validation (crear un fichero en la raíz web — no usada en este caso)

**Solución (vía TXT)**:
1. En la pantalla de error, cPanel muestra el valor EXACTO a usar, con este formato:
   ```
   _simpledcver.midominio.com. 1 IN TXT "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9....."
   ```
2. Copia el valor JWT completo (todo lo que va entre comillas)
3. Ve a **Cloudflare → tu dominio → DNS → Records → Add record**
4. Tipo: **TXT**, Name: `_simpledcver`, Content: pega el JWT completo
5. **Proxy status: DNS only** (nube gris, no naranja) — es solo un TXT, no importa, pero por costumbre
6. Guarda
7. Verifica que propagó (puede tardar de segundos a ~1-2 minutos):
   ```bash
   dig @1.1.1.1 _simpledcver.midominio.com TXT +short
   ```
8. Vuelve al formulario de cPanel (puede que la sesión haya expirado y tengas que rellenar el dominio de nuevo) y pulsa Submit otra vez — debería crear el dominio sin error esta vez.

### ⚠️ Error esperado: "Has alcanzado el número máximo de dominios para esta cuenta"
Los planes de hosting básicos (ej. Namecheap Stellar) tienen un límite de **Addon Domains** (verificado: 2 en este caso). Si ya tienes el máximo, no puedes añadir más sin:
- Subir de plan (Stellar Plus/Business = ilimitados), o
- Usar un subdominio en vez de un dominio propio, o
- Dejar ese dominio en su ubicación actual

Revisa tu límite actual en cPanel → Home → **Statistics → Addon Domains** (aparece como "X / Y").

## Paso 2 — Quitar el dominio de Cloudflare Pages (liberar el DNS record)
Mientras el dominio esté como "Custom Domain" de un proyecto Cloudflare Pages/Workers, el registro DNS de la raíz (`@`) aparece como una "ruta de Worker" bloqueada (con candado) y NO se puede editar directamente desde DNS → Records.

1. Ve a **Cloudflare → Workers & Pages** (menú lateral)
2. Localiza el proyecto Pages correspondiente a tu dominio (Cloudflare le pone un nombre aleatorio tipo `shy-mud-8dc4` si nunca lo renombraste)
3. Entra al proyecto → **Settings** (o directamente la vista de detalle) → busca la sección **Domains**
4. Si la vista compacta no te deja gestionar, busca el enlace/pestaña **"Domains and routes"** (vista completa con más opciones)
5. En la fila de tu dominio custom, click en el botón **"..."** (tres puntos) → **Remove**
6. Confirma la eliminación (avisa de hasta 24h de propagación DNS, en la práctica suele ser mucho más rápido)

Nota: esto NO borra el proyecto Pages ni el contenido — solo desvincula el dominio custom. La URL `*.workers.dev` sigue funcionando de forma independiente (también sujeta al mismo bloqueo compartido, así que no sirve como alternativa).

## Paso 3 — Crear el registro DNS hacia el nuevo hosting
1. Averigua la IP del hosting (si ya tienes otro dominio en el mismo servidor, reutiliza su IP):
   ```bash
   dig otrodominio-ya-en-el-hosting.com A +short
   ```
   o el hostname del servidor cPanel (ej. `server706.web-hosting.com`):
   ```bash
   dig server706.web-hosting.com A +short
   ```
2. En **Cloudflare → tu dominio → DNS → Records → Add record**
3. Tipo: **A**, Name: **`@`** (raíz del dominio), IPv4 address: la IP del hosting
4. **Proxy status: DNS only** (nube GRIS, no naranja) — esto es CRÍTICO: si dejas el proxy naranja activado, el tráfico vuelve a pasar por la infraestructura compartida de Cloudflare y el bloqueo persiste
5. Guarda
6. Verifica propagación:
   ```bash
   dig @1.1.1.1 midominio.com A +short
   ```
   Debe devolver la IP del hosting, no la de Cloudflare.

(Opcional: añade también un registro para `www` si lo usas, mismo proceso.)

## Paso 4 — Subir el contenido del sitio
1. En cPanel, ve a **File Manager**
2. Navega a la carpeta del dominio (normalmente `/home/tuusuario/tudominio.com/`)
3. Click **Upload**, selecciona tu `.zip` con el contenido del sitio
   - En macOS, si no ves la carpeta donde está el zip (ej. `/tmp`) en el selector nativo, pulsa **`Cmd+Shift+G`** y pega la ruta absoluta
   - ⚠️ Verifica bien que estás DENTRO de la carpeta del dominio antes de subir — es fácil subir a la raíz por error si la navegación falló
4. Una vez subido, selecciona el `.zip` → botón **Extract**
5. En el diálogo de extracción, especifica la ruta destino correcta (ej. `/tudominio.com`) — si lo dejas en blanco puede extraer en la raíz de tu cuenta en vez de en la carpeta del dominio
6. Verifica que `index.html` (u otros ficheros) quedó directamente en la raíz de la carpeta del dominio, no dentro de una subcarpeta extra
7. Borra el `.zip` sobrante (ya no se necesita, y evita exponerlo públicamente)

## Paso 5 — Verificar que todo funciona
```bash
curl -sk https://midominio.com/ -o /tmp/test.html -w "HTTP: %{http_code}\n"
head -c 300 /tmp/test.html
```
Debe devolver `HTTP: 200` y el HTML correcto de tu sitio.

También comprueba que el certificado SSL, aunque al principio puede ser el genérico del hosting (ej. `*.web-hosting.com`) mientras AutoSSL emite uno propio (proceso automático, puede tardar minutos-horas), la conexión HTTPS ya funciona sin errores.

## Checklist rápido
- [ ] Addon Domain creado en cPanel (validado por TXT si nameservers siguen en Cloudflare)
- [ ] Custom Domain eliminado del proyecto Cloudflare Pages/Workers
- [ ] Registro A creado en Cloudflare DNS, apuntando a la IP del hosting, en modo **DNS only**
- [ ] Contenido subido y extraído en la carpeta correcta del dominio
- [ ] Verificado con `curl` que responde HTTP 200 con el contenido correcto
- [ ] Zip(s) sobrantes borrados del servidor

## Errores/pitfalls encontrados en la práctica
- El formulario "Create Domain" de cPanel puede quedarse con el spinner varios segundos/minutos tras el TXT recién propagado — si falla, espera y reintenta antes de asumir que algo está mal.
- Las sesiones de cPanel (`cpsess...` en la URL) expiran y cambian; si navegas con una URL vieja puede devolver 404 o un formulario en blanco — vuelve a la home de cPanel y navega de nuevo.
- El File Manager de cPanel (versión "v3", basada en YUI) no siempre permite navegar por doble click sintético vía JavaScript — a veces requiere clicks reales de usuario, especialmente para abrir el diálogo nativo de "Upload".
- Verificar SIEMPRE en qué carpeta real se subió el fichero antes de extraer — es fácil que un upload aterrice en la raíz de la cuenta (`/home/usuario/`) en vez de en la carpeta del dominio si la navegación previa no llegó a la carpeta correcta.
