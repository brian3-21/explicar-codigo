---
name: Explicar Código
description: Explica código y proyectos de forma ordenada, con el contexto primero y referencias reales a archivos y líneas. Úsala cuando preguntes "qué hace este proyecto", "explicame este repositorio", "cómo funciona X", "explícame este archivo o esta función", "por qué está esto aquí", "por qué me falla esto", "dónde está X", "qué riesgo tiene cambiar Y" o cualquier pregunta para entender un repositorio antes de tocarlo.
---

# Explicar Código

Explica código y proyectos siempre con el mismo esquema: **contexto → explicación → referencias**. La diferencia con una respuesta normal es que nada se suelta en el aire: cada afirmación lleva el `ruta/archivo.ext:42` del que sale.

Regla que no se negocia: **nunca expliques un fragmento aislado**. Localiza el archivo, léelo entero, rastrea quién lo usa y explica con un caso concreto.

## Paso 0 — Contexto del proyecto (obligatorio)

Antes de la primera explicación de una conversación, dedica **un solo bloque** a entender el repositorio. La lista exacta de qué mirar está en `references/analisis-proyecto.md`.

Ese bloque produce un **mapa del proyecto** compacto que se reutiliza el resto de la conversación. Si ya lo hiciste, no lo repitas: ve directo a la pregunta.

Si el repositorio es enorme y la pregunta es muy puntual, haz el análisis mínimo (qué es, stack, entrypoint, convenciones) y dilo.

## Paso 1 — Clasifica la pregunta

Cinco tipos. Cada uno tiene su formato en `references/formatos.md`. Si la pregunta es ambigua, elige el tipo principal y menciona en una línea qué parte del otro tipo también hay.

| La pregunta suena a... | Tipo | Formato |
|---|---|---|
| "qué hace este proyecto", "explícame el repo", "por dónde empiezo" | 1. Panorama | `formatos.md` §1 |
| "cómo funciona X", "qué hace esta función", "explicame este flujo" | 2. Cómo funciona | `formatos.md` §2 |
| "por qué está esto aquí", "para qué sirve esto" | 3. Por qué existe | `formatos.md` §3 |
| "me da error", "esto no funciona", "por qué falla" | 4. Por qué falla | `formatos.md` §4 |
| "dónde está X", "qué toco para cambiar Y", "es seguro editar Z" | 5. Dónde toco | `formatos.md` §5 |

Preguntas mixtas: se responde primero el tipo principal y luego se añade el secundario con su bloque correspondiente.

## Paso 2 — Localiza el código

- `glob` por nombre de archivo y `grep` por símbolo, por cadena de texto o por mensaje de error.
- Lee el archivo **completo** con `read`. Un fragmento fuera de contexto lleva a conclusiones incorrectas.
- Rastrea en las dos direcciones: **quién llama a esto** y **qué llama esto**. Incluye imports, tipos, configuración y tests.
- Si el símbolo, la carpeta o el archivo no existe, dilo. **No inventes** nombres de archivo, rutas ni números de línea.

## Paso 3 — Escribe la respuesta

Aplica el formato del tipo y estas reglas:

- Cita siempre `ruta/archivo.ext:42`. Si no sabes la línea, cita solo el archivo.
- Usa **un ejemplo concreto con datos**, no abstracciones. Reutiliza los datos que dio el usuario.
- La longitud la marca el tipo, no la complejidad del código. Dos párrafos pueden bastar.
- Responde en español. Si usas un término técnico, explícalo en la misma frase.

## Reglas de estilo

- **No empieces por dentro de la función.** Empieza por qué ese código existe en el sistema.
- **No repitas el README.** Si algo ya está documentado, enlázalo con `ruta/README.md` en una línea y sigue con lo que no está.
- **Distingue hecho de hipótesis.** Si el porqué no se puede inferir del código, dilo: «esto no se puede saber solo leyendo; la hipótesis más probable es X».
- **Nombra los riesgos.** Si algo parece frágil, acoplado o duplicado, dilo en una línea.
- **No arregles nada** salvo que te lo pidan. Explicar y modificar son intenciones distintas.

## Referencias

- `references/analisis-proyecto.md` — qué leer en el paso 0 y con qué orden.
- `references/formatos.md` — los cinco formatos con un ejemplo relleno cada uno.

