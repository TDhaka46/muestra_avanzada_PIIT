# SATELLITE-TRACK v2.4 — Contexto Completo del Proyecto

## Resumen Ejecutivo

Prototipo funcional de una **plataforma inteligente de mantenimiento predictivo para transporte de personal minero**. Es un frontend puro (sin backend) construido como una Single Page Application (SPA) en HTML/CSS/JS vanilla, con toda la lógica de UI, navegación y estado en el cliente.

El archivo entregable es `SATELLITE-TRACK.html` — un archivo autocontenido que se abre con doble clic en cualquier navegador moderno.

---

## Stack Tecnológico

| Elemento | Decisión |
|---|---|
| Framework | Vanilla HTML + CSS + JavaScript (sin React, sin bundler) |
| Tipografía | Inter (UI) + JetBrains Mono (valores numéricos, IDs, códigos) vía Google Fonts |
| Iconografía | Tabler Icons Webfont v3.7.0 vía CDN |
| Gráficos | CSS puro (barras animadas con `transition: height`) |
| Animaciones | CSS `@keyframes` + `transition` (sin librería externa) |
| Tema | Dark enterprise — inspirado en SAP Fiori Dark / Microsoft Fluent Dark / IBM Maximo |
| Distribución | Archivo `.html` único autocontenido (~68KB) |
| Dependencias CDN | Google Fonts, Tabler Icons (solo fuentes/íconos, sin JS externo) |

> **Nota:** El proyecto fue iniciado con la intención de usar React + Tailwind + React Router + Framer Motion + Recharts + Heroicons, pero durante el desarrollo se optó por HTML/CSS/JS vanilla para mantenerlo como un único archivo portable sin proceso de build.

---

## Arquitectura de la Aplicación

### Patrón de navegación

SPA con navegación por visibilidad de `div.page` — no hay React Router ni hash routing. La función `goTo(page)` oculta todas las páginas y muestra la indicada.

```
Dashboard (page-dashboard)
    └── [Botón "Ver" en BUS-089] → Consola Prescriptiva (page-console)
            └── [Botón "Ejecutar Desvío"] → Reconfiguración (page-reconfig)
                    └── [Botón "Aplicar Cambio"] → Toast OT → Dashboard
```

### Fuente única de datos (State)

Todo el estado vive en variables JS globales al top del `<script>`:

```javascript
let vehicles = [...]      // Array principal de vehículos — tabla + gráfico RUL
let extraBars = [...]     // Vehículos adicionales solo para el gráfico RUL
let events = [...]        // Feed de eventos prescriptivos
let reconfigId            // ID del vehículo activo en pantalla Reconfig
let selectedSev           // Severidad seleccionada en la consola de telemetría
let consoleOpen           // Estado del panel lateral de telemetría
let selectedEcCode        // Código de error seleccionado en la consola
```

### Estructura de un vehículo

```javascript
{
  id: 'BUS-089',           // Identificador único
  type: 'Bus Interprov.',  // Tipo de vehículo
  icon: 'ti-bus',          // Clase de Tabler Icons
  route: 'Lima → Trujillo',
  status: 'CRÍTICO',       // 'CRÍTICO' | 'ALERTA' | 'ESTABLE' | 'PROGRAMADO'
  rulH: 14,                // Remaining Useful Life en horas (número)
  prio: 'P1',              // 'P1' | 'P2' | 'P3'
  ot: null,                // null | 'OT-2026-XXXX' (cuando tiene OT creada)
  errors: ['P0700','P2714'] // Códigos de error activos
}
```

---

## Sistema de Diseño

### Tokens CSS (variables)

```css
/* Fondos — 5 niveles de elevación */
--bg-canvas:   #080e1a   /* base más profunda */
--bg-base:     #0c1422
--bg-surface:  #101c2e   /* topbar, sidebar, cards */
--bg-raised:   #142035   /* elementos elevados */
--bg-overlay:  #192840
--bg-hover:    #1e3049   /* estado hover */

/* Bordes */
--border-s: rgba(255,255,255,0.06)   /* sutil */
--border-d: rgba(255,255,255,0.10)   /* default */
--border-b: rgba(255,255,255,0.16)   /* fuerte */

/* Acento principal */
--accent:   #2563eb
--accent-l: #3b82f6
--accent-m: rgba(37,99,235,0.12)  /* fondo muted */

/* Semáforo industrial */
--crit:     #f43f5e   /* rojo — crítico */
--warn:     #f59e0b   /* ámbar — alerta */
--ok:       #10b981   /* verde — estable */
--info:     #06b6d4   /* cyan — informativo/OT */
--violet:   #8b5cf6   /* violeta — IA/LSTM */

/* Cada color tiene variantes -bg y -br (border) */
--crit-bg: rgba(244,63,94,0.10)
--crit-br: rgba(244,63,94,0.28)
/* (mismo patrón para warn, ok, info, violet) */

/* Texto — jerarquía de 4 niveles */
--tx1: #f0f4ff   /* primario */
--tx2: #8fa3c0   /* secundario */
--tx3: #4d6480   /* terciario / placeholders */
--tx4: #2d4060   /* deshabilitado / separadores */
```

### Radios
```css
--r-sm: 4px    /* badges, chips pequeños */
--r-md: 6px    /* botones, inputs */
--r-lg: 10px   /* cards principales */
--r-xl: 14px   /* modales, toast */
```

### Tipografía
- **UI general**: Inter 400/500/600/700
- **Valores numéricos, IDs, códigos, timestamps**: JetBrains Mono 500/700
- Escala de tamaños usada: 8px / 9px / 10px / 11px / 12px / 13px / 17px / 18px / 26px

### Convenciones de componentes

**Acento lateral en tablas**: Las filas críticas tienen `3px` de color en el borde izquierdo de `td:first-child::before`

**Status pills**: `border-radius: 20px` con color semántico + dot con `box-shadow` glow

**KPI cards**: Acento de `3px` en el borde izquierdo vía `::after` pseudo-elemento

**Badges**: `9px`, `font-weight: 700`, `letter-spacing: 0.3px`

---

## Pantallas Implementadas

### 1. Dashboard — Monitoreo de Flota (`page-dashboard`)

**Componentes:**
- Topbar fija (46px)
- Sidebar colapsable (56px → 200px al hover) con 5 items de navegación
- 4 KPI cards (Flota Total, Críticos/Alerta, Disponibilidad, RUL Promedio)
- Grid 2 columnas: tabla de flota + panel lateral
- Tabla de flota con: ID+badge ERR, tipo, ruta, status pill, RUL con barra mini, prioridad, acciones
- Panel lateral: gráfico de barras RUL + estado de flota con progress bars
- Feed de eventos prescriptivos con badge LSTM AI
- Statusbar fija inferior (26px)
- Botón FAB "Consola Telemetría"
- Panel lateral deslizante de Consola de Telemetría (340px desde la derecha)

**Comportamientos especiales:**
- Al recibir una OT el vehículo muestra badge `cyan OT`, estado "PROGRAMADO", barra RUL llena cyan
- La columna de acciones tiene: botón "Ver" (solo activo en BUS-089 sin OT) + botón edición (abre Reconfig para cualquier vehículo)
- Cuando `consoleOpen = true`, el main se estrecha `margin-right: 340px`

### 2. Consola Prescriptiva (`page-console`)

**Componentes:**
- Page header con botón "← Panel", título con `BUS-089` en rojo, badge "EN ESPERA"
- Timeline de 7 pasos (4 completados, 1 activo pulsante, 2 pendientes)
- Card: Análisis Predictivo (grid 2 col con 6 campos + barra de confianza animada)
- Bloque Orquestación Taller (con conn pill "CONECTADO" + datos)
- Bloque Orquestación Almacén (con tabla de materiales: SKU, desc, cant, estado)
- Card Protocolo: alerta box + botones Ejecutar/Cancelar + info box

**Datos hardcodeados** (no dinámicos): BUS-089, 14h RUL, 94.2% confianza, FOSA N°02, materiales TRN-9982-MB / KTS-4412-SEN / LIQ-7721-GEAR

### 3. Reconfiguración de Prioridad (`page-reconfig`)

**Componentes:**
- Asset card con datos del vehículo activo (dinámico via `reconfigId`)
- 3 opciones de prioridad con radio interactivo (P1 crítico, P2 moderado con badge "IA", P3 baja)
- Textarea de justificación con contador de caracteres (max 500)
- Summary strip con 5 celdas (Unidad, Prioridad, RUL, Fosa, Taller)
- Botones Aplicar / Cancelar

**Lógica:** `selectPrio(p)` actualiza clases visuales + `summ-prio`. `applyChange()` valida textarea y llama `showToast()`.

### 4. Toast OT (overlay)

- Overlay con `backdrop-filter: blur(6px)`
- Check animado con `scale(0) → scale(1)`
- Número OT generado: `'OT-2026-' + Math.floor(Math.random()*9000+1000)`
- Progress bar que corre en 3.1s → auto-cierre y regreso al Dashboard
- Al cerrar: marca `v.ot = otNum`, `v.status = 'PROGRAMADO'` y re-renderiza

---

## Consola de Telemetría (Panel Simulador)

### Propósito
Permite simular la recepción de señales de telemetría/códigos de error desde cualquier unidad, actualizando el dashboard en tiempo real.

### Campos del formulario
- **Unidad**: dropdown con las 4 unidades + opción "Nueva unidad..." (muestra input custom)
- **Código de error**: grilla 2×4 con 8 códigos predefinidos (P0700, P2714, P0300, P0190, U0100, P0420, P0116, C0035) — selección toggle
- **Código personalizado**: inputs libres para código + descripción
- **Severidad**: botones CRÍTICO / ALERTA / ESTABLE (auto-ajusta RUL y prioridad)
- **RUL (horas)**: número + select prioridad P1/P2/P3
- **Ruta**: texto libre

### Catálogo de códigos de error
```javascript
const errorCodes = [
  {code:'P0700', desc:'Sistema Transmisión',   sev:'crit'},
  {code:'P2714', desc:'Presión Fluido Trans.',  sev:'crit'},
  {code:'P0300', desc:'Falla Encendido Mult.',  sev:'warn'},
  {code:'P0190', desc:'Sensor Presión Comb.',   sev:'warn'},
  {code:'U0100', desc:'Pérdida Comun. ECU',     sev:'crit'},
  {code:'P0420', desc:'Rendimiento Catalizad.', sev:'warn'},
  {code:'P0116', desc:'Temp. Refrigerante',     sev:'warn'},
  {code:'C0035', desc:'Sensor Vel. Rueda FL',   sev:'crit'},
]
```

### Efecto en el dashboard al inyectar
1. Vehículo existente → actualiza `status`, `rulH`, `prio`, `route`, agrega error al array
2. Vehículo nuevo → push al array `vehicles`, re-sort por prioridad
3. Evento nuevo → `unshift` en array `events` (máx 8 eventos)
4. Re-render: `renderFleet()`, `renderChart()`, `renderFleetStatus()`, `renderEvents()`, `updateKPIs()`
5. Actualiza timestamp "Última sync"
6. Agrega líneas al log de transmisión (terminal verde en la consola)
7. Flash visual en la fila de la tabla (animación `rowFlash`)

---

## Funciones JavaScript Principales

```javascript
goTo(page)              // Navegación entre páginas ('dashboard'|'console'|'reconfig')
openReconfig(vehicleId) // Abre Reconfig con contexto del vehículo seleccionado
toggleConsole()         // Abre/cierra el panel lateral de telemetría
injectTelemetry()       // Lee el formulario y actualiza el estado + dashboard
renderFleet(flashId)    // Re-renderiza la tabla (flashId resalta fila nueva)
renderChart()           // Re-renderiza las barras RUL (vehicles + extraBars, sorted)
renderFleetStatus()     // Re-renderiza panel de estado con progress bars
renderEvents()          // Re-renderiza el feed de eventos
updateKPIs()            // Recalcula y actualiza los 4 KPI cards
renderTimeline()        // Renderiza los 7 pasos del timeline en page-console
renderErrorCodes()      // Renderiza la grilla de códigos de error en la consola
selectPrio(p)           // Selecciona P1/P2/P3 en la pantalla Reconfig
setSev(sev)             // Establece severidad en la consola de telemetría
toggleEc(code, sev)     // Toggle de código de error en la grilla
applyChange()           // Valida y ejecuta el cambio de prioridad → showToast()
showToast()             // Muestra el toast de OT creada → auto-cierra en 3.4s
addLog(cls, text)       // Agrega línea al log de la consola de telemetría
clearLog()              // Limpia el log de transmisión
updateChars()           // Actualiza contador de caracteres del textarea
rulColor(h, hasOT)      // Retorna color CSS según horas RUL
rulPct(h)               // Convierte horas a porcentaje (escala 120h = 100%)
```

---

## Lógica de Colores RUL

```javascript
function rulColor(h, hasOT) {
  if (hasOT)  return 'var(--info)';   // cyan — intervenido
  if (h < 20) return 'var(--crit)';   // rojo — crítico
  if (h < 40) return 'var(--warn)';   // ámbar — alerta
  return 'var(--ok)';                  // verde — estable
}
function rulPct(h) {
  return Math.min(Math.round((h / 120) * 100), 100); // escala 120h max
}
```

---

## Layout y Medidas

```
Topbar:    fixed, top:0,    height: 46px
Sidebar:   fixed, left:0,   width: 56px (hover: 200px), top: 46px, bottom: 0
Main:      margin-top: 46px, margin-left: 56px (200px hover, 340px console-open)
Statusbar: fixed, bottom:0, height: 26px, left: 56px
TC Panel:  fixed, right:0,  width: 340px, top: 46px, bottom: 26px
```

---

## Datos Iniciales del Sistema

### Vehículos en la tabla
| ID | Tipo | Ruta | Status | RUL | Prioridad |
|---|---|---|---|---|---|
| BUS-089 | Bus Interprov. | Lima → Trujillo | CRÍTICO | 14h | P1 |
| BUS-110 | Bus Interprov. | Lima → Huancayo | ALERTA | 22h | P2 |
| BUS-102 | Bus Interprov. | Lima → Ica (T.A) | ESTABLE | 55h | P3 |
| CAM-045 | Camioneta Van | Traslado Base | ESTABLE | 74h | P3 |

### Vehículos extra (solo gráfico RUL)
| ID | RUL | Color |
|---|---|---|
| BUS-077 | 68h | #10b981 |
| CAM-031 | 85h | #06b6d4 |
| BUS-055 | 92h | #10b981 |

---

## Posibles Mejoras Futuras

### Funcionalidad
- [ ] Pantalla de Inventario ERP (tab ya visible en sidebar)
- [ ] Pantalla de Gestión Taller (asignación de mecánicos, fosas, turnos)
- [ ] Pantalla de Reportes Analíticos (gráficos históricos de RUL, MTTR, disponibilidad)
- [ ] Detalle de vehículo (expandir la fila o abrir modal con historial completo)
- [ ] Persistencia de estado (localStorage o IndexedDB)
- [ ] Simulación de telemetría automática (intervalo periódico que degrada RUL)
- [ ] Filtros y búsqueda en la tabla de flota
- [ ] Exportación de OT a PDF

### Técnico
- [ ] Migrar a React + Vite si se necesita escalar
- [ ] Añadir Recharts para gráficos históricos más complejos
- [ ] Versión offline completa (inlinar fuentes e íconos en base64)
- [ ] Modo responsive mobile (actualmente optimizado para desktop 1280px+)
- [ ] Dark/light theme toggle

### Datos
- [ ] Conectar con API real (WebSocket para telemetría en tiempo real)
- [ ] Integración con ERP SAP vía REST
- [ ] Modelo LSTM real para inferencia de RUL

---

## Historial de Iteraciones

1. **v0.1** — Wireframe inicial basado en imagen de referencia (Monitoreo Activos)
2. **v0.2** — Mejora visual premium (JetBrains Mono, animaciones, hover states)
3. **v0.3** — Pantalla Consola Prescriptiva (BUS-089, timeline, orquestación, materiales)
4. **v0.4** — Pantalla Reconfiguración de Prioridad (radio interactivo, justificación, summary)
5. **v0.5** — Integración SPA completa (navegación, estado global, toast OT, update BUS-089)
6. **v1.0** — Rediseño enterprise premium (sidebar SAP-style, tokens CSS, page headers, statusbar)
7. **v1.1** — Fix: gráfico RUL sincronizado con tabla; botón edición → Reconfig
8. **v1.2** — Consola de Telemetría (panel lateral, formulario, inyección de señales, log terminal)
9. **v1.3** — Empaquetado como `.html` autocontenido descargable

---

## Archivos del Proyecto

| Archivo | Descripción |
|---|---|
| `SATELLITE-TRACK.html` | Aplicación completa autocontenida (~68KB) |
| `SATELLITE-TRACK-context.md` | Este archivo — contexto para continuar en nueva sesión |

---

## Instrucciones para Continuar en Claude Code

1. Abre este archivo `.md` al inicio de la sesión
2. El código fuente completo está en `SATELLITE-TRACK.html` — ábrelo con el editor
3. Toda la lógica está en el `<script>` al final del `<body>`
4. Todo el CSS está en el `<style>` dentro del `<head>`
5. Para agregar una nueva pantalla: crear `<div class="page" id="page-NOMBRE">`, agregar case en `goTo()`, y agregar item al sidebar
6. Para agregar datos a la tabla: push al array `vehicles` y llamar `renderFleet()`
7. El sistema de tokens CSS está completo — úsalo para mantener consistencia visual

---

*Generado el: 2026-06-23 | Proyecto: SATELLITE-TRACK v2.4 | Sesión: Claude Sonnet 4.6*
