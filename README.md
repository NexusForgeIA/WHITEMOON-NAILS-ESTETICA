# WHITEMOON-NAILS-ESTETICA — demo de centro de uñas y estética

Landing de captación de una sola página para **centros de uñas (nails) y
estética**, con el agente IA de WhiteMoon. Marca: **WhiteMoon** (la propia
agencia).

**En vivo:** https://nexusforgeia.github.io/WHITEMOON-NAILS-ESTETICA/

HTML5 + CSS plano + JS vanilla. Sin React, Vite, Framer ni Tailwind.
Sin paso de build: GitHub Pages sirve el repo tal cual.

Es una réplica exacta del molde de demos de WhiteMoon (la anterior del
escaparate): mismo layout, mismas secciones, mismos efectos, mismos
breakpoints y la misma Kanit autoalojada. Solo cambian la paleta (clara: blanco, rosa y dorado), los textos,
las imágenes, el schema, el agente y el sector del backend.

---

## Regla de oro: honestidad

Esta demo **no inventa datos**. No hay años de experiencia, reseñas,
testimonios, profesionales, clientas reales, cifras de resultados ni precios.
Los tres casos de "Nuestro trabajo" son **ejemplos ilustrativos**, no clientas.

Los dos primeros van etiquetados como **Portfolio**. Solo el tercero
(recuperación de la uña natural) usa el formato **Antes / Después**, porque ahí
sí hay un estado previo que enseñar; aun así las fotos son de stock y **no son
la misma mano**: los `alt` describen lo que cada imagen muestra de verdad.

El bloque de contacto incluye un hueco marcado como *Hueco reservado* para los
diseños, las reseñas y los datos que aportará el centro real.

---

## Estructura

```
index.html                              landing completa
assets/css/styles.css                   sistema de diseño + @font-face de Kanit
assets/fonts/kanit-{300,500,600,900}-latin.woff2
assets/js/site.js                       reveal · magnet · marquee · chars · cards apiladas
assets/js/lead.js                       envío de leads — ÚNICO sitio con config de Supabase
assets/js/agente.js                     asistente IA "Lía"
assets/img/                             27 fotos locales (la og:image es la del hero)
supabase/functions/nails-notify/        Edge Function del aviso por Telegram
robots.txt  sitemap.xml  llms.txt  .nojekyll
```

Orden de secciones: **Hero · Marquee · Sobre nosotros · Servicios · Nuestro
trabajo**, más un bloque **FAQ + Contacto** al final.

Todo el contenido va dentro de un único `<main id="contenido">`; el `<nav>` y
el `<footer>` quedan fuera, así que hay un solo landmark principal.

---

## Tipografía autoalojada

Kanit se sirve desde `assets/fonts` en **woff2, subset latin**, en vez del
`<link>` de Google Fonts. Motivos:

- Dos conexiones menos a terceros (`fonts.googleapis.com`, `fonts.gstatic.com`).
- El peso **900** —el del titular del hero— va en `<link rel="preload">`, así
  que el H1 se pinta ya con Kanit y **no hay reflow del hero**, que era lo que
  disparaba el CLS.

Solo se embarcan los 4 pesos que usa la página (300 · 500 · 600 · 900), ~19 KB
cada uno. El `unicode-range` es el del subset latin de Google, que cubre las
tildes, la ñ y los signos `¿` `¡` del castellano.

### Titular del hero: "UÑAS CON ARTE"

En una sola línea, Kanit 900, `white-space: nowrap` (como el molde). Medido en
el navegador: mide **7,237 veces su font-size**. A 320 px quedan 272 px útiles,
así que caben 37,6 px (11,74vw); el `clamp(2rem, 11.3vw, 200px)` deja un 3,7 %
de margen y entra en una línea desde 280 px.

La tilde de la **Ñ** sobresale ~0,02 em por encima de la caja de
`line-height: 1`, y el `overflow: hidden` de `.hero__title` la cortaba: el H1
lleva `padding-top: .06em`.

### CLS 0: altura de reserva de la barra

`site.js` mide la barra y escribe su altura en `--nav-h`, que da el hueco
superior del hero. El molde tenía una reserva única de `84px`, pero en móvil la
barra mide 50 px: el JS la corregía después del primer pintado, el titular se
desplazaba y Lighthouse daba **CLS 0,005** (también en el molde). Ahora la
reserva coincide con la altura medida en cada breakpoint (50 · 62 · 70 · 77 ·
81 · 85 px) y el CLS es **0**. La altura la marca el logo, no el texto, así
que no cambia al llegar la fuente.

Los enlaces de la barra van a 1.125rem hasta 1279 px y suben a 1.4rem desde
**1280**. Antes subían en 1024 y entre 1024 y ~1280 "Habla con el agente" se
partía en dos líneas (barra de 104 px). Ahora es una sola fila de 84,8 px a
1024, 1100 y 1280.

### Foto del hero centrada

`.hero__img` se centra con la propiedad **`translate`**, no con `transform`:
el reveal (`[data-rv].in { transform: none }`) y el magnet (`style.transform`)
escriben `transform` y pisaban el `translate(-50%)`, así que la foto salía
descentrada. `translate` se compone aparte, y el reveal y el magnet siguen
igual.

---

## Paleta y contrastes

Clara, no oscura: blanco cálido, rosa y dorado. Variables en el `:root` de
`styles.css` y constantes `BG`, `INK`, `ROSE`, `GOLD`, `LINE` y `GRAD` de
`agente.js`.

| Rol | Color |
|---|---|
| Fondo base | `#FFFBF9` (blanco cálido) |
| Superficie alterna (Servicios) | `#FBE8EE` (rosa empolvado) |
| Texto | `#2B1A21` (ciruela muy oscuro, no negro puro) |
| Rosa de acento (solo decorativo) | `#D8869F` |
| Rosa para texto y botones | `#A8385F` |
| Dorado de detalle (líneas, bordes, brillos) | `#C9A24A` · brillo `#E9CF86` |
| Dorado para texto grande / normal | `#9A7330` / `#7E5D22` |
| Botón primario | degradado `#A8385F` → `#8C6A2A`, texto blanco |
| Titulares y numerales 01–05 | degradado `#9A7330` → `#A8385F` |

Contrastes medidos (WCAG 2.x) sobre el color **ya compuesto** con la opacidad:

| Par | Ratio | Requisito | |
|---|---|---|---|
| Texto `#2B1A21` sobre `#FFFBF9` | 16,05:1 | 4,5 | ✅ |
| Texto `#2B1A21` sobre `#FBE8EE` | 14,05:1 | 4,5 | ✅ |
| Rosa `#A8385F` sobre `#FFFBF9` | 6,01:1 | 4,5 | ✅ |
| Rosa `#A8385F` sobre `#FBE8EE` | 5,26:1 | 4,5 | ✅ |
| Blanco sobre `#A8385F` (botón, burbuja del usuario) | 6,18:1 | 4,5 | ✅ |
| Blanco sobre `#8C6A2A` (punto más claro del botón) | **4,99:1** | 4,5 | ✅ |
| Degradado de titulares sobre `#FFFBF9` (punto más claro) | 4,19:1 | 3 (grande) | ✅ |
| Degradado de titulares sobre `#FBE8EE` (numerales de Servicios) | 3,67:1 | 3 (grande) | ✅ |
| Dorado `#7E5D22` sobre `#FFFBF9` ("Hueco reservado") | 5,87:1 | 4,5 | ✅ |
| Secundarios: texto a `.7` sobre `#FFFBF9` | 6,00:1 | 4,5 | ✅ |
| Secundarios: texto a `.7` sobre `#FBE8EE` | 5,66:1 | 4,5 | ✅ |
| Chat: texto sobre la burbuja del bot | 14,22:1 | 4,5 | ✅ |
| Chat: placeholder a `.7` sobre blanco | 6,09:1 | 4,5 | ✅ |
| Chat: error `#B42318` sobre blanco | 6,57:1 | 4,5 | ✅ |
| Chat: borde del input sobre blanco | 3,78:1 | 3 (no texto) | ✅ |
| Dorado `#C9A24A` sobre `#FFFBF9` | 2,33:1 | — | solo decorativo |
| Rosa `#D8869F` sobre `#FFFBF9` | 2,61:1 | — | solo decorativo |

Con texto oscuro sobre fondo claro, las opacidades del molde no valen: a `.6`
el secundario se queda en 4,36:1. **Todos los textos secundarios van a `.7`**
(servicios, casos, FAQ, contacto, hueco reservado, pie y cabecera del chat).

La sección Servicios se invierte a **rosa empolvado sobre blanco** (en el molde
era blanco sobre negro), para mantener el ritmo de secciones. Sombras suaves y
cálidas en rosa muy bajo; la barra al hacer scroll es blanco translúcido con
blur.

---

## Imágenes

Las fotos son de **Unsplash** con licencia libre y están **descargadas y
servidas en local**: nada de hotlink en caliente. Descargadas a `q=70` y al
ancho real del hueco (×2 en las cards, para retina). En el HTML hay un
comentario `<!-- [IMG_XXX] -->` justo antes de cada `<img>`.

**Sin caras** (regla de la casa): solo manos, pies, producto e interiores.

| Token | Fichero | Tamaño |
|---|---|---|
| `[IMG_HERO]` | `hero-nails.jpg` · pincel aplicando esmalte | 1040×693 |
| `[IMG_MARQUEE_1..21]` | `mq-01.jpg` … `mq-12.jpg` (12 únicas, en bucle) | 840×540 |
| `[IMG_DECO_1..4]` | `deco-1.jpg` … `deco-4.jpg` · esmaltes y herramientas | 440×440 |
| `[IMG_P1_A/B/C]` | `case1-*.jpg` · nail art floral | 940×460 · 940×676 · 1200×980 |
| `[IMG_P2_A/B/C]` | `case2-*.jpg` · francesa y baby boomer | idem |
| `[IMG_P3_A/B/C]` | `case3-*.jpg` · recuperación de la uña natural | idem |
| `og:image` | `og-nails.jpg` · la misma foto que el hero | 1200×630 **JPG** (nunca SVG) |

Todas con `width`/`height` **reales** declarados y `loading="lazy"` salvo el
hero, que además va con `fetchpriority="high"` y `rel="preload"`. Los `alt`
describen lo que la foto muestra de verdad.

**Son fotos de stock genéricas de manicura y pedicura, no trabajos de un centro
concreto.** Al personalizar para un cliente real hay que sustituirlas por sus
diseños. Está declarado en el bloque *Hueco reservado* de la web y en
`llms.txt`.

---

## Efectos (todos vanilla, en `site.js`)

| Efecto | Implementación |
|---|---|
| **Reveal** | `IntersectionObserver` once, `rootMargin: 50px` → clase `.in`. Transición CSS de `opacity` + `transform`, `cubic-bezier(.25,.1,.25,1)`, 0.7 s. Offsets y delays por elemento vía `--rx` `--ry` `--rdelay` `--rdur`. |
| **Magnet** | `mousemove`; si el cursor entra en el rect + 150 px, `translate3d(dx/3, dy/3, 0)`. |
| **Marquee** | `scroll` passive. `offset = (scrollY − sectionTop + innerHeight) × 0.3`; fila 1 `translateX(offset−200)`, fila 2 al revés. Tiles triplicados por JS, con las copias en `aria-hidden` y `alt=""`. |
| **Chars** | Cada carácter en un `<span>`; opacidad de 0.2 a 1 según el progreso de scroll. El párrafo completo queda en `aria-label` para que el lector no lo lea letra a letra. |
| **Cards apiladas** | `position: sticky` (`top` 96 px / md 128 px, más `i × 28px`) dentro de contenedores de `85vh`. La escala baja 0.03 por cada card posterior. |

Un único listener de `scroll` agrupado en `requestAnimationFrame`. Con
`prefers-reduced-motion` no se registra ningún listener decorativo y todo se
muestra en su estado final.

---

## Asistente IA "Lía"

Modelo **demo + pivote**. Quien abre el asistente no es clienta del centro: es
un dueño de negocio viendo lo que hace un agente de WhiteMoon. Por eso Lía no
pide datos de cita: enseña cómo responde y pivota a captar al visitante como
**prospecto de agencia**.

**Regla de marca (AI Act):** el rótulo del chat dice **"Asistente IA"**. La
cabecera muestra *Lía* y debajo *Asistente IA · demo · WhiteMoon*; el panel se
anuncia como *"Lía, asistente IA del centro de uñas de WhiteMoon"*.

Flujo corto, solo botones hasta llegar a los datos:

```
servicio → respuesta breve → pivote → nombre → teléfono → cierre
```

1. Saludo *"Hola, soy Lía, la asistente del centro. ¿Qué te gustaría ver?"*
   con 3 botones: **Semipermanente**, **Uñas de gel o acrílico** y **Nail art**.
2. Al pulsar uno, Lía responde con una frase fija sobre ese servicio. **Sin
   cifras**: ningún precio ni dato del centro inventado.
3. A continuación viene el pivote, el del molde en femenino: *"Y esto te lo he
   respondido yo sola, un agente de WhiteMoon. En tu negocio haría lo mismo,
   24/7. ¿Te interesa uno así? Déjame tus datos y te llamamos."*
4. *"¿Cómo te llamas?"* → *"¿Tu teléfono?"* → cierre *"Perfecto, {nombre}. Te
   llamamos al {telefono}."* y envío del lead.

Nombre (mínimo 2 caracteres) y teléfono (español, 9 dígitos, admite `+34` y
`0034`) se validan antes de avanzar. Al cerrar, el input se queda a la vista
pero **deshabilitado**, con el placeholder *"Conversación finalizada"*.

Los textos viven en las constantes `SERVICIOS` y `PIVOTE` de
`assets/js/agente.js`.

### Qué lleva el lead

| Campo `leads_web` | Valor |
|---|---|
| `origen` | `demo-nails` |
| `sector` | `nails` (enruta el aviso) |
| `interes` | `Quiere agente IA para su negocio` |
| `mensaje` | `Dueño de negocio llegado desde la demo de uñas. Tipo de negocio: Centro de uñas y estética` |

El tipo de negocio **no se pregunta**: va implícito como *Centro de uñas y
estética*. El prefijo `Tipo de negocio: ` es el que lee `nails-notify`; si se
cambia en `agente.js`, hay que cambiarlo también en la función.

### Envío del lead

`lead.js` hace **dos cosas en paralelo**:

1. `INSERT` en `leads_web` (Supabase) con la clave **publicable**, protegida por
   RLS — `origen='demo-nails'`, `sector='nails'`, `empresa='WhiteMoon'`.
   Con **un reintento** si PostgREST devuelve `503` (proyecto despertando).
2. Aviso a la Edge Function `nails-notify` por **`navigator.sendBeacon`**,
   con el cuerpo como `Blob` de tipo `text/plain;charset=UTF-8` — **no**
   `application/json`, que dispararía un preflight que `sendBeacon` no puede
   hacer. Así el aviso sale aunque el usuario cierre la pestaña justo después
   de dejar el teléfono. Si el navegador no encola el beacon, cae a `fetch`
   con `keepalive`.

### Edge Function `nails-notify`

Calcada de la función de aviso del molde. Envía por **Telegram Bot API** leyendo los
secrets `TELEGRAM_BOT_TOKEN` y `TELEGRAM_CHAT_ID` (los mismos que ya existen en
el proyecto; no hay secretos nuevos), con `verify_jwt: false` y guard de lead
incompleto (sin nombre o sin teléfono → `400`, sin aviso). El tipo de negocio
lo extrae de `mensaje`. Formato del aviso:

```
🔔 PROSPECTO DE AGENCIA · vino de la demo de uñas (demo-nails)
Nombre: …
Teléfono: …
Tipo de negocio: Centro de uñas y estética
```

Despliegue:

```bash
supabase functions deploy nails-notify --project-ref mlaqtniujnvfxcvcourm --no-verify-jwt
```

**Nunca CallMeBot**: los avisos de WhiteMoon van siempre por Telegram.

### Seguridad

En el repo **solo** vive la clave publicable de Supabase (`sb_publishable_…`),
pensada para el navegador. El token del bot de Telegram y cualquier otro
secreto viven como *secrets* de la Edge Function, nunca en el JS.

---

## SEO / GEO

- `title` 37 c y `meta description` 150 c, idéntica en Open Graph y Twitter.
- `og:image` en **JPG** 1200×630.
- JSON-LD en un único `@graph`: `NailSalon` (subtipo válido de
  `LocalBusiness`) con `hasOfferCatalog` de los 5 servicios, `Service`,
  `BreadcrumbList` y `FAQPage`. Sin duplicados.
- Las respuestas del `FAQPage` son texto plano, sin etiquetas inline, y
  **coinciden palabra por palabra** con el DOM visible.
- `areaServed`: noroeste de Madrid (Majadahonda, Las Rozas, Pozuelo, Boadilla
  y Torrelodones). Mismo `geo` y NAP que el resto de demos.
- `llms.txt` con un único `#` inicial y enlaces en Markdown.
- `robots.txt` permite GPTBot, ClaudeBot, PerplexityBot y Google-Extended.
- `.nojekyll` para que GitHub Pages sirva el repo tal cual.

---

## Personalizar para un cliente real

1. **Marca** — buscar y reemplazar `WhiteMoon` en `index.html`, `llms.txt` y la
   constante `EMPRESA` de `assets/js/lead.js`, y sustituir los dos ficheros
   `assets/img/whitemoon-logo.*` por el logo del cliente.
2. **Colores** — bloque `:root` de `assets/css/styles.css`, el degradado
   `.hero-heading` y las constantes de color de `assets/js/agente.js`. Si se
   cambia la paleta, **volver a medir** los contrastes de la tabla de arriba.
3. **Contacto y mapa** — la sección `#contacto` lleva los datos **reales de
   WhiteMoon** (643 199 580, `comercial@whitemoon.es`, Majadahonda) para que los
   enlaces se puedan probar. Hay que sustituir el `tel:`, el `mailto:`, el
   `wa.me/` y las coordenadas del `<iframe>` del mapa por los del centro.
4. **Dominio** — sustituir `https://nexusforgeia.github.io/WHITEMOON-NAILS-ESTETICA/`
   en canonical, og:url, hreflang, JSON-LD, `sitemap.xml`, `robots.txt` y
   `llms.txt`.
5. **Zonas y servicios** — `areaServed` del JSON-LD, `llms.txt` y las
   constantes `SERVICIOS` y `PIVOTE` de `assets/js/agente.js` (y el tipo de
   negocio fijo de `mensaje` en `submitLead`).
6. **Afirmaciones de higiene y de servicio — CONFIRMAR CON EL CENTRO REAL** antes
   de publicar: que desinfecta y esteriliza las herramientas después de cada
   servicio, que las limas son de un solo uso, que hace la retirada en el
   centro y que ofrece pedicura con o sin semipermanente. Aparecen en el claim
   del hero, en la FAQ (DOM y `FAQPage`), en el JSON-LD y en `llms.txt`.
7. **Fotos** — sustituir las de stock por los diseños reales del centro (sin
   caras, o con consentimiento por escrito).

---

## Accesibilidad

- Enlace *saltar al contenido* hacia el `<main>`, `:focus-visible` en toda la web
  (contorno rosa `#A8385F`).
- Un solo `<main>` y jerarquía `h1 → h2 → h3` sin saltos.
- Contrastes AA medidos sobre el color real ya compuesto: ver la tabla de
  *Paleta y contrastes*.
- El FAB del asistente lleva un `aria-label` que **empieza por su texto
  visible**, para no disparar `label-content-name-mismatch` (WCAG 2.5.3).
- Las copias de los tiles del marquee van con `aria-hidden` y `alt=""`.
- Las imágenes decorativas de "Sobre nosotros" son `aria-hidden`.
- FAQ sobre `<details>` nativo: legible aunque el JS no cargue.
- `prefers-reduced-motion` respetado en la web y en el asistente.
- Responsive real en 600, 640, 768, 900 y 1024. En móvil las cards dejan de
  ser sticky y se apilan en flujo normal.

---

Demo de [WhiteMoon Agencia IA](https://whitemoon.es/).
