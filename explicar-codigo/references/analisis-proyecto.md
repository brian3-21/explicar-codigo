# Análisis del proyecto (paso 0)

Qué leer, en qué orden, para poder explicar cualquier cosa del repositorio después. Es un bloque único por conversación, no uno por pregunta.

## Orden de lectura

### 1. Qué dice que es
`README.md`, `docs/`, `CONTRIBUTING.md`, `AGENTS.md` o `CLAUDE.md`, el `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`.

No lo copies en la respuesta: extrae **el propósito en una frase** y la lista de entrypoints.

### 2. Cómo se ejecuta
Los scripts del manifiesto (`package.json` → `scripts`, `Makefile`, `docker-compose.yml`, `justfile`, `.github/workflows/`).

Cada comando importante se traduce a `comando → archivo:línea`. Es lo que permite responder "¿cómo arranco esto?" sin inventar.

### 3. Estructura de primer nivel
Lista los directorios raíz y **una responsabilidad por directorio**. No los nombres: qué vive ahí.

Si hay más de ~15 directorios de primer nivel, agrúpalos por capa.

### 4. Convenciones
`tsconfig.json`, `eslint`, `prettier`, `.editorconfig`, `biome.json`, y los patrones de nombres de archivo en uso (`camelCase`, `kebab-case`, `PascalCase`).

Aquí se aprende **cómo se escribe el código**, que es lo que evita recomendar algo ajeno al estilo del repo.

### 5. Variables de entorno y configuración
`.env.example`, esquemas de config, constantes en un módulo central. Apunta dónde se leen en runtime.

### 6. Qué cubren los tests
Lista los directorios de test y qué testea cada grupo. Un test casi siempre explica el porqué de un módulo mejor que el propio código.

### 7. Un recorrido real
Rastrea **un** camino principal de principio a fin: una petición HTTP, un comando de CLI, o un job. Tabla de pasos con `archivo:línea`.

Este paso es el que más valor da y el que más se olvida. Sin él, las explicaciones posteriores son theorycrafting.

## Salida: el mapa del proyecto

Cuando termines, escribe este bloque. Es corto, se reutiliza el resto de la conversación y el usuario lo lee una vez:

```text
**Qué es** — una frase.

**Stack** — lenguaje, framework, dependencias clave.

**Entrypoints** — tabla: comando o ruta → archivo:línea.

**Estructura** — tabla: carpeta → responsabilidad.

**Convenciones** — 3 a 6 viñetas con lo que hay que respetar al escribir código aquí.

**Recorrido principal** — tabla de pasos con archivo:línea.

**Configuración** — variables de entorno y dónde se leen.
```

## Reglas

- Cita `ruta/archivo.ext:42` en cada fila de las tablas.
- Si algo no se puede determinar, escribe `desconocido` en vez de suponer.
- Si el proyecto no parece corresponder a lo que el usuario preguntó, dilo antes de explicar nada.
- No hagas este análisis si el usuario solo quiere una explicación puntual de una función y ya has hecho el mapa antes en la conversación.
