# CryoBox Web Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Sitio de una sola página con scroll, con visor 3D interactivo de la CryoBox, para la muestra del 2026-12-05.

**Architecture:** Sitio estático Vite + Three.js. Todo el texto vive en `src/content.js`; la geometría y las posiciones viven en `src/three/`. Se entrega como un único `index.html` autocontenido (vite-plugin-singlefile) que abre con doble clic en Chrome sobre Windows, sin internet.

**Tech Stack:** Vite, Three.js (`OrbitControls`), Vitest + jsdom, vite-plugin-singlefile. JavaScript con módulos ES.

**Spec:** `docs/superpowers/specs/2026-10-30-cryobox-web-design.md`

## Global Constraints

- Fecha límite: 2026-12-05. Se muestra en computadora con pantalla grande, mouse y teclado, **Windows + Chrome**.
- Funciona **sin internet**: sin CDN, sin fuentes ni recursos externos. Entrega: un único `index.html`.
- Idioma: español.
- El contenido (textos, integrantes, etapas, rótulos) se edita en **un solo archivo**: `src/content.js`.
- Estilo visual C: degradé azul frío (azul profundo a azul hielo). Portada con etiqueta "CRYOBOX · PROTOTIPO", frase, visor 3D y botón "Explorar".
- Frase principal por defecto: "CryoBox: vacunas frías, sin importar la distancia." (alternativa: "El sistema innovador de refrigeración de vacunas").
- Integrantes: Kevin Aguilera, Santino Sinich, Lionel Villar, Manolo Mamaní, Jonathan Quiroga. Roles: "Pendiente de definir". Sin fotos.
- Orden de secciones: Portada, ¿Qué es CryoBox?, Cómo funciona, Proceso de desarrollo, Equipo, Pie. Menú fijo arriba.
- Proceso de desarrollo: etapas provisorias marcadas "a completar".
- Circuito **sin compresor**. Prototipo en desarrollo, **sin mediciones**: ningún texto afirma temperaturas ni rendimiento. Nunca "HOT EFRIGRATOR" ni "compresor".
- Se muestra "bomba 12 V" y **no** el voltaje de la batería.
- Nota de seguridad visible: el isobutano es inflamable.
- Tres espacios reservados para logos: Conectados Mendoza Futura, Nodo Reynaldo Merín, CryoBox.
- 3D esquemático, colores planos. Caja semitransparente, evaporador adentro y condensador afuera. Etiquetas grandes. Posiciones en configuración.
- Piezas: conservadora de telgopor, evaporador, condensador, capilar (3 vueltas), estructura de madera, cooler, bomba 12 V, tubos de PVC, batería, gas isobutano.

## Review Focus

1. Un integrante sin rol (o un campo vacío en `content.js`) debe mostrar "Pendiente de definir", nunca "undefined" ni un hueco. → Task 2
2. Chrome sin WebGL o con el contexto perdido: mostrar un mensaje y la imagen de respaldo, no un recuadro en blanco. → Task 3
3. Cambio de tamaño de ventana o de resolución: el canvas y las etiquetas se reacomodan sin quedar desfasados. → Task 5
4. Etiquetas de piezas detrás de la cámara o fuera de pantalla deben ocultarse; etiquetas que se superponen no deben tapar el texto de otra. → Task 5
5. Zoom y rotación sin límites dejan al usuario "dentro" del modelo o perdido; la rotación automática debe retomarse tras la inactividad. → Task 3

## Cronograma (hoy 2026-10-30)

| Fecha tope | Tasks |
|---|---|
| 2026-11-03 | 1, 2 |
| 2026-11-08 | 3 |
| 2026-11-17 | 4, 5 |
| 2026-11-22 | 6 |
| 2026-11-26 | 7 |
| 2026-11-29 | 8 |
| 2026-12-01 | 9 (verificación en Windows + Chrome) |
| 2026-12-02 a 12-04 | Margen: contenido real del equipo (documento de proceso, roles, logos), ensayo final |

El modelo 3D (tasks 4 y 5) tiene un **tope de tiempo**: si al 2026-11-17 no está listo, se simplifican las piezas, no se atrasan las demás.

## Estructura de archivos

- `package.json`, `vite.config.js`, `index.html`: herramientas y entrada.
- `src/content.js`: todo el contenido editable + `validateContent`.
- `src/main.js`: arranque; conecta secciones, escena y diagrama.
- `src/styles.css`: tema C.
- `src/ui/sections.js`: pinta portada, qué es, proceso, equipo y pie desde el contenido.
- `src/ui/nav.js`: menú fijo con salto a secciones.
- `src/ui/diagram.js`: SVG del circuito con flechas animadas.
- `src/three/quality.js`: perfil de calidad.
- `src/three/scene.js`: escena, cámara, controles, bucle.
- `src/three/layout.js`: posiciones de cada pieza.
- `src/three/parts.js`: construye la geometría.
- `src/three/labels.js`: etiquetas e interacción.
- `src/three/flow.js`: flechas 3D del gas.
- `scripts/check-offline.js`: comprueba que `dist/index.html` no usa recursos externos.
- `docs/verificacion.md`: lista de verificación manual.
- `tests/*.test.js`.

---

### Task 1: Herramientas y archivo de contenido

**Files:**
- Create: `package.json`, `vite.config.js`, `index.html`, `src/content.js`, `src/main.js`, `tests/content.test.js`
- Modify: `.gitignore` (agregar `node_modules/`, `dist/`)

**Interfaces:**
- Produces: `content` con forma `{ meta: {title, tagline, tagline_alt}, about: {paragraphs: string[]}, parts: Part[], circuit: CircuitStep[], timeline: Stage[], team: Member[], safetyNote: string }`; `Part = {id, name, description, labelOnly?: boolean}`; `CircuitStep = {id, partId, text}`; `Stage = {title, text, pending: boolean}`; `Member = {name, role}`; `validateContent(c): string[]` (lista de errores, vacía si es válido).
- Ids de piezas: `caja`, `evaporador`, `condensador`, `capilar`, `estructura`, `cooler`, `bomba`, `tubos`, `bateria`, `gas` (`gas` con `labelOnly: true`).
- Orden del circuito: `capilar`, `evaporador`, `condensador`, `bomba`.

- [ ] **Step 1: Escribir los tests** en `tests/content.test.js`:
  - `has 10 parts with the expected ids`
  - `team has exactly 5 members and every role is "Pendiente de definir"`
  - `pump name or description mentions "12 V"` y `no text mentions battery voltage` (ningún string de `parts` con `/\b9\s?V\b/` ni "batería" junto a un número de voltios)
  - `no text claims temperatures or performance`: ningún string con `/\d+\s?°\s?C/` ni `/mantiene|garantiza|logra enfriar/i`
  - `no text mentions "compresor" as part of the system` (sólo se admite la frase "sin compresor") y nunca "EFRIGRATOR"
  - `validateContent returns [] for the real content and an error for a member missing a name`
  - `timeline stages are all pending: true`
- [ ] **Step 2: Ejecutar** `npx vitest run tests/content.test.js`. Esperado: FAIL (módulo inexistente).
- [ ] **Step 3: Armar el proyecto** con Vite vanilla, instalar `three`, `vitest`, `jsdom`, `vite-plugin-singlefile`; configurar Vitest con `environment: 'jsdom'`; `vite.config.js` con `base: './'` y el plugin singlefile; scripts `dev`, `build`, `test`.
- [ ] **Step 4: Implementar `src/content.js`** con los valores de la spec (§6, §7, §8). Las descripciones de las piezas son de una o dos líneas, en español, sin afirmaciones de rendimiento; los pasos del circuito siguen §8 de la spec con redacción de "diseño previsto".
- [ ] **Step 5: Ejecutar** `npm test`. Esperado: PASS.
- [ ] **Step 6: Commit** (incluye la spec, este plan y el `.gitignore` pendientes):

```bash
git add .gitignore docs package.json package-lock.json vite.config.js index.html src tests
git commit -m "chore: scaffold Vite project and editable content file"
```

### Task 2: Página, tema y secciones

**Files:**
- Create: `src/styles.css`, `src/ui/sections.js`, `src/ui/nav.js`, `tests/sections.test.js`
- Modify: `index.html`, `src/main.js`

**Interfaces:**
- Consumes: `content` (Task 1).
- Produces: `renderSections(root: HTMLElement, content): void` crea `<section>` con ids `portada`, `que-es`, `como-funciona`, `desarrollo`, `equipo`, `pie`, y dentro de `portada` un `<div id="visor">` (contenedor del 3D); `initNav(root: HTMLElement): void` arma el menú fijo con un enlace por sección.

- [ ] **Step 1: Escribir los tests** en `tests/sections.test.js` (jsdom):
  - `renders the six sections in order`
  - `team renders 5 cards`
  - `member without role renders "Pendiente de definir" and the DOM never contains the text "undefined"` (Review Focus 1; probar también un `content` con `role` vacío y con `timeline[0].text` ausente)
  - `renders 3 logo placeholders`
  - `renders the safety note text in the footer`
  - `timeline items show the text "a completar"`
  - `portada shows "CRYOBOX · PROTOTIPO" and the tagline`
  - `nav has one link per section pointing to its id`
- [ ] **Step 2: Ejecutar** `npx vitest run tests/sections.test.js`. Esperado: FAIL.
- [ ] **Step 3: Implementar** `renderSections` y `initNav`; `styles.css` con el degradé azul frío, tipografía grande de sistema (sin fuentes externas), `scroll-behavior: smooth`, menú `position: sticky`. Valores de campos ausentes: usar los textos "Pendiente de definir" / "A completar".
- [ ] **Step 4: Ejecutar** `npm test`. Esperado: PASS. Revisar `npm run dev` en Chrome a mano.
- [ ] **Step 5: Commit**

```bash
git add src index.html tests
git commit -m "feat: page sections, nav and cold-blue theme"
```

### Task 3: Escena 3D base

**Files:**
- Create: `src/three/quality.js`, `src/three/scene.js`, `tests/quality.test.js`, `tests/scene-limits.test.js`
- Modify: `src/main.js`

**Interfaces:**
- Produces: `pickQuality(search: string, devicePixelRatio: number): { pixelRatio: number, antialias: boolean }` (`?calidad=baja` fuerza `pixelRatio: 1` y `antialias: false`; si no, `pixelRatio = min(devicePixelRatio, 2)`, `antialias: true`); `CONTROL_LIMITS = { minDistance: number, maxDistance: number, idleMs: number }`; `createScene(container: HTMLElement, quality): { scene, camera, renderer, controls, dispose(): void } | null` (devuelve `null` y muestra el mensaje de respaldo en `container` si no hay WebGL).

- [ ] **Step 1: Escribir los tests**:
  - `pickQuality: "?calidad=baja" gives pixelRatio 1 and antialias false`
  - `pickQuality: default caps pixelRatio at 2`
  - `CONTROL_LIMITS: minDistance > 0 and maxDistance > minDistance` (Review Focus 5)
  - `createScene returns null and writes a fallback message into the container when WebGL is unavailable` (jsdom no tiene WebGL; Review Focus 2). El mensaje es "No se pudo iniciar el visor 3D" más una descripción textual del sistema.
- [ ] **Step 2: Ejecutar** `npx vitest run tests/quality.test.js tests/scene-limits.test.js`. Esperado: FAIL.
- [ ] **Step 3: Implementar** con `OrbitControls` (damping, zoom y rotación limitados por `CONTROL_LIMITS`, `autoRotate` que se pausa al interactuar y se retoma tras `idleMs` sin actividad) y `ResizeObserver` sobre el contenedor. Un cubo de prueba se retira en el Task 4.
- [ ] **Step 4: Ejecutar** `npm test` y comprobar a mano en Chrome que rota, hace zoom, se detiene al arrastrar y retoma sola. Esperado: PASS.
- [ ] **Step 5: Commit**

```bash
git add src tests
git commit -m "feat: base 3D scene with limited controls and WebGL fallback"
```

### Task 4: Piezas y disposición

**Files:**
- Create: `src/three/layout.js`, `src/three/parts.js`, `tests/parts.test.js`
- Modify: `src/main.js`

**Interfaces:**
- Consumes: `content.parts` (Task 1), `createScene` (Task 3).
- Produces: `LAYOUT: Record<partId, { position: [number, number, number], labelAnchor: [number, number, number] }>` con una entrada por cada id de `content.parts`; `buildParts(layout): Map<string, THREE.Object3D>` con un objeto por pieza **no** `labelOnly`; la `caja` usa material semitransparente.

- [ ] **Step 1: Escribir los tests** en `tests/parts.test.js`:
  - `LAYOUT has an entry for every part id in content`
  - `buildParts returns an object for every non-labelOnly part`
  - `evaporador bounding box lies inside the caja bounding box` y `condensador bounding box lies outside it` (con `THREE.Box3`)
  - `caja material is transparent`
  - `capilar has exactly 3 turns` (exponer `userData.turns` en la pieza)
- [ ] **Step 2: Ejecutar** `npx vitest run tests/parts.test.js`. Esperado: FAIL.
- [ ] **Step 3: Implementar** la geometría con formas simples y colores planos (cobre para serpentinas y capilar, madera para la estructura, blanco para telgopor, gris para bomba, ventilador y tubos, azul para el PVC). Serpentinas con `TubeGeometry` sobre una hélice. Agregar las piezas a la escena en `main.js`.
- [ ] **Step 4: Ejecutar** `npm test` y revisar a mano que se ve el evaporador adentro de la caja. Esperado: PASS.
- [ ] **Step 5: Commit**

```bash
git add src tests
git commit -m "feat: schematic 3D parts and config-driven layout"
```

### Task 5: Etiquetas e interacción

**Files:**
- Create: `src/three/labels.js`, `tests/labels.test.js`
- Modify: `src/main.js`, `src/styles.css`

**Interfaces:**
- Consumes: `LAYOUT`, `buildParts` (Task 4), `content.parts`.
- Produces: `projectLabel(point: [number, number, number], camera: THREE.Camera, size: {width: number, height: number}): { x: number, y: number, visible: boolean }` (pura); `createLabels(container: HTMLElement, parts: Part[], layout, camera, objects: Map): { update(size): void, dispose(): void }`.

- [ ] **Step 1: Escribir los tests**:
  - `projectLabel: a point in front of the camera at the origin projects to the center of the viewport and is visible`
  - `projectLabel: a point behind the camera is not visible` (Review Focus 4)
  - `projectLabel: a point outside the viewport is not visible`
  - `createLabels creates one label element per part, with the part's name`
  - `clicking or hovering a label shows its description; moving away hides it`
  - `update(size) repositions labels after a size change` (Review Focus 3: tras `update({width: 800, height: 600})` las posiciones cambian respecto de 1920×1080)
  - `labels closer than 24 px are offset so their rectangles do not overlap` (Review Focus 4)
- [ ] **Step 2: Ejecutar** `npx vitest run tests/labels.test.js`. Esperado: FAIL.
- [ ] **Step 3: Implementar** etiquetas HTML superpuestas, de tamaño grande (mínimo 20 px de fuente) y legibles a distancia; `update` se llama en cada cuadro y en cada cambio de tamaño; la descripción aparece en un panel junto a la etiqueta.
- [ ] **Step 4: Ejecutar** `npm test` y revisar a mano con la ventana a 1920×1080 y a 1280×720. Esperado: PASS.
- [ ] **Step 5: Commit**

```bash
git add src tests
git commit -m "feat: part labels with hover descriptions and overlap handling"
```

### Task 6: Diagrama de "Cómo funciona"

**Files:**
- Create: `src/ui/diagram.js`, `tests/diagram.test.js`
- Modify: `src/main.js`, `src/styles.css`

**Interfaces:**
- Consumes: `content.circuit`, `content.parts`.
- Produces: `renderDiagram(container: HTMLElement, content, onSelect: (partId: string) => void): void` dibuja un SVG con un nodo por paso del circuito, en el orden de `content.circuit`, conectados por flechas animadas (CSS), y la lista de pasos con su texto. Al elegir un paso se llama `onSelect(step.partId)`.

- [ ] **Step 1: Escribir los tests**:
  - `renders one node per circuit step in content order`
  - `node labels are the part names, not typos` (ningún texto con `/EFRIGRATOR/i` ni `/compresor|compressor/i`)
  - `clicking a step calls onSelect with its partId`
  - `the diagram contains the text "sin compresor"`
  - `the gas isobutano is mentioned in the diagram`
- [ ] **Step 2: Ejecutar** `npx vitest run tests/diagram.test.js`. Esperado: FAIL.
- [ ] **Step 3: Implementar** el SVG redibujado (caliente en rojo, frío en azul, sin copiar el diagrama de referencia).
- [ ] **Step 4: Ejecutar** `npm test`. Esperado: PASS.
- [ ] **Step 5: Commit**

```bash
git add src tests
git commit -m "feat: redrawn circuit diagram with step selection"
```

### Task 7: Flechas 3D del gas y resaltado (opcional al final)

**Files:**
- Create: `src/three/flow.js`, `tests/flow.test.js`
- Modify: `src/main.js`

**Interfaces:**
- Consumes: `LAYOUT`, `content.circuit`, `onSelect` del Task 6.
- Produces: `buildFlowPath(layout, circuit: CircuitStep[]): THREE.Vector3[]` (puntos que pasan por el ancla de cada pieza del circuito, en orden, y cierra el ciclo); `createFlow(scene, path): { update(dt: number): void, highlight(partId: string | null): void }`.

- [ ] **Step 1: Escribir los tests**:
  - `buildFlowPath visits the anchors in circuit order and returns to the start`
  - `highlight(partId) changes the emissive color of that part and highlight(null) restores it`
- [ ] **Step 2: Ejecutar** `npx vitest run tests/flow.test.js`. Esperado: FAIL.
- [ ] **Step 3: Implementar** flechas pequeñas que recorren el trayecto en bucle; conectar `onSelect` del diagrama con `highlight`. Si al 2026-11-26 hay atraso, entregar sólo `buildFlowPath` + flechas y dejar el resaltado fuera.
- [ ] **Step 4: Ejecutar** `npm test`. Esperado: PASS.
- [ ] **Step 5: Commit**

```bash
git add src tests
git commit -m "feat: animated 3D gas flow and step highlighting"
```

### Task 8: Entrega en un solo archivo, sin internet

**Files:**
- Create: `scripts/check-offline.js`, `tests/offline.test.js`, `README-EDITAR.md`
- Modify: `vite.config.js`, `package.json`

**Interfaces:**
- Produces: `findExternalRefs(html: string): string[]` (URLs `http(s)://` en atributos `src`, `href` o en `@import`/`url()`), exportada desde `scripts/check-offline.js`; script `npm run build:entrega` que ejecuta `vite build` y luego `check-offline`.

- [ ] **Step 1: Escribir los tests** en `tests/offline.test.js`:
  - `findExternalRefs returns [] for html with only inline scripts and styles`
  - `findExternalRefs finds an https src and an @import url`
  - `the built dist/index.html (after npm run build) contains no external refs and no <script src>`
- [ ] **Step 2: Ejecutar** `npx vitest run tests/offline.test.js`. Esperado: FAIL.
- [ ] **Step 3: Implementar** `findExternalRefs`, el script y la configuración del build. `README-EDITAR.md` explica en lenguaje simple qué campos de `src/content.js` se editan y que luego hay que correr `npm run build:entrega`; el resultado es `dist/index.html`.
- [ ] **Step 4: Ejecutar** `npm run build:entrega` y `npm test`. Esperado: PASS; abrir `dist/index.html` con doble clic (`file://`) con la red desconectada y comprobar que el 3D carga.
- [ ] **Step 5: Commit**

```bash
git add scripts tests vite.config.js package.json README-EDITAR.md
git commit -m "feat: single-file offline build and check"
```

### Task 9: Verificación contra la spec y ensayo

**Files:**
- Create: `docs/verificacion.md`

- [ ] **Step 1: Escribir `docs/verificacion.md`** con una casilla por criterio de §11 de la spec y por restricción de §3, más: probar en **Windows + Chrome** a pantalla completa, con mouse; probar `?calidad=baja`; probar con Wi-Fi apagado; leer toda la web buscando afirmaciones de rendimiento.
- [ ] **Step 2: Ejecutar `npm run build:entrega` y `npm test`**, completar la lista en la computadora real de la muestra (o una equivalente) y anotar resultados.
- [ ] **Step 3: Reemplazar** los marcadores pendientes con lo que el equipo haya entregado (documento de proceso, roles, logos, frase final), volver a construir y repetir la lista.
- [ ] **Step 4: Commit**

```bash
git add docs/verificacion.md src
git commit -m "docs: verification checklist and final content"
```
