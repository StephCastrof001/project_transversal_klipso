# Skills Transversales — Klipso

Skills de Claude Code para usar en TODOS los proyectos.
Fuente: [mattpocock/skills](https://github.com/mattpocock/skills) + workflow de [@cuevaio](https://substack.com/@cuevaio).

---

## ¿Global o por proyecto?

| Qué | Dónde |
|---|---|
| **SKILL.md** (las skills en sí) | `~/.claude/skills/` — GLOBAL, una instalación |
| **CONTEXT.md**, ADRs, PRDs | En cada REPO — documenta el dominio del proyecto |

Las skills son infraestructura. Los docs que generan son del proyecto.

---

## Instalación (una sola vez)

```bash
npx skills@latest add mattpocock/skills
```

Luego correr en cada repo nuevo:

```
/setup-matt-pocock-skills
```

Configura: issue tracker (GitHub/Linear/local), labels de triage, carpeta de docs.

---

## Skills disponibles

### Workflow principal (en orden)

| Slash command | Cuándo usar | Qué produce |
|---|---|---|
| `/grill-with-docs` | **Antes de buildear** — entrevista exhaustiva | `CONTEXT.md` + ADRs actualizados |
| `/to-prd` | Después del grill — sin re-entrevistar | PRD con user stories + módulos |
| `/to-issues` | Después del PRD | Issues verticales AFK/HITL en GitHub |
| `/tdd` | Al implementar cada issue | Red → green → refactor por slice |
| `/handoff` | Contexto ~80k tokens | Doc compacto para nueva sesión |

### Mantenimiento de backlog

| Slash command | Cuándo usar | Qué produce |
|---|---|---|
| `/triage` | Issues desordenados en backlog | Labels estado: `needs-triage / needs-info / ready-for-agent / ready-for-human / wontfix` |
| `/improve-codebase-architecture` | Post-sprint, cuando el código crece | Lista de deepening opportunities con HTML report |

### Exploración / diseño

| Slash command | Cuándo usar | Qué produce |
|---|---|---|
| `/prototype` | Antes de decidir dirección — UI o lógica | Código throwaway: terminal app o variaciones UI |
| `/ask-matt` | No sabes qué skill usar | Router — te dice qué skill encaja |

### Setup

| Slash command | Cuándo usar |
|---|---|
| `/setup-matt-pocock-skills` | Primero en cada repo nuevo — **obligatorio** |

---

## Workflow completo 

```
1. /grill-with-docs     → resolver árbol de decisiones + CONTEXT.md
2. /to-prd              → sintetizar PRD
3. Nueva sesión         → contexto limpio con PRD
4. build                → implementar
5. /handoff             → cuando llegas a ~80k tokens → nueva sesión
   repeat desde paso 3
```

**Regla de oro de Anthony:** 100k tokens no alcanzan para un producto. Todo lo importante
(decisiones, lenguaje de dominio, PRD, estilo de código) debe vivir en markdown files
que cualquier agente pueda leer en cualquier sesión.

---

## Detail de cada skill

### `/grill-with-docs`
Entrevista al usuario sobre la feature/bug antes de escribir código. Recorre el árbol
de decisiones resolviendo dependencias. Además:
- Lee `CONTEXT.md` existente para no re-definir términos
- Desafía lenguaje vago contra el glosario
- Crea ADRs para decisiones hard-to-reverse
- Actualiza `CONTEXT.md` inline durante la sesión

**vs `/grill-me`:** `/grill-with-docs` es para cuando hay codebase. `/grill-me` es para
cualquier otra cosa (sin código).

---

### `/triage`
State machine para issues. Cada issue tiene exactamente:
- **1 categoría:** `bug` | `enhancement`
- **1 estado:** `needs-triage` | `needs-info` | `ready-for-agent` | `ready-for-human` | `wontfix`

Flujos:
- Triage todo el backlog sin revisar
- Mover issue específico a nuevo estado
- Crear agent brief para issues `ready-for-agent`
- Usa `.out-of-scope/` para cerrar automáticamente issues fuera de scope

---

### `/to-prd`
Sintetiza lo que ya está en la conversación en un PRD. **No re-entrevista.**
Incluye: problem statement, solución, user stories numeradas, decisiones de implementación,
decisiones de testing, out-of-scope, módulos a tocar.

---

### `/to-issues`
Rompe el PRD en issues de GitHub con slices verticales (no horizontales).
Cada issue corta a través del stack completo (schema + API + UI + tests).
Marca cada uno como AFK (agente solo) o HITL (necesita humano).

---

### `/tdd`
Red → Green → Refactor, un comportamiento a la vez.
- Tests verifican comportamiento a través de interfaces públicas, no internals
- Rechaza horizontal TDD (muchos tests, luego muchas implementaciones)
- Cada slice: un test rojo → implementación mínima → verde → refactor

---

### `/prototype`
Código throwaway para responder una pregunta antes de comprometerse.
- **Logic prototype:** terminal app para state machines, reducers, business rules
- **UI prototype:** variaciones visuales en una route, switchables por URL param

Regla: delete the prototype once it answered the question. Keep the answer, not the scaffolding.

---

### `/improve-codebase-architecture`
Busca módulos "poco profundos" (shallow) y propone hacerlos más profundos (deep).
- Lee CONTEXT.md y ADRs primero
- Lista oportunidades numeradas con: módulos afectados, fricción actual, qué cambiaría
- HTML report con deepening opportunities

Usar después de un agent run, code review, o sprint.

---

### `/handoff`
Compact con contexto rico para nueva sesión. Define el propósito del handoff.
Genera: estado actual, trabajo restante, decisiones importantes, skills que debe usar el próximo agente.

---

## Pares de skills más útiles

| Si usas... | Úsalo con... |
|---|---|
| `/grill-with-docs` | `/to-prd` siempre después |
| `/to-prd` | `/to-issues` → descompone en tickets |
| `/to-issues` | `/tdd` → implementa cada ticket |
| `/prototype` | `/handoff` → para entrar y salir del contexto del prototype |
| `/improve-codebase-architecture` | `/to-issues` → convierte mejoras en tickets |
| `/triage` | `.out-of-scope/` → ADRs de lo que NO buildeas |

---

## Archivos que generan las skills (por proyecto)

```
mi-proyecto/
├── CONTEXT.md              # Glosario de dominio — generado por /grill-with-docs
├── docs/
│   ├── adrs/               # Architectural Decision Records
│   └── prds/               # PRDs generados por /to-prd
└── .out-of-scope/          # Features que decidimos NO buildear (para /triage)
```

---

## Fuentes

- Repo oficial: https://github.com/mattpocock/skills
- Artículos: https://www.aihero.dev/skills
- Workflow Anthony: https://cuevaio.substack.com/p/actually-building-with-ai
