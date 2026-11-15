# Diagnóstico: sistema de trabajo con IA (agente personal)

**Fecha:** 2026-11-15
**Estado:** entrevista pausada en la pregunta 17. Todavía no hay recomendaciones; se entregan cuando termine la entrevista y se verifique la máquina.

---

## 1. Objetivo del proyecto

Mejorar los procesos y workflows de trabajo con Claude para:

- Reducir los errores y la dependencia de revisión manual.
- Consumir menos recursos de la máquina, sobre todo memoria swap.
- Consumir menos tokens.
- Trabajar más rápido.
- Investigar librerías y repositorios de GitHub que reduzcan el consumo de swap.

Inspiración: un video sobre cómo Anthropic evolucionó su forma de trabajar (link pendiente).

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
| 17 | Tipo de hosting | *Pendiente:* ¿VPS con Docker o compartido? ¿Despliegue manual (`git pull`) o automático? El usuario pidió confirmarlo en la máquina. |

---

## 3. Para verificar en la máquina

El usuario pidió que Claude se conecte a la máquina para revisar esto, o que deje todo listo para correrlo desde una terminal:

- [ ] Modelos de Ollama instalados y cargados, y cuánta RAM ocupan (`ollama list`, `ollama ps`).
- [ ] Servidores MCP configurados: cuáles y cuántos de Playwright (`claude mcp list` y la configuración global y de cada proyecto).
- [ ] Consumo de memoria por proceso con las 4 a 6 terminales abiertas (Claude Code / Node, MCP, Chromium, Docker / OrbStack).
- [ ] Recursos asignados a OrbStack y contenedores activos.
- [ ] Stack completo de Colombia IA (versión de Drupal, base de datos, módulos de autenticación).
- [ ] Estado real de los tests y flujos automáticos de Colombia IA.
- [ ] Tipo de hosting en Hostinger y método de despliegue.
- [ ] Contenido y vigencia de los `CLAUDE.md` frente a la documentación de Obsidian.

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

## 5. Cómo seguir

1. Retomar la entrevista desde la pregunta 17.
2. Conectarse a la máquina (Remote Control desde la app de escritorio de Claude) o correr un script de diagnóstico preparado para la terminal.
3. Con los datos reales, entregar las recomendaciones: memoria y swap, tokens y límites, verificación y calidad, workflows entre proyectos, y la investigación de repositorios de GitHub.
