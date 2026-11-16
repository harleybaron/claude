# Diagnóstico: sistema de trabajo con IA (agente personal)

**Fecha:** 2026-11-15
**Estado (15-nov, ~19:20):** entrevista terminada (24 preguntas). Sección 3 verificada en 8 de 8 puntos; el Lenovo queda sin medir porque no respondía. Siguiente paso: las recomendaciones (sección 5, paso 3).

---

## 1. Objetivo del proyecto

Mejorar los procesos y workflows de trabajo con Claude para:

- Reducir los errores y la dependencia de revisión manual.
- Consumir menos recursos de la máquina, sobre todo memoria swap.
- Consumir menos tokens.
- Trabajar más rápido.
- Investigar librerías y repositorios de GitHub que reduzcan el consumo de swap.

Inspiración: «Claude ya se está creando a sí mismo» (Platzi, Freddy Vega, 18:28): https://www.youtube.com/watch?v=CnfoCNd0QHE. Visto el 15-nov por subtítulos.

Es un video de opinión, no un tutorial: no trae ni técnicas ni configuraciones. Lo que sirve para este diagnóstico:
- [05:17–05:45] Informe de METR: en Anthropic la IA pasó de ~1 % de las tareas de investigación (feb-2026) a 26 % (ago-2026).
- [05:55–06:13] El modelo nunca trabaja solo: siempre hay una persona que lo dirige. No es automejora recursiva.
- [07:28–07:40] El cuello de botella ya no es escribir código: es «más que probar, más que verificar, más que conectar». Va con el síntoma 13 (los flujos automáticos de verificación no funcionan).
- [07:48–08:04] Cambió la capa de abstracción: hace falta pensamiento sistémico para llegar a producción.
- [09:13–09:25] Hoy el límite lo ponen la velocidad y la RAM. En el video se habla de los centros de datos, no del Mac.

---

## 2. Respuestas recopiladas

| # | Tema | Respuesta |
|---|------|-----------|
| 1 | Forma de trabajo | De 4 a 6 terminales al tiempo, de proyectos distintos. Casi todo es desarrollo y unos pocos son de generación de video e imágenes. |
| 2 | Máquina | Mac M3 Pro, 36 GB de RAM, 1 TB interno y un disco externo USB-C de alta velocidad. |
| 3 | Infraestructura local | Docker con OrbStack (por confirmar el nombre). La infraestructura de despliegue también está en Docker. |
| 4 | Herramientas de IA | Claude Code es la principal. Codex se manejaba desde Claude, pero la licencia venció y probablemente sale. Tiene Gemini en plan básico, sin usar. |
| 5 | Modelos locales | Ollama, posiblemente con Qwen 32B (que configuró Claude). **No confirmado.** |
| 6 | Síntoma | Avisos de consumo al 90–95%, casi sin espacio para ejecutar. |
| 7 | Tipo de aviso | De memoria: macOS obliga a cerrar aplicaciones y cada terminal consume mucha memoria swap. |
| 8 | Duración de sesiones | Cada sesión de proyecto dura de 5 a 7 días. Reinicia el Mac los domingos. |
| 9 | MCP y plugins | Varios servidores de Playwright. **Falta identificar cuáles y cuántos.** |
| 10 | Plan de Claude | Plan superior ("Ultra", probablemente Max 20x) por unos 10 días más. Lo normal es Max 5x. |
| 11 | Límites de uso | Llega al límite con frecuencia y cambia a otra cuenta para seguir trabajando. |
| 12 | Ejemplo de errores | **Colombia IA:** plataforma para vender, con tableros y sistemas de medición. Lleva unos 1,5 meses con Claude y hubo unas 40 preguntas iniciales. Aun así fallan los accesos, hay errores de interfaz y clics que no responden. Otra IA (Grok, por confirmar) encontró unos 32 errores. |
| 13 | Verificación | En teoría Claude corre flujos automáticos, pero no funcionan. Fallan cosas básicas (autenticación, recuperar clave, flujos que deberían ser modulares) con errores 500. |
| 14 | Contexto y documentación | Todos los proyectos tienen `CLAUDE.md` y configuraciones, documentados también en Obsidian. La arquitectura de Colombia IA la armó Claude con Fable 5.1. |
| 15 | Stack | Drupal, por la facilidad de módulos, permisos y roles. El resto del stack está en el proyecto. |
| 16 | Despliegue | El código va por Git y el hosting es Hostinger. |
| 17 | Tipo de hosting | El usuario respondió «VPS con Docker» (opción A, que redactó Claude a partir de su memoria). **Corregido por SSH el 15-nov:** es **hosting compartido** de Hostinger (kernel CloudLinux LVE `4.18.0-553.157.1.lve.1.el8`, servidor `us-phx-web753`), sin Docker (`which docker` rc=1) y sin git en el servidor. La base de datos de producción es **SQLite**. Despliegue: `scripts/equipo/desplegar.sh` (lo lanza Claude) → puerta local (clasificador, sello y aprobación) → respaldo de la SQLite y del módulo → rsync de un solo módulo custom → `drush cr` → humo → reversión automática si falla → aviso por Telegram. No hace `updb` ni `config:import`. |
| 18 | Video de referencia | https://www.youtube.com/watch?v=CnfoCNd0QHE (resumen en la sección 1). |
| 19 | Resultado esperado | Metas medibles en 2 semanas (hasta el 29-nov-2026): (1) swap < 50 % con 4 a 6 terminales abiertas (15-nov: 90 %, 13.930/15.360 MB); (2) cero bloqueos por límite de uso sin cambiar de cuenta; (3) el QA automático de IA Colombia detecta los fallos de login, recuperar clave y errores 500 antes que el usuario. |
| 20 | Repos probados para el swap | Ninguno probado: Claude investiga. Candidato que vio en redes (grupo de Facebook «OPENCODE \| AVANZADO», 14-nov): **wici-ai/inferssd** (https://github.com/wici-ai/inferssd), 378 estrellas: corre modelos MoE más grandes que la RAM/VRAM desde el SSD, sobre llama.cpp. Primera lectura: resuelve otro problema (modelos locales que no caben). No baja el swap de Claude Code, MCP y Docker, y lo más probable es que meta más presión de E/S. Los modelos locales casi no se usan (Ollama: 0-10 llamadas por semana). Se evalúa con los datos de la sección 3. |
| 21 | Autonomía | Autonomía con lista roja en todos los proyectos. Sin preguntar: commits, PRs y despliegue a QA. Producción sin preguntar solo si pasan tests, QA y revisión. Siempre con el sí del usuario: dinero, credenciales, borrados y cambios de base de datos. |
| 22 | Video e imagen | Hoy una parte corre en local en el Mac (ejemplos: Z-Image-Turbo, 31 GB; HyperFrames con Chromium y ffmpeg). **Quiere pasar el trabajo pesado al Lenovo por SSH.** Hay que medir en la sección 3 qué corre en el Mac y cuánta memoria usa, y comprobar la GPU y la RAM del Lenovo. |
| 23 | Elección de modelo | No tiene regla para la sesión principal. Claude propone una regla por tipo de sesión y la mide en el informe semanal (`medir-uso.py`). Hoy `modelo-por-tarea.md` solo cubre a los subagentes. |
| 24 | Proyectos urgentes | Milagro Week (evento 16-18 nov, Citi NY), GROUNDRISE e IA Colombia. Las mejoras se prueban primero en estos tres. |

**Entrevista cerrada el 15-nov-2026.** Sigue la verificación de la sección 3.

---

## 3. Para verificar en la máquina

El usuario pidió que Claude se conecte a la máquina para revisar esto, o que deje todo listo para correrlo desde una terminal:

- [x] **Ollama** (15-nov 18:xx): 7 modelos instalados, 72 GB en el SSD externo. **Ninguno cargado** (`ollama ps` vacío) y el proceso ocupa 0,03 GB. Hoy no explica el swap. El «Qwen 32B» de la pregunta 5 no existe: hay qwen3.8:27b y qwen3.5:9b, sin uso.
- [x] **MCP:** 5 globales (context7, google-multi, pascal, ranki, rea) y 1 Playwright de proyecto en `~`. Hay otro `.mcp.json` en `~/proyectos/qa-agentes-iacolombia`. Cada sesión levanta 5 a 22 procesos hijos (google-multi, pascal y context-mode, uno por sesión) que suman **0,04-0,07 GB por sesión**. Los MCP no son el problema de memoria. Sí cuestan tokens: los nombres de herramientas diferidas en cada turno.
- [x] **Memoria por proceso** (4 sesiones de Claude abiertas): swap 12,8/14,3 GB; compresor 6,6 GB; memoria libre 71 %. Por grupo (RSS):
  - **OrbStack 6,4 GB:** el proceso más grande del Mac.
  - **Chromium 4,3 GB:** Google Chrome abierto (el `agent-browser` usa el perfil `ms365` con 9 procesos), aunque la regla es usar Edge.
  - **Claude Code 3,9 GB:** 0,5-1,0 GB por sesión.
  - **Edge 3,6 GB** (48 procesos), más Teams WebView 0,6 GB.
  - **Node 0,8 GB:** OpenClaw gateway 0,3 GB; 2 vigilantes `qa/_prod-*.mjs` de la plataforma de eventos, vivos.
  - Ojo: 4 `mcp-server-postgres` vienen de la config de Codex (MCP `postgres-trading`, con el trading en pausa).
- [x] **OrbStack:** límite de 6 GiB de RAM y 6 CPU. **33 contenedores encendidos.** Usan ~1,8 GB reales, pero la VM retiene los 6 GB. Hay 4 copias de la pila `rdn*` (rdn, rdn20, rdn21, rdn22: php + mariadb + nginx + mailpit cada una), más pco, actividades-ape, ape_rueda e iacol_*. **Es la palanca más grande:** apagar las pilas que no se usan y bajar el límite de OrbStack libera hasta ~6 GB.
- [x] **CLAUDE.md vs Obsidian** (subagente Haiku, solo lectura):
  - **IA Colombia:** el CLAUDE.md (27 líneas, 10-nov) no tiene la rama activa ni el dominio. Su STATE.md pesa 126 KB y conserva un «EN PAUSA» viejo en la línea 1734.
  - **GROUNDRISE:** el CLAUDE.md (28-oct) va 15 días atrás del ESTADO.md. Las especificaciones del producto se contradicen (3 % HPLC frente al pivote sin estandarizar), y `CLAUDE.local.md` nombra modelos de Ollama borrados el 10-nov. `groundrise-app` no tiene CLAUDE.md.
  - **Milagro Week:** el CLAUDE.md pesa 27,5 KB y **no está en git** (untracked); las líneas 56-193 son un log de sesiones que se carga en cada turno. Dice `apr_*` en actividades-procolombia y el STATE dice `rdn_*` en milagro-week.
- [x] **Stack de IA Colombia** (subagente Sonnet, solo lectura): Drupal 11.4.11, PHP 8.3 (local 8.3.33, producción 8.3.36), MariaDB 11.8 en local y **SQLite en producción**. El login es el formulario del core envuelto por `odin_dashboard`; en contrib solo está `login_destination`. **Hay 3 diferencias entre local y producción:**
  - **Registro:** `register` = `admin_only` en producción y `visitors_admin_approval` en local y en config/sync.
  - **Correo:** config/sync dice `iacol_mailjet`, pero el activo es `php_mail` en los dos. En local hubo 39 errores «Error sending email» entre el 2 y el 13-nov.
  - **Módulos:** iacol_cro, iacol_demo, iacol_legal e iacol_uso están en local y no en producción; producción tiene carpetas sobrantes `iacol_analytics.previo` y `.viejo`.
- [x] **Tests de IA Colombia:** 59 clases PHPUnit, 4 specs de Playwright, pytest y CI de GitLab (solo corre en MR o en push a main). Hoy: phpstan OK y pytest 114/114. Última corrida de Drupal (11-nov): 185 tests, 3 fallos. Última de Playwright (14-nov): 6 fallos, los mismos de la línea base. **No hay ningún test del formulario de login ni de recuperar clave:** Playwright entra con `drush uli` y se salta el formulario. Contra los 500 solo están el test 04 y `watchErrors` en la portada. **Esto explica el síntoma 13:** los flujos que fallan no están cubiertos.
- [x] **Producción, errores:** watchdog sin errores en la semana visible (el dblog solo guarda 1000 filas: del 9 al 15-nov). Los 157 404 son bots y los 35 403 son anónimos en /admin. `/user/login` y `/user/password` responden 200. **6 usuarios y 0 logins en 7 días:** el flujo real no se usa hace semanas, por eso no aparecen fallos. Sin verificar: que el correo de recuperar clave llegue (`php_mail` en hosting compartido) y los 500 previos al bootstrap de Drupal (no hay logs del servidor web).
- [x] Contenido y vigencia de los `CLAUDE.md` frente a la documentación de Obsidian: ver arriba.
- [ ] **Lenovo (por la pregunta 22): sin medir.** A las 17:3x respondía (LENOVO-X1.local); a las ~19:15 el SSH se colgó sin salida y después ya no se encontraba en la red («Probando IPs conocidas…»). Falta saber su GPU y RAM antes de pasarle el trabajo de video e imagen.

---

## 4. Preguntas pendientes

- Link del video de referencia.
- Resultado concreto esperado al terminar (pregunta 1, solo se respondió en parte).
- Tipo de hosting y despliegue (pregunta 17).
- Repositorios de GitHub que ya sabe que funcionaron para reducir el swap.
- Nivel de autonomía deseado: commits, PRs y despliegues automáticos, o siempre con aprobación.
- Proyectos de generación de video e imágenes: qué herramientas usan y si corren en local.
- Cómo se elige hoy entre Opus, Sonnet y Fable para cada tarea.
- Cuál es el proyecto más urgente aparte de Colombia IA.

---

## 5. Recomendaciones (15-nov-2026, a partir de lo medido en la sección 3)

Cada una va ligada a una de las metas de la pregunta 19. Todavía no se investigaron repos de GitHub para el swap: con lo medido, el swap sale de los contenedores y de los navegadores, no de algo que falte instalar.

### Meta 1: swap < 50 %
1. **Contenedores:** apagar las pilas que no se usan esta semana (4 copias `rdn*`, pco, ape…; hoy 33 encendidos) y bajar el límite de OrbStack de 6 a 4 GiB. Ahorro esperado: hasta ~6 GB. Necesita el sí del usuario sobre cuáles están en uso en otras terminales.
2. **Navegadores:** un solo navegador para la automatización (Edge, como dice la regla). Cerrar el Chrome que deja abierto `agent-browser` con el perfil `ms365` (4,3 GB) y que la skill cierre su sesión al terminar.
3. **Sesiones:** máximo 4 terminales. Al dejar un proyecto, «guarda y cierra», que ya pausa sus contenedores. El reinicio del domingo ya vacía el swap residual.

### Meta 2: cero bloqueos por límite sin cambiar de cuenta
4. **Sesiones por tarea, no por proyecto:** hoy una sesión dura 5-7 días y relee todo el contexto en cada turno (los turnos de más de 300k tokens leen el 73 % del total). Proponer: `/clear` al terminar cada tarea, con el estado guardado en STATE.md.
5. **CLAUDE.md livianos:** pasar las líneas 56-193 del CLAUDE.md de Milagro Week (27,5 KB, se carga en cada turno) a su STATE.md, y versionarlo. Actualizar los de GROUNDRISE e IA Colombia.
6. **Regla de modelo para la sesión principal:** Sonnet para desarrollo rutinario y Opus solo para arquitectura, depuración difícil y revisiones antes de producción. Se mide en `medir-uso.py`.

### Meta 3: el QA de IA Colombia detecta los fallos antes que el usuario
7. **Tests del flujo real:** Playwright que use el formulario de login (bueno y malo), recuperar clave con el correo capturado en Mailpit, y un recorrido de todas las rutas por rol comprobando que no haya 500. Que entren en la puerta de `desplegar.sh`.
8. **Correo y diferencias con producción:** activar Mailjet como interface de correo (hoy `php_mail`) y alinear el registro y los módulos de producción con config/sync.
9. **Vigía diario en producción:** login con un usuario de prueba + correo de recuperar clave que llegue; si falla, aviso por Telegram. Así el fallo aparece aunque nadie entre.

### Pendiente
- Lenovo: medir GPU y RAM antes de pasarle el trabajo de video e imagen.
### Hecho el 15-nov
- **Recomendación 3 (tope de CLAUDE.md), opción A:** 8 KB por proyecto, en `~/.claude/rules/cerrar-huecos.md`. Recortes pendientes, cada uno desde su propia sesión: concurso SEO (10,3 KB; tenía cambios sin commit de otra terminal a las 09:12 del 15-nov) y Milagro (27,5 KB, después del 18-nov).
- **Recomendación 2b (modelo de la sesión principal), opción A ampliada:** `settings.json` pasa de `claude-fable-5-2[1m]` a `opusplan` (Opus en plan mode, Sonnet al ejecutar); Sonnet y opusplan añadidos al selector de `/model`. Regla en `~/.claude/rules/modelo-por-tarea.md`, sección «Sesión principal». Pendiente: que `medir-uso.py` cuente horas de sesión por modelo.
- **Recomendación 2a (sesiones por tarea), opción A:** añadida a `~/.claude/rules/no-avisar-contexto-alto.md` («Corte por cambio de tarea»): al cerrar una tarea, estado en STATE.md y una sola línea de aviso.
- **Recomendación 1 (regla Docker por worktree):** aplicada en `~/.claude/rules/multiagente.md`, sección «Varias terminales a la vez», debajo de «Worktree propio por agente que escribe».
- Apagada la pila `rdn` del worktree `actividades-procolombia-wt/v2` (4 contenedores, `docker stop`; se arranca con `docker compose up -d` en esa carpeta). `iacol_equipo` NO se apagó: comparte red y BD con `equipo-completo`. OrbStack: 6,4 → 4,8 GB.
- **Recomendación 7 (tests del flujo real):** specs 05, 06 y 07 en `~/proyectos/qa-agentes-iacolombia`, rama `qa/flujo-login` (commits a41e756 y siguiente, sin push), con un usuario QA dedicado (`qa-login`, uid 21 local). Corridos contra 8095 (Claude volvió a correr el 05 y el 07):
  - **05, login por formulario:** 3/3 pasan (credenciales buenas, contraseña mala, logout). Control positivo: con la clave forzada a una mala, falla.
  - **06, recuperar clave: FALLA.** (1) Tras el POST la portada no muestra el aviso de confirmación: la persona no ve nada. (2) El correo no sale (`php_mail` → «Error sending email»), y la pila no tiene mailpit.
  - **07, rutas sin 500: FALLA.** De 46 rutas, `/admin/odin/cro/nuevo` da 500 (`CroEnqueuer.php:32`, «La URL de la tienda no es válida» cuando la cuenta no tiene dominio). Además es un GET sin token que encola un trabajo. `iacol_cro` solo está en local, no en producción.
  - Trampa: el core limita a 5 recuperaciones de clave por usuario cada 6 h y después descarta el envío sin avisar. El helper limpia el contador del usuario QA.
  - Falta: integrarlos en la puerta de `desplegar.sh`.

---

## 6. Cómo seguir

1. Retomar la entrevista desde la pregunta 17.
2. Conectarse a la máquina (Remote Control desde la app de escritorio de Claude) o correr un script de diagnóstico preparado para la terminal.
3. Con los datos reales, entregar las recomendaciones: memoria y swap, tokens y límites, verificación y calidad, workflows entre proyectos, y la investigación de repositorios de GitHub.
