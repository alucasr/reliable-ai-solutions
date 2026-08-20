# reliable-ai-solutions

Este repositorio contiene el código fuente de 3 landing pages para la empresa **Reliable AI Solutions**, que ofrece soluciones basadas en RAG (Retrieval Augmented Generation) para procesar y generar contenido a partir de documentación interna de clientes.

## Descripción

Las landing pages están diseñadas para atraer a diferentes segmentos de clientes y destacar las características únicas de la solución RAG de la empresa:

- **smartgenai.solutions**: Versión corporativa, enfocada en empresas que buscan soluciones profesionales y escalables.
- **reliableai.solutions**: Versión vibrante, ideal para startups y equipos ágiles que valoran la innovación y la simplicidad.
- **aireliable.solutions**: Versión dark/tech, con un estilo moderno y tecnológico, adecuado para audiencias que prefieren interfaces minimalistas y modernas.

Cada landing está desplegada en **Cloudflare Pages** y utiliza **Cloudflare Workers** para el backend, incluyendo formularios de contacto que usan **FormSubmit.co** con endpoints hash, sin exponer el correo del propietario.

## Estructura de carpetas

```
.
├── landings/                # Código fuente editable de las landing pages
├── deploy/                  # Copias listas para desplegar en Cloudflare Pages
│   ├── smartgenai.solutions/ # Versión corporativa
│   ├── reliableai.solutions/ # Versión vibrante
│   └── aireliable.solutions/ # Versión dark/tech
├── docs/                    # Análisis de competencia y SEO
└── README.md
```

## ¿Cómo desplegar?

1. **Inicia sesión en Cloudflare Pages** y selecciona el proyecto.
2. **Configura el dominio** para cada landing (smartgenai.solutions, reliableai.solutions, aireliable.solutions).
3. **Sube el contenido** de la carpeta correspondiente de `deploy/` al dashboard de Cloudflare Pages.
4. **Asegúrate de que el archivo `index.html` esté en la raíz de cada despliegue**.
5. **Configura los Workers** si es necesario para manejar formularios o endpoints.

## Estado del proyecto

- ✅ **PoC completada**: Las landing pages están funcionales y desplegadas.
- ⚠️ **Empresa aún no constituida legalmente**: El proyecto se encuentra en fase de validación de mercado.

¡Estamos construyendo una solución que transformará la forma en que las empresas gestionan su documentación interna con inteligencia artificial!