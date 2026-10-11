# AGENTS.md

Guía para agentes de IA que trabajen en este repositorio.

## Qué es este proyecto

Sitio estático de **Talos**, un asistente con IA para gestión ganadera que el
ganadero usa por chat (texto, audio y fotos). En el copy no se promete un canal
específico (WhatsApp, Telegram…): se habla de "escribirle" o "el chat". No hay backend, build ni
dependencias instaladas: son archivos HTML que se abren directo en el navegador.

`index.html` es la landing pública con waitlist. El resto de la carpeta son
las imágenes que usa: `logo-talos.png` (nav y footer), `Logo talos icono.png`
(avatar del chat), `Logo talos fondo blanco.png` (favicon) y las fotos de fondo
del hero y de la waitlist.

Todo lo que haya en esta carpeta se publica en el sitio. No agregues aquí
archivos internos o de trabajo.

## Idioma y tono

- Todo el contenido visible está en **español de México** (`lang="es"`), con
  vocabulario ganadero: hato, potrero, vaquillas, becerros, palpar, engorda,
  manga, rastro, días de retiro, aretes.
- El tono es cercano y concreto: oraciones cortas, cifras reales y nombres de
  rancho/animal (El Mezquite, La Esperanza, V-040, N-120). Sin jerga técnica
  frente al usuario final.
- Los comentarios del código también van en español.

## `index.html`

Un solo archivo con `<style>` y `<script>` en línea. Mantén esa estructura: no
agregues frameworks, bundlers ni archivos CSS/JS externos.

### Estructura

Las secciones están delimitadas con comentarios `<!-- ===== NOMBRE ===== -->`:
`NAV`, `HERO`, `PROBLEMA` (`#problema`), `SOLUCIÓN` (`#solucion`, tema claro
con clase `.light`), `WAITLIST` (`#waitlist`) y `FOOTER`.

### Estilos

- Usa las variables de `:root` (`--bg`, `--card`, `--line`, `--accent`,
  `--text`, `--muted`, `--radius`, etc.) en vez de colores sueltos. Excepción:
  la sección `.light`, que tiene su propia paleta verde/blanca y un chat que
  imita Telegram (azules `#4b90c4`, `#3a8bc2`).
- CSS compacto, una regla por línea o pocas líneas, agrupado por bloques con
  comentarios `/* ---------- nombre ---------- */`.
- Tipografías: `Google Sans Flex` (texto) y `Geist Mono` (clase `.mono`).
- Íconos: SVG en línea estilo trazo (`stroke-width` 1.8–2, `stroke-linecap`
  y `linejoin` `round`), 18–20 px.
- Responsive con breakpoints en `940px` y `560px`. Respeta
  `prefers-reduced-motion`.
- Animaciones de entrada con la clase `.reveal` (se activa con un
  `IntersectionObserver`).

### Demo del chat

Las conversaciones viven en el objeto `CONVOS` (claves: `hato`, `sanidad`,
`gastos`, `reproduccion`, `alimentacion`), conectadas a los botones
`.chip[data-convo="..."]`. Cada mensaje es un objeto con `k`:

- `day` / `sys`: separador centrado (`t`).
- `u`: mensaje del usuario (`html`, `time`).
- `a`: respuesta de Talos (`html`, `time`, `btns` opcional). Antes de cada `a`
  se inserta un indicador de "escribiendo".
- `audio`: nota de voz (`dur`, `trn` = transcripción, `time`).
- `ticket`: foto de un ticket (`title`, `lines[]`, `time`).

Para agregar un tema nuevo, crea la clave en `CONVOS` **y** su `chip` con el
mismo `data-convo`. Las respuestas deben ser coherentes con los datos del resto
de la demo (mismos ranchos, aretes y cifras).

### Waitlist

El formulario `.js-waitlist` hace `POST` a Formspree (`WAITLIST_ENDPOINT`). Si
cambias campos, conserva los `name` (`finca`, `email`, `whatsapp`, `ubicacion`,
`cabezas`) porque son las columnas que llegan a Formspree. No cambies el
endpoint sin que te lo pidan.

## Modelo de dominio

La referencia del producto es `modelo-datos-ganadero.html`, que vive fuera de
este proyecto, en `Documents/`. Es un build minificado de React de más de 1 MB:
no lo edites ni lo leas completo; búscalo con `grep` si necesitas un dato.

Describe el modelo de datos por esquemas y las funciones que llama el agente:

- `admin`: organizaciones, suscripciones, ranchos, potreros, razas, catálogos.
- `hato`: animales, lotes, conteos (`registrarConteo`, `darDeAltaAnimales`).
- `sanidad`: diagnósticos, aplicaciones y días de retiro.
- `reproduccion`: servicios e inseminaciones, diagnósticos de gestación.
- `produccion`: pesajes, rotación de potreros.
- `comercial`: compras, ventas, traslados, terceros, movimientos financieros.
- `agente`: mensajes recibidos y acciones ejecutadas (trazabilidad).

Cada acción del agente sigue el flujo **Recibe → Entiende → Valida → Confirma
→ Ejecuta → Deja rastro**. Cuando escribas copy o conversaciones para la
landing, respeta ese flujo: Talos confirma antes de escribir datos y avisa
restricciones reales (por ejemplo, un animal en retiro no se vende a rastro).

## Cómo probar

No hay comandos de build ni tests. Abre `index.html` en el navegador y revisa:

1. Que cada chip cargue su conversación y que "↻ Repetir" la reproduzca.
2. Que el layout funcione en escritorio, en ~900 px y en ~375 px de ancho.
3. Que el formulario muestre el estado "Enviando…" y luego el de éxito o error.
