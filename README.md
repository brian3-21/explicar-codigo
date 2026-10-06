# Explicar Código

Skill para [OpenCode](https://opencode.ai) que explica código y proyectos de forma ordenada, siempre citando archivos y líneas reales.

La mayoría de las explicaciones de código empiezan por la mecánica: qué función hace qué, línea por línea. Eso responde *cómo*, pero no ayuda a recordar ni a razonar sobre el código. Esta skill invierte el orden y siempre parte del contexto:

> **Contexto del proyecto → Clasificación de la pregunta → Explicación con referencias**

## Qué hace

Antes de responder, la skill analiza el repositorio **una vez por conversación** y produce un mapa compacto que reutiliza el resto de la sesión. Después clasifica tu pregunta en uno de cinco tipos y aplica el formato correspondiente:

| Tu pregunta | Tipo | Formato |
|---|---|---|
| «qué hace este proyecto», «por dónde empiezo» | 1. Panorama | Qué es, cómo se ejecuta, estructura, recorrido principal |
| «cómo funciona X», «qué hace esta función» | 2. Cómo funciona | Entrada, salida, recorrido paso a paso, trampas |
| «por qué está esto aquí» | 3. Por qué existe | Problema, necesidad, qué pasaría sin él, resumen |
| «me da error al guardar el pedido» | 4. Por qué falla | Síntoma, causa raíz, mecánica, cómo confirmarlo, arreglo |
| «dónde toco para cambiar el IVA» | 5. Dónde toco | Archivo y línea, radio de impacto, precauciones, verificación |

El formato **se adapta a la pregunta** en lugar de imponer siempre los mismos bloques.

### Los tres pasos

1. **Analiza el proyecto** — lee README, scripts, estructura, convenciones, variables de entorno y tests, y rastrea un recorrido real de extremo a fin. Devuelve un mapa: qué es, stack, entrypoints, estructura, convenciones, configuración.
2. **Clasifica y localiza** — decide el tipo de pregunta, encuentra el archivo con `glob` y `grep`, lo lee completo y rastrea quién lo llama y qué llama.
3. **Responde** — aplica el formato del tipo citando `ruta/archivo.ext:42` y usando un ejemplo concreto con tus datos.

### Reglas de la skill

- **Nunca explica un fragmento aislado.** Sin archivo entero, contexto y referencias, la explicación es suposición.
- **No inventa** nombres de archivo, rutas ni líneas. Si el símbolo no existe, lo dice.
- **Distingue hecho de hipótesis.** Si el porqué no se puede inferir del código, lo dice y marca la hipótesis como hipótesis.
- **No repite el README.** Si algo ya está documentado, lo enlaza y sigue con lo que no.
- **No modifica nada** salvo que se lo pidas. Explicar y editar son intenciones distintas.

## Instalación

La carpeta `explicar-codigo/` es la skill instalable.

### Opción 1 — Global (todos tus proyectos)

```bash
git clone https://github.com/brian3-21/por-que-existe.git
cp -r por-que-existe/explicar-codigo ~/.config/opencode/skills/
```

En Windows PowerShell:

```powershell
git clone https://github.com/brian3-21/por-que-existe.git
Copy-Item -Recurse "por-que-existe\explicar-codigo" "$HOME\.config\opencode\skills\"
```

### Opción 2 — Solo este proyecto

Copia la carpeta `explicar-codigo/` a `.opencode/skills/` dentro del proyecto donde quieras usarla.

### Opción 3 — Usar el repo como fuente

En `opencode.json` o `opencode.jsonc`:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "skills": ["./explicar-codigo"]
}
```

La ruta es relativa al directorio de trabajo activo de OpenCode. Si prefieres no mover archivos, añade este repo clonado a tu config global (`~/.config/opencode/opencode.json`).

## Uso

No hay comando especial. La skill se anuncia sola al modelo cuando tu pregunta coincide con su descripción, y también puedes cargarla a mano con la herramienta `skill` usando el ID `explicar-codigo`. En modo slash aparece como `/explicar-codigo`.

Preguntas que la activan:

- «Explícame este repositorio»
- «¿Qué hace `calcularIVA` en `src/domain/impuestos.ts`?»
- «¿Cómo funciona el flujo de checkout de principio a fin?»
- «¿Por qué existe este módulo?»
- «¿Por qué me salta este error al guardar el pedido?»
- «¿Dónde está el código que manda el email de confirmación?»
- «¿Qué se rompe si cambio el modelo de `Pedido`?»

## Estructura

```text
.
├── README.md
├── LICENSE
└── explicar-codigo/             ← la skill instalable
    ├── SKILL.md                 ← flujo, clasificación de preguntas, reglas de estilo
    └── references/
        ├── analisis-proyecto.md ← paso 0: qué leer y con qué orden
        └── formatos.md          ← los 5 formatos con un ejemplo relleno cada uno
```

El ID de la skill lo define la ruta (`explicar-codigo/SKILL.md` → `explicar-codigo`), no el campo `name` del frontmatter, que es solo la etiqueta visible («Explicar Código»).

## Personalización

- **Cuándo se ofrece la skill** — el campo `description` del frontmatter en `explicar-codigo/SKILL.md`.
- **Qué preguntas existen** — la tabla de la sección «Clasifica la pregunta» en `SKILL.md`. Añade o quita filas para cambiar los tipos.
- **Qué se lee del proyecto** — `references/analisis-proyecto.md`.
- **Cómo se responde** — `references/formatos.md`. Añade un bloque a un formato o cambia los ejemplos.

## Licencia

MIT — ver [LICENSE](LICENSE).
