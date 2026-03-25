# Skills utilizadas

Este documento resume las skills disponibles en `.agents/skills`, indicando **qué hace cada una** y **para qué sirve** en el flujo de trabajo del proyecto.

## 1) `find-skills`

### Qué hace
Permite buscar, descubrir y recomendar skills del ecosistema abierto de agentes usando el CLI de skills (`npx skills`). Incluye un flujo para:
- Entender la necesidad del usuario.
- Buscar skills relevantes por dominio/tarea.
- Verificar calidad (popularidad, reputación de la fuente, señales de confianza).
- Proponer instalación y uso.

### Para qué sirve
Sirve para **extender capacidades** cuando aparece una necesidad especializada (por ejemplo: testing, documentación, diseño, despliegue) y queremos encontrar una skill ya existente en vez de reinventar el flujo.

## 2) `frontend-design`

### Qué hace
Guía la creación de interfaces frontend de alta calidad visual y de producción, evitando diseños genéricos. Define principios de:
- Dirección estética clara e intencional.
- Tipografía, color, composición y movimiento con criterio.
- Diferenciación visual según contexto.
- Implementación funcional real (no solo mockups).

### Para qué sirve
Sirve para **diseñar y construir UI/UX** (componentes, páginas, dashboards, landing pages, layouts) con una estética cuidada y una identidad visual fuerte, manteniendo calidad técnica y experiencia de usuario.

## 3) `skill-creator`

### Qué hace
Es una metodología completa para crear y mejorar skills de forma iterativa. Cubre:
- Definición de intención y disparadores.
- Redacción de `SKILL.md` (estructura, frontmatter, alcance).
- Diseño de casos de prueba/evaluación.
- Ejecución comparativa (con y sin skill, o versión vieja vs nueva).
- Medición de resultados (calidad, tiempo, tokens) y mejora continua.

### Para qué sirve
Sirve para **crear nuevas skills o evolucionar existentes** con evidencia, reduciendo subjetividad. Es útil cuando queremos que una capacidad quede empaquetada, reproducible y evaluada objetivamente.

## 4) `vercel-react-best-practices`

### Qué hace
Aporta una guía extensa de optimización de performance para React y Next.js (mantenida por Vercel), organizada por categorías de impacto, como:
- Eliminación de waterfalls.
- Optimización de bundle.
- Performance server-side.
- Fetching en cliente.
- Re-renders, rendering y rendimiento de JS.

### Para qué sirve
Sirve para **escribir, revisar o refactorizar código React/Next.js** con foco en rendimiento real: menor tiempo de carga, menor costo de render, menos trabajo innecesario del cliente/servidor y mejores prácticas escalables.

## 5) `webapp-testing`

### Qué hace
Proporciona un toolkit para testear aplicaciones web locales con Playwright (scripts en Python), incluyendo:
- Interacción automatizada con UI.
- Inspección/reconocimiento del DOM renderizado.
- Captura de screenshots y logs.
- Soporte para levantar uno o varios servidores con script helper (`with_server.py`).

### Para qué sirve
Sirve para **validar comportamiento frontend**, depurar flujos visuales e identificar problemas de UI de manera automatizada, especialmente en apps dinámicas donde el estado renderizado importa.

## Resumen rápido de uso

- Se usa `find-skills` cuando necesite **buscar una capacidad nueva**.
- Se usa `frontend-design` cuando el objetivo sea **crear o mejorar la interfaz visual**.
- Se usa `skill-creator` cuando quieras **crear/medir/mejorar una skill** con método.
- Se usa `vercel-react-best-practices` cuando trabajes en **performance React/Next.js**.
- Se usa `webapp-testing` cuando necesites **probar y depurar una web app local**.
