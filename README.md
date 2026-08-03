# Harness — Planner → Generator → Evaluator

Tres agentes que construyen una app completa sin que tú estés en medio.
**Cero contexto compartido**: se coordinan escribiendo archivos en disco.

```
        prompt          SPEC.md           app + git
Humano ────────► Planner ───────► Generator ─────────► Evaluator
                                      ▲                    │
                                      └──── FINDINGS.md ◄───┘
                                          (hasta 3 pasadas)
```

| Agente | Qué hace | Su único output |
|---|---|---|
| **Planner** | convierte tu idea en una especificación cerrada, con un rubric de criterios verificables | `SPEC.md` |
| **Generator** | construye todo de una corrida, commiteando con git | el código |
| **Evaluator** | levanta la app y prueba cada criterio como usuario real | `FINDINGS.md` |

Si el Evaluator encuentra fallas críticas, el Generator entra en **modo fix** y solo arregla eso.
El loop se repite hasta 3 pasadas o hasta que no quede nada crítico abierto.

---

## Instalación

Párate en la carpeta de tu proyecto y corre:

```bash
curl -fsSL https://raw.githubusercontent.com/santmun/claude-code-harness/main/instalar.sh | bash
```

O si prefieres revisarlo antes de correrlo (recomendado):

```bash
git clone https://github.com/santmun/claude-code-harness
bash claude-code-harness/instalar.sh ~/mi-proyecto
```

Crea `.claude/skills/harness/` y `.claude/agents/` en tu proyecto, e inicializa git si falta.

## Uso

Abre Claude Code en la carpeta y corre:

```
/harness "construye un panel de reservas para una barbería"
```

Para continuar un build que ya tiene `SPEC.md`, corre `/harness` a secas.

---

## Por qué funciona

**El que construye no es el que juzga.** El Generator no evalúa su propio trabajo — un agente
revisando lo que acaba de escribir tiende a darse el visto bueno. El Evaluator llega con contexto
limpio y solo puede reportar, nunca arreglar.

**Toda la coordinación pasa por archivos.** No hay contexto compartido entre agentes. Lo que un
agente necesita saber, lo lee del disco. Eso hace el sistema reproducible y depurable: puedes abrir
`SPEC.md` y `FINDINGS.md` y entender exactamente qué pasó.

**El rubric define "terminado".** El Planner escribe criterios binarios y comprobables
("el form rechaza email inválido", "GET /api/x devuelve 200"). Sin eso, el loop no sabe cuándo parar.

## Requisitos

- Claude Code
- git (el instalador lo inicializa si falta)
- Playwright *(recomendado)* — para que el Evaluator pruebe la interfaz como usuario real.
  Sin él, verifica por `curl` y deja constancia de lo que no pudo probar en UI.

## Archivos que genera en tu proyecto

| Archivo | Quién lo escribe | Para qué |
|---|---|---|
| `SPEC.md` | Planner | la especificación + el rubric |
| `FINDINGS.md` | Evaluator | qué falla, con evidencia y severidad |
| `PLAN.md` | tú *(opcional)* | contexto de negocio que el Planner leerá |

---

@sanmunoz.ia · Horizontes IA
