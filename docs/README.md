# Reliable AI Solutions — Propuestas de Landing Page

Tres alternativas de landing page para el proyecto "Reliable AI Solutions" (dominios comprados:
`reliablesolutions.ai`, `smartgenai.solutions`, `reliableai.solutions`, `aireliable.solutions`),
una empresa ficticia de soluciones de IA fiable/segura/de confianza para empresas.

> **Idioma:** el copy de las 3 landings está en **inglés**, ya que es el estándar de facto en el
> mercado B2B SaaS de IA/enterprise software (todas las referencias de competencia analizadas —
> Credo AI, Holistic AI, Fiddler AI, Arize AI, IBM watsonx.governance — publican en inglés como
> idioma principal). Este README y el análisis de competencia están en español para el equipo interno.

Análisis de competencia completo: ver `analisis_competencia_seo.md` en este mismo workspace.

---

## Archivo por archivo

### `landing_v1_corporativa.html` — Minimalista / Corporativa
- **Nombre de marca usado:** "ReliableAI" (logo azul marino + acento menta)
- **Paleta:** azul marino corporativo (`#0B1F3A`) + verde menta (`#2FD3A6`) sobre fondo blanco.
- **Tipografía:** Inter (sans-serif geométrica, muy legible, estándar SaaS enterprise).
- **Inspiración directa:** **Credo AI** y **IBM watsonx.governance** — estructura de hero con
  badges de compliance visibles de inmediato (EU AI Act, ISO 42001, SOC 2), barra de logos de
  clientes justo debajo del hero, y un "dashboard" simulado en el hero como prueba visual del
  producto (patrón tomado de Arize AI e IBM).
- **Por qué esta paleta:** el azul marino + menta replica el patrón dominante observado en el
  sector (Credo AI usa azul+verde lima; IBM usa azul institucional) — comunica seriedad y
  confianza sin resultar frío, y el acento menta aporta modernidad frente a un azul plano.
- **Tono:** conservador, orientado a compliance/riesgo — ideal si el cliente objetivo son equipos
  de Legal, Risk & Compliance en banca, salud o sector público.
- **Diferenciador clave:** panel de métricas ("Trust Score 98/100") en el hero, igual que el
  patrón "producto visible desde el hero" de Arize AI/Fiddler AI.

### `landing_v2_vibrante.html` — Bold / Vibrante con gradientes
- **Nombre de marca usado:** "ReliableAI" (wordmark con badge degradado)
- **Paleta:** gradiente violeta → rosa → naranja (`#7C3AED → #EC4899 → #FB923C`) sobre fondo blanco,
  con una sección oscura (`#1E0E3D`) para "How it works".
- **Tipografía:** Sora (titulares, geométrica y con carácter) + Manrope (cuerpo, muy legible).
- **Inspiración directa:** **Holistic AI** (uso de un color de marca distintivo y memorable —
  violeta — poco común en el sector, que ellos usan para diferenciarse del azul genérico) y
  **Fiddler AI** (acento vibrante de alto contraste sobre fondo claro). El uso de texto con
  gradiente y tarjetas flotantes animadas sigue tendencias de diseño SaaS 2025-2026 (Linear,
  Vercel, Stripe) para dar sensación de producto moderno y ágil.
- **Por qué este enfoque:** rompe con el estereotipo "enterprise = aburrido" — pensado para
  posicionar a Reliable AI Solutions como una startup ágil y confiable a la vez, apuntando a
  equipos de producto/ingeniería (no solo compliance) que valoran velocidad de shipping.
- **Diferenciador clave:** stats animados en el hero (uptime, decisiones auditadas, deployments),
  chips flotantes de trust ("Zero incidents this month", "SOC 2 & ISO 42001") con micro-animación,
  sección "How it works" en bloque oscuro para generar contraste visual y ritmo de scroll.

### `landing_v3_dark.html` — Dark Mode / Tech-Futurista
- **Nombre de marca usado:** "RELIABLE.AI" (estética de dominio/terminal)
- **Paleta:** fondo casi negro (`#05060A`) con acentos cian (`#22E5C9`) y violeta (`#8B5CF6`),
  glows radiales sutiles.
- **Tipografía:** Space Grotesk (titulares tech) + JetBrains Mono (para elementos de "terminal"/código).
- **Inspiración directa:** **Arize AI** y **Fiddler AI** (estética "developer tool", fondo oscuro,
  uso de tipografía monoespaciada para reforzar credibilidad técnica) combinada con patrones de
  dark-mode SaaS actuales (Vercel, Linear, GitHub). El hero simula una terminal ejecutando un
  script de verificación (`reliable-ai verify --env production`) — recurso directo del mundo
  developer-tool para comunicar "esto es real, esto corre en producción".
- **Por qué este enfoque:** apunta a compradores técnicos (CTOs, Heads of ML/AI Platform,
  ingenieros) que confían más en un producto que "habla su idioma" (terminal, status live,
  monospace) que en marketing corporativo tradicional. El dark mode también es tendencia dominante
  en landing pages de infraestructura/observabilidad 2025-2026.
- **Diferenciador clave:** panel tipo terminal en el hero con output en vivo simulado, indicador
  de "system status" con pulso animado (patrón "todo funciona, en tiempo real"), microcopy en
  formato `01_CONNECT / 02_DEFINE / 03_VERIFY / 04_PROVE` en la sección "how it works".

---

## Elementos comunes a las 3 (best practices del sector aplicadas)

Extraídos del análisis de las 5 referencias (Credo AI, Holistic AI, Fiddler AI, Arize AI, IBM
watsonx.governance) — ver `analisis_competencia_seo.md`:

1. Hero con propuesta de valor en una frase + CTA dual ("Request/Book a Demo" + CTA secundario).
2. Trust signals explícitos desde el primer scroll: ISO/IEC 42001, SOC 2 Type II, EU AI Act,
   NIST AI RMF, GDPR — simulados como certificaciones (marcar como "simulado/ficticio" antes de
   publicar, ya que estas certificaciones deben obtenerse realmente).
3. Barra de logos de clientes (ficticios) justo después del hero.
4. Producto/dashboard visible desde el hero para anclar la promesa en algo tangible.
5. Sección "How it works" en 4 pasos simples.
6. Testimonios con foto/avatar, nombre, cargo y empresa — 3 por landing.
7. Footer completo con navegación, legal, Trust Center y redes sociales.
8. Responsive mobile-first con media queries en 960px y 640px; animaciones respetan
   `prefers-reduced-motion`; navegación sticky con CTA siempre visible.
9. Contraste de color verificado para cumplir AA en textos principales sobre sus fondos
   respectivos.

## Notas para el siguiente paso

- **Todo el contenido (logos de clientes, testimonios, métricas, certificaciones) es ficticio** y
  debe sustituirse por datos reales antes de publicar cualquiera de las 3 landings.
- Los 3 archivos son 100% autocontenidos (HTML + CSS inline en `<style>`), solo dependen de
  Google Fonts y Font Awesome vía CDN — no requieren build ni backend, se pueden abrir
  directamente en el navegador o desplegar tal cual en cualquiera de los 4 dominios comprados.
- Recomendación de naming: dado que los dominios existentes usan "reliable" de forma variada
  (`reliableai.solutions`, `reliablesolutions.ai`), el nombre de marca "ReliableAI" (v1/v2) o
  "RELIABLE.AI" (v3) encaja de forma natural con el dominio `reliableai.solutions`.
