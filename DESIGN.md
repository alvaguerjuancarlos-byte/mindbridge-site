# DESIGN.md — MindBridge (mindbridge.com.mx)

> **Naturaleza de este documento:** a diferencia de un DESIGN.md generativo (brief para construir un sitio desde cero), este documenta el sistema de diseño **tal como existe hoy** en `sitio.html` (verificado contra `https://mindbridge.com.mx` el 2026-08-24). Todo valor aquí fue extraído del CSS/HTML real, no inventado. Donde el sitio se desvía de buenas prácticas (contraste, breakpoints, CSS muerto) se anota explícitamente como **Nota** en vez de corregirse en silencio.

---

## 1. Visual Theme & Atmosphere

MindBridge se presenta como una consultora de IA que traduce tecnología compleja a decisiones de negocio — el tagline "Smart Tech · Human Sense" es literal en el diseño: superficies limpias en blanco hueso (`--white`, `--bone`) con un acento teal saturado que funciona como firma visual (números, subrayados, íconos, bordes activos), sobre una base tipográfica geométrica (Space Grotesk) para autoridad y una sans humanista (Manrope) para lectura de cuerpo. El navy (`--navy`) aparece solo en superficies de "peso" — barra de navegación CTA, encabezados de acordeón, sección de contacto — funcionando como el color de "decisión" frente al teal como color de "dato/resultado".

El sitio evita las convenciones de agencia de IA genérica (gradientes morados, glassmorphism decorativo, blobs): usa bordes de 0.5px casi invisibles (`--border: rgba(0,168,188,0.13)`) en vez de sombras para separar tarjetas, y confía en fotografía real de banda ancha (fotos del fundador, equipos) con overlay navy semitransparente como recurso narrativo entre secciones, más que en ilustración.

**Key Characteristics:**
- Acento teal (`#00A8BC`) como color de dato/métrica/resultado; navy (`#1E3A5F`) como color de autoridad/CTA de alto compromiso
- Bordes ultra-finos (0.5px, ~13% opacidad de teal) en vez de sombras como recurso de separación principal
- Tipografía dual: Space Grotesk (display, siempre 700 en headings reales) + Manrope (cuerpo, labels, 300–600)
- Tamaños de heading fluidos vía `clamp()` — no hay escala fija por breakpoint, escala con el viewport
- "Bandas foto" de imagen real + overlay navy(0.72–0.85) como separadores narrativos entre secciones
- Micro-interacción sutil: `translateY(-1px/-2px)` en hover de botones, sin escalados agresivos
- Radios de esquina tokenizados y consistentes (`--radius-2xs` a `--radius-pill`), reutilizados en TODO — nunca valores sueltos
- Un solo breakpoint real (768px) — el sitio es esencialmente de dos estados: desktop y mobile, sin tablet intermedio

---

## 2. Color Palette & Roles

### Tokens declarados (`:root`, línea 11 de `sitio.html`)

| Token | Hex / Valor | Uso real observado |
|---|---|---|
| `--teal` | `#00A8BC` | Acento primario: números/métricas, links activos, íconos, bordes de foco, texto destacado en `<em>`/`<span>` |
| `--teal-mid` | `#00B4C8` | Bordes izquierdos de blockquote, flechas, hover de bordes de tarjeta |
| `--teal-light` | `#7DDCE8` | Íconos sobre fondo navy (acordeones), texto secundario sobre fondo oscuro |
| `--teal-pale` | `#E0F7FA` | Fondos de hover (pilares, métricas), fondo de íconos, fondo de pills |
| `--navy` | `#1E3A5F` | CTA principal (nav, botones), fondo de header de acordeón, fondo de sección "Hablemos", tagline bar |
| `--navy-mid` | `#2A4E7F` | Hover de `--navy` (botones, nav CTA) |
| `--ink` | `#1A2A3A` | Texto principal sobre fondo claro |
| `--ink-muted` | `#3D5166` | Texto secundario/cuerpo sobre fondo claro |
| `--ink-soft` | `#5B7185` | Texto terciario (subtítulos, metadata) |
| `--bone` | `#F3F6F9` | Fondo de sección alterno (nosotros bloque 1, servicios, casos, tarjetas de stat) |
| `--white` | `#F9FBFC` | Fondo de página / fondo de tarjeta |
| `--border` | `rgba(0,168,188,0.13)` | Todo borde de tarjeta, separador, grid de 1px entre columnas de acordeón |

### Colores usados inline que NO están tokenizados (extraídos del HTML)

| Valor | Uso |
|---|---|
| `#111` | Fondo oscuro de marcos de logo/imagen en acordeones y carrusel de clientes |
| `#fff` (literal, no `var(--white)`) | Texto sobre navy/oscuro, tarjeta de Juan Carlos, botones |
| `rgba(255,255,255,0.08 / 0.1 / 0.55 / 0.6 / 0.75 / 0.85 / 0.9)` | Texto y superficies translúcidas sobre fondo navy (sección Hablemos, proof strip, tagline) |
| `rgba(30,58,95,0.72 / 0.75)` | Overlay navy sobre "bandas foto" (nosotros, casos) |
| `rgba(14,28,46,0.85)` | Overlay sobre imagen de fondo de la sección Hablemos |
| `rgba(0,180,200,0.1)` | Sombra de hover de `caso-card` |
| `rgba(0,168,188,0.1)` | Sombra de hover de tarjeta de industria (`mkCard`) |
| `rgba(0,0,0,0.15)` | Overlay sutil `.hero::before` (clase actualmente sin uso real, ver §7) |

### Interactive States (reales)

| Rol | Valor | Uso |
|---|---|---|
| Link nav hover | `var(--teal)` | `.nav-links a:hover` |
| Link nav (mobile abierto) | `#fff` | `.nav-links a` dentro de `@media max-width:768px` |
| Focus / foco de teclado | **No definido explícitamente** | No hay `:focus` ni `:focus-visible` en ningún selector del sitio — ver Nota en §7 |
| Error / Success / Warning | **No existen** — el sitio no tiene formularios con validación (el "CTA" es un `mailto:`) | — |

### ⚠️ Nota de contraste (verificado, no corregido)

Contraste calculado (fórmula WCAG relativa luminancia) para las combinaciones reales más usadas:

| Combinación | Ratio | Veredicto |
|---|---|---|
| `--ink` (#1A2A3A) sobre `--white`/`--bone` | ~14.8:1 | ✅ Pasa AA/AAA holgado |
| `--ink-muted` (#3D5166) sobre `--white` | ~8.4:1 | ✅ Pasa AA |
| **`--teal` (#00A8BC) como texto sobre `--white`/`--bone`** | **~2.75:1** | ❌ **No pasa AA** ni para texto grande (mínimo 3:1). Se usa así en labels de sección (`.section-tag`, "Nosotros", "Servicios", etc., 11px/500) y en números destacados |
| `#fff` sobre `--navy` (#1E3A5F) | ~8.6:1 | ✅ Pasa AA |
| `--teal-light` (#7DDCE8) sobre `--navy` | ~9.1:1 | ✅ Pasa AA (uso correcto en acordeones) |

**Implicación:** el teal como texto sobre fondos claros es una elección de marca deliberada y consistente en todo el sitio, pero técnicamente no pasa WCAG AA. No se recomienda "arreglarlo" sin decisión explícita del usuario, ya que es la firma visual del sitio — se documenta para que quede registrado.

---

## 3. Typography Rules

**Fuente principal (display/headings):** Space Grotesk — Google Fonts, pesos 300/400/500/600/700
**Fuente secundaria (cuerpo/labels):** Manrope — Google Fonts, pesos 300/400/500/600/700
**Import real:** `family=Space+Grotesk:wght@300;400;500;600;700&family=Manrope:wght@300;400;500;600;700`

> Nota: Space Grotesk está en la lista de "fuentes prohibidas" del generador de sistemas de diseño estándar (sobreusada en sitios generados por IA). Aquí se documenta tal cual porque es la fuente real y ya establecida del sitio en producción — no se sustituye sin instrucción explícita.

| Rol | Fuente | Tamaño real (CSS) | Peso | Line-height | Letter-spacing | Dónde se usa |
|---|---|---|---|---|---|---|
| Hero Headline | Space Grotesk | `clamp(3rem, 5.5vw, 5rem)` (48–80px) | **400** (sin override, no 700) | 1.08 | normal | `.hero-headline` |
| Section Heading (h2) | Space Grotesk | `clamp(1.8rem,2.8vw,2.5rem)` a `clamp(2rem,3.5vw,3rem)` (29–48px según sección) | 700 | 1.15 | -0.02em | H2 de nosotros/servicios/clientes/casos/hablemos |
| Sub-heading / Card title | Space Grotesk | 15–16px | 700 | 1.2–1.3 | normal | Título de acordeón, tarjeta de Juan Carlos |
| Stat número | Space Grotesk | 2rem (32px) | 700 | 1 | normal | Contadores "+250", "92%", "40" |
| Body Large | Manrope | 15–16px | 300 | 1.7–1.8 | normal | Párrafos de intro de sección, CTA copy |
| Body | Manrope | 12–14px | 300–400 | 1.5–1.65 | normal | Descripciones de tarjeta, párrafos secundarios |
| Button / CTA | Space Grotesk (CTA grande) / Manrope (nav) | 14–16px | 500–600 | 1 | 0–0.02em | `.nav-cta`, `.btn-primary`, botón "Agenda una conversación" |
| Eyebrow / Section tag | Manrope | 10–11px | 500 | normal | 0.08–0.12em | "Nosotros", "Servicios", "Casos de éxito", etc. — siempre uppercase |
| Small / metadata | Manrope | 11–12px | 400–500 | 1.4–1.5 | 0–0.09em | Labels de contacto, sub-logo, KPI labels |
| Caption / logo-sub | Manrope | 8.4px | 500 | normal | 0.09em | `.logo-sub` — el tamaño más pequeño del sitio, uppercase |

**Notas reales de implementación:**
- No existe una escala tipográfica fija en `rem`/`px` puros para headings — todo H2 usa `clamp()` con mínimo y máximo distintos por sección (no hay un solo valor "Section Heading" reutilizado, hay 3 variantes ligeramente distintas: `2.75rem`, `2.5rem`, `3rem` de techo).
- El Hero Headline **no lleva `font-weight: 700`** pese a ser el elemento más grande de la página — usa el peso normal (400) del navegador. Esto es infrecuente frente al resto de headings (siempre 700) y probablemente no intencional, pero así está en producción.
- El cuerpo de texto usa mayoritariamente peso 300 (Light), no 400 — es una elección consistente que le da ligereza al texto largo.

---

## 4. Component Stylings

### Botones

**`.nav-cta` (botón de navegación, usa `!important` para pisar `.nav-links a`):**
| Propiedad | Valor |
|---|---|
| Background | `var(--navy)` #1E3A5F |
| Texto | `#fff` |
| Padding | `10px 22px` (desktop) / `12px 24px` (mobile) |
| Border-radius | `var(--radius-sm)` = 6px |
| Peso / tamaño | 500 / 14px |
| Hover background | `var(--navy-mid)` #2A4E7F |
| Hover transform | `translateY(-1px)` |
| Transición | `background 0.2s, transform 0.15s` |

**`.btn-primary` (CTA de hero — clase definida, actualmente sin uso en el HTML activo, ver §7):**
| Propiedad | Valor |
|---|---|
| Background | `var(--navy)` |
| Texto | `#fff` |
| Padding | `14px 32px` |
| Border-radius | `var(--radius-sm)` = 6px |
| Tamaño / peso | 15px / 500 |
| Shadow | `0 2px 12px rgba(30,58,95,0.22)` |
| Hover background | `var(--navy-mid)` |
| Hover transform | `translateY(-2px)` |
| Hover shadow | `0 4px 20px rgba(30,58,95,0.32)` |

**CTA de contacto real en uso ("Agenda una conversación", inline style, sección Hablemos):**
| Propiedad | Valor |
|---|---|
| Background | `var(--teal)` #00A8BC (NO navy — única vez que un CTA primario usa teal en vez de navy) |
| Texto | `#fff`, Space Grotesk 600, 16px |
| Padding | `16px 40px` |
| Border-radius | `var(--radius-sm)` = 6px |
| Estados hover/focus/active | **No definidos** — es un `<a>` con solo estilo base |

**`.btn-secondary` (definida, sin uso activo):**
| Propiedad | Valor |
|---|---|
| Color | `rgba(255,255,255,0.85)` |
| Tamaño/peso | 15px / 400 |
| Hover color | `var(--teal)` |
| Elemento hijo `.btn-arrow` | círculo 20×20, borde `currentColor`, `translateX(3px)` en hover |

**Botón de acordeón (`onclick="toggleAccordion"`, inline style):**
| Propiedad | Valor |
|---|---|
| Background | `var(--navy)` |
| Padding | `1.25rem 2rem` |
| Min-height | 80px |
| Ícono contenedor | 40×40px, `rgba(255,255,255,0.1)`, `var(--radius-md)` = 8px |
| Flecha (`▼`) | `var(--teal-light)`, `transition: transform 0.3s`, rota 180° al abrir (vía JS, no CSS `:checked`) |

### Cards

**Stat card (nosotros — "+250 Proyectos"):**
| Propiedad | Valor |
|---|---|
| Background | `var(--white)` |
| Border | `0.5px solid var(--border)` |
| Border-radius | `var(--border-radius-lg)` = `var(--radius-2xl)` = 12px |
| Padding | `1rem 1.5rem` |
| Shadow | ninguna |

**Card de industria (`mkCard`, generada por JS):**
| Propiedad | Valor |
|---|---|
| Background | `var(--white)` |
| Border | `0.5px solid var(--border)` |
| Border-radius | `var(--radius-2xl)` = 12px |
| Padding | `1rem` |
| Hover border | `var(--teal-mid)` |
| Hover shadow | `0 2px 12px rgba(0,168,188,0.1)` |
| Transición | `border-color 0.25s, box-shadow 0.25s` |

**`caso-card` (tarjeta expandible de caso de éxito, generada por JS):**
| Propiedad | Valor |
|---|---|
| Background | `var(--bone)` |
| Border | `0.5px solid var(--border)` |
| Border-radius | `var(--border-radius-lg)` = 12px |
| Hover border | `var(--teal-mid)` |
| Estado abierto (`.is-open`) border | `var(--teal)` |
| Estado abierto shadow | `0 4px 20px rgba(0,180,200,0.1)` |
| Transición | `border-color 0.3s, box-shadow 0.3s` |

### Inputs
**No existen inputs de formulario en el sitio.** Todo contacto es vía `<a href="mailto:...">` — no hay `<input>`, `<textarea>` ni validación. Si se agrega un formulario, no hay precedente de estilo de input que documentar; habría que definirlo desde cero siguiendo los tokens existentes (`--radius-sm`, `--border`, `--teal` como focus).

### Navigation
| Propiedad | Valor |
|---|---|
| Background | `rgba(249,251,252,0.94)` + `backdrop-filter: blur(22px)` |
| Height | 72px (desktop) / 60px (mobile) |
| Padding | `0 4rem` (desktop) / `0 1.25rem` (mobile) |
| Link color | `var(--ink-muted)` |
| Link hover | `var(--teal)` |
| Borde inferior | `0.5px solid var(--border)` |
| Menú mobile | `position:fixed`, `width:50%`, `height:38vh`, `background: rgba(30,58,95,0.24)` + blur, hamburguesa de 3 líneas con animación de rotación a "X" |

---

## 5. Layout Principles

**Unidad base:** el sitio no usa una escala de 4px/8px estricta — trabaja principalmente en `rem` con saltos irregulares pero consistentes por contexto: `0.75rem, 1rem, 1.25rem, 1.5rem, 2rem, 2.5rem, 3rem, 3.5rem, 4rem, 5rem, 6rem`.

**Grid / contenedor:**
| Propiedad | Valor |
|---|---|
| Max container width | `1200px` (todas las secciones de contenido) |
| Contenedor Hablemos | `1100px` (única sección con ancho distinto) |
| Padding horizontal desktop | `4rem` (64px) — constante en todas las secciones |
| Padding horizontal mobile | `1.25rem` (20px) |
| Columnas reales | No hay grid de 12 columnas — cada sección define su propio `grid-template-columns` ad-hoc (`1fr 1fr`, `repeat(3,1fr)`, `repeat(4,1fr)`) que colapsa a `1fr` en mobile vía JS (`applyMobileGrids()`), no vía media query |

**Espaciado entre secciones:**
| Contexto | Valor |
|---|---|
| Padding vertical de sección (desktop) | `4rem`–`10.5rem` (varía mucho: nosotros 4rem, servicios 6rem, casos 10.5rem/10rem) |
| Padding vertical de sección (mobile) | `3–3.5rem` uniforme |
| Banda foto separadora | `200–220px` de alto fijo, full-bleed |

**Border Radius Scale (tokens reales):**
| Token | Valor | Uso observado |
|---|---|---|
| `--radius-2xs` | 2px | Barras de hamburguesa |
| `--radius-xs` | 4px | (declarado, sin uso encontrado) |
| `--radius-sm` | 6px | Botones (nav-cta, CTA, acordeón) |
| `--radius-md` | 8px | Contenedores de ícono (40×40, 32×32) |
| `--radius-lg` | 9px | Ícono de caso-card |
| `--radius-xl` | 10px | Métricas (dead CSS), back-bar pill |
| `--radius-2xl` | 12px | Cards de stat, industria, acordeón contenedor (vía `--border-radius-lg`) |
| `--radius-3xl` | 16px | Tarjeta de Juan Carlos (contacto) |
| `--radius-pill` | 99px | Pills de tag/etiqueta ("N casos", pills de tecnología) |
| `--border-radius-md` | = `--radius-md` (8px) | Alias, uso genérico |
| `--border-radius-lg` | = `--radius-2xl` (12px) | Alias más usado del sitio — cards, acordeones, bloques de sección |

---

## 6. Depth & Elevation

El sitio **no usa un sistema de elevación por niveles** — no hay 4–5 sombras graduadas reutilizables. Solo existen 4 valores de `box-shadow` en todo el código, cada uno atado a un componente específico:

| Contexto | CSS box-shadow | Uso |
|---|---|---|
| Flat (default) | `none` | Estado de reposo de prácticamente todo (cards, botones) — el sitio separa superficies con **bordes de 0.5px**, no con sombras |
| `.btn-primary` reposo | `0 2px 12px rgba(30,58,95,0.22)` | Solo en la clase sin uso activo (§7) |
| `.btn-primary` hover | `0 4px 20px rgba(30,58,95,0.32)` | Idem |
| Card de industria hover | `0 2px 12px rgba(0,168,188,0.1)` | `mkCard` (JS) |
| `caso-card` abierta | `0 4px 20px rgba(0,180,200,0.1)` | Estado `.is-open` |

**Implicación para trabajo futuro:** si se necesita más profundidad visual (modales, dropdowns nuevos), no hay un token `--shadow-*` que reutilizar — habría que crear la escala desde cero siguiendo el patrón de color existente (sombras tintadas de navy `rgba(30,58,95,x)` para elementos sobre fondo claro, ya que es el único precedente real).

---

## 7. Observaciones — CSS vivo vs. CSS muerto

Al comparar cada clase declarada en el primer bloque `<style>` (líneas 8–591) contra las clases realmente usadas en el HTML/JS servido, aproximadamente la mitad del bloque es **CSS heredado de una iteración anterior del sitio que ya no se renderiza**:

**Clases declaradas y usadas (vivas):**
`nav`, `.nav-logo`, `.logo-mark`, `.logo-text-block`, `.logo-name`, `.logo-sub`, `.nav-links`, `.nav-cta`, `.nav-hamburger`, `.hero`, `.hero-inner`, `.hero-eyebrow`, `.eyebrow-dot`, `.hero-headline`, `.hero-sub`, `.reveal`, `.accordion-item`, `.accordion-content`, `.accordion-arrow`, `#hablemos`, `.ind-sel-grid` (y `.caso-card`/`.ind-sel-grid`/`mkCard` inyectadas por JS).

**Clases declaradas pero SIN uso en el HTML actual (muertas):**
`.hero-glow`, `.hero-glow-2` (ya con `display:none` explícito), `.btn-primary`, `.btn-secondary`, `.btn-arrow`, `.proof-strip`, `.proof-label`, `.proof-divider`, `.proof-logos`, `.proof-logo`, `.porque`, `.porque-inner`, `.section-tag`, `.porque-grid`, `.porque-statement`, `.porque-right`, `.porque-body`, `.flow`, `.flow-step`, `.flow-num`, `.flow-label`, `.flow-arrow`, `.metrics`, `.metric`, `.metric-num`, `.metric-label`, `.pilares`, `.pilares-header`, `.pilares-headline`, `.pilares-sub`, `.pilares-grid`, `.pilar`, `.pilar-icon`, `.pilar-title`, `.pilar-body`, `.pilar-tag`, `.tagline-bar`, `.tagline-text`.

**Por qué importa:** las secciones "nosotros" y "servicios" fueron reconstruidas con estilos inline (probablemente en una edición posterior) reemplazando lo que antes eran `.porque` + `.pilares` + `.metrics` + `.flow` + `.tagline-bar` — pero el CSS viejo nunca se limpió. Esto no rompe nada (no hay conflicto de nombres), pero es ~250 líneas de peso muerto en un archivo ya de 9.3MB, y una fuente de confusión si alguien busca "dónde se define `.pilar`" esperando encontrar contenido visible.

---

## 8. Responsive Behavior (tal como existe)

### Breakpoints reales

El sitio define **un único breakpoint CSS**: `@media (max-width: 768px)`. No existe breakpoint intermedio de tablet ni de desktop grande — por encima de 768px todo usa los mismos valores fijos, sin importar si el viewport es 900px o 2560px (salvo lo que ya fluye por `clamp()`/`vw` en tipografía).

| Nombre | Ancho | Mecanismo |
|---|---|---|
| Mobile | ≤768px | Media query CSS + JS (`applyMobileGrids`) |
| Desktop (único, sin techo) | >768px | Estilos base, sin adaptación adicional por encima de 768px |

### Estrategia de colapso real

- **Navegación:** hamburguesa aparece <768px (`.nav-hamburger { display:none }` en desktop, `flex` en mobile); menú se despliega como panel fijo lateral (no full-screen), 50% de ancho, 38vh de alto.
- **Grids:** la mayoría de los `grid-template-columns` (porque-grid 2 col, pilares-grid 3 col, ind-sel-grid 4 col, acordeones 3–4 col) están definidos como **estilos inline**, no en clases CSS con media query. El colapso a 1 columna en mobile lo hace JavaScript (`applyMobileGrids()`, sitio.html:1312) recorriendo `document.querySelectorAll('[style*="grid-template-columns"]')` y forzando `1fr` — es decir, la responsividad de casi todo el sitio depende de que JavaScript se ejecute, no de CSS puro. Si JS falla o está deshabilitado, esas secciones no colapsan en mobile.
- **Hero:** el padding se reduce (`3rem 1.25rem 2rem` vs `5rem 4rem 3rem`) pero la estructura sigue siendo `flex-direction: column` en ambos casos — no hay cambio de dirección de flex entre breakpoints.
- **Proof strip:** cambia de `flex-direction: row` a `column` en mobile.
- **Tipografía:** escala principalmente vía `clamp(mín, vw, máx)`, no vía tablas de tamaño por breakpoint — por diseño, no hace falta un override en el media query para la mayoría de los headings.

### Touch targets
No hay un estándar de 44×44px aplicado deliberadamente. El botón de hamburguesa usa `padding:8px` sobre un ícono de ~22px (~38px efectivo, ligeramente por debajo del mínimo recomendado). Los links del menú mobile sí tienen área generosa (`padding: 2rem 1.5rem` en el contenedor, `gap: 1.25rem` entre ítems).

---

## 9. Quick Reference (para futuras ediciones del sitio)

### Colores
- Acento: `#00A8BC` (teal) — dato/resultado/link
- Autoridad/CTA: `#1E3A5F` (navy) — decisión/compromiso
- Texto principal: `#1A2A3A` · Texto secundario: `#3D5166` · Texto terciario: `#5B7185`
- Fondo página: `#F9FBFC` · Fondo alterno: `#F3F6F9`
- Borde universal: `rgba(0,168,188,0.13)`, siempre `0.5px solid`

### Tipografía
- Headings reales: Space Grotesk 700, letter-spacing `-0.02em`, tamaño en `clamp()`
- Cuerpo: Manrope 300 (párrafos largos) / 400–500 (UI, labels)
- Eyebrow/label de sección: Manrope 500, 11px, uppercase, `letter-spacing: 0.1em`, color `var(--teal)`

### Patrón de tarjeta genérico (el más repetido del sitio)
`background: var(--white) o var(--bone); border: 0.5px solid var(--border); border-radius: var(--border-radius-lg) /* 12px */; padding: 1–2.5rem;` — sin sombra en reposo, borde cambia a `var(--teal-mid)` o `var(--teal)` en hover/activo.

### Antes de tocar el sitio
1. Verificar sha256 de `sitio.html` local contra `https://mindbridge.com.mx/sitio.html` (ver memoria del proyecto) — no asumir que el repo local está desplegado. **Ojo:** `mindbridge.com.mx` hace 308 redirect a `www.mindbridge.com.mx` — hay que seguir el redirect (`curl -L`) antes de comparar el hash, o se compara contra la página de redirect y no contra el sitio real.
2. Los estilos reales están **inline**, no en las clases del primer `<style>` — para cambiar el look de una sección, buscar el `style="..."` del bloque, no una clase.
3. La responsividad de grids depende de `applyMobileGrids()` (JS) — un cambio en un `grid-template-columns` inline debe seguir siendo detectable por ese selector (`[style*="grid-template-columns"]`) para colapsar correctamente en mobile.

## 10. Idioma (ES/EN) — agregado 2026-09-26

El sitio es bilingüe con un selector "ES · EN" en el nav (visible también en el menú móvil porque
comparte el mismo `<ul id="nav-links">`). Sin duplicar `sitio.html` (pesa 9.3MB, casi todo
imágenes base64 compartidas entre ambos idiomas) — un solo archivo, diccionario JS + atributos.

- **HTML estático** (nav, hero, nosotros, los 3 acordeones de servicios, clientes, hablemos):
  cada elemento de texto lleva `data-i18n="clave"`. El objeto `TRANSLATIONS = {es:{...}, en:{...}}`
  (arriba del todo del `<script>` principal) trae **ambos** idiomas por clave — el español que ya
  está en el HTML no se "captura" en runtime, es simétrico con el inglés. `applyLanguage(lang)`
  hace `el.innerHTML = TRANSLATIONS[lang][clave]` para cada `[data-i18n]`.
- **`CASOS`/`INDUSTRIAS`** (el explorador de industrias, dentro de la IIFE "EXPLORADOR DE
  INDUSTRIAS"): cada objeto tiene sub-objetos `es:{...}`/`en:{...}` para los campos traducibles
  (`titulo`, `problema`, `pills`, `cierre`, y `kpis[].label` cuando existen). `renderCaso()`/
  `mkCard()` leen `c[currentLang]`/`ind[currentLang]`. `window.rerenderCasos()` vuelve a dibujar
  la vista actual (respetando si hay una industria seleccionada, vía `currentIndId`) cuando cambia
  el idioma — no resetea la navegación del usuario.
- **Persistencia**: `localStorage['mindbridge-lang']`. Default `es` si no hay preferencia guardada.
- **Selector de idioma y contraste móvil**: los botones ES/EN usan la clase `.lang-btn`
  (`.active` para el idioma actual), no color inline — porque el menú móvil pinta los links de
  blanco con `!important` (`.nav-links a` en el media query `≤768px`) y ese selector no alcanza a
  `<button>`. Hay una regla `.nav-links .lang-btn` aparte en ese mismo media query. Si se agrega
  otro control no-`<a>` al nav, va a necesitar su propio override ahí por la misma razón.
- **Bug preexistente encontrado, no relacionado con el idioma**: el objeto `ICO` (íconos SVG por
  industria) usa atributos SVG sin comillas y sin espacio antes de `/>` (ej. `y2=11/>`) — el
  parser de HTML lee el `/` como parte del valor del atributo (`"11/"`), rompe varios íconos
  (visible en consola: `Error: <line> attribute y2: Expected length, "11/"`). No se tocó al hacer
  el bilingüe; si se toca `ICO` por otra razón, aprovechar para separar el `/` con un espacio
  (`y2=11 />`) o comillar los valores.
