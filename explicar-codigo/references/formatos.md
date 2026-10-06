# Formatos de respuesta

Cinco formatos, uno por tipo de pregunta. Cada uno es **una estructura, no una jaula**: si la pregunta es más simple de lo que el formato sugiere, se recorta.

Todos los ejemplos son inventados y están para copiar el tono, no el contenido.

---

## §1 Panorama

**Cuándo**: «qué hace este proyecto», «explícame el repo», «por dónde empiezo», «de qué va esto».

Si ya hiciste el paso 0, este formato es el mapa del proyecto aplicado a una pregunta concreta.

### Bloques

1. **Qué es** — una frase.
2. **Cómo se ejecuta** — tabla `comando o ruta → archivo:línea`.
3. **Estructura** — tabla `carpeta → responsabilidad`.
4. **Recorrido principal** — tabla de pasos con `archivo:línea`, entrada → salida.
5. **Convenciones** — 3 a 6 viñetas de lo que hay que respetar antes de escribir código aquí.
6. **Dónde tocar** — 3 bullets: «para cambiar X, mira `archivo.ext:12`».

### Ejemplo relleno

> 1. **Qué es** — API HTTP que recibe pedidos y los sincroniza con un ERP.
>
> 2. **Cómo se ejecuta**
>
> | | |
> |---|---|
> | `npm run dev` | `src/server.ts:12` |
> | `POST /pedidos` | `src/routes/pedidos.ts:8` |
> | `npm run sync` | `src/jobs/sync.ts:5` |
>
> 3. **Estructura**
>
> | Carpeta | Responsabilidad |
> |---|---|
> | `src/routes/` | Entrada HTTP, valida y delega |
> | `src/domain/` | Reglas de negocio, sin dependencias de HTTP |
> | `src/adapters/erp/` | Cliente del ERP, único punto que habla su protocolo |
>
> 4. **Recorrido principal** — `POST /pedidos` con `{"id": 7}`
>
> | # | Qué ocurre | Dónde |
> |---|---|---|
> | 1 | Valida el cuerpo con el esquema | `src/routes/pedidos.ts:11` |
> | 2 | Traduce el JSON a entidad interna | `src/mappers/pedido.ts:6` |
> | 3 | Guarda en la BD transaccional | `src/repos/pedidos.ts:20` |
> | 4 | Encola la sincronización | `src/jobs/cola.ts:14` |
> | 5 | Responde `202 Accepted` | `src/routes/pedidos.ts:19` |
>
> 5. **Convenciones** — entidades en `PascalCase`; DTOs con sufijo `Dto`; nada de lógica de negocio en `src/routes/`; errores siempre con `AppError`.
> 6. **Dónde tocar** — para el cálculo del IVA, `src/domain/impuestos.ts:31`; para el formato de respuesta, `src/mappers/pedido.ts:6`; para reintentos del ERP, `src/adapters/erp/cliente.ts:44`.

---

## §2 Cómo funciona

**Cuándo**: «cómo funciona X», «qué hace esta función», «explícame este flujo», «qué pasa cuando…».

### Bloques

1. **En una frase** qué hace, en lenguaje llano.
2. **Entrada / salida** — qué recibe y qué devuelve o produce.
3. **Recorrido** — tabla de pasos con el **mismo caso** que dio el usuario, `archivo:línea`.
4. **Lo que no es obvio** — 2 o 3 viñetas: trampas, casos borde, efectos secundarios, supuestos que no se ven leyendo.

### Ejemplo relleno

> **En una frase** — convierte un pedido entrante en un asiento contable y lo guarda.
>
> **Entrada**: un `PedidoDto` con líneas y cliente.
> **Salida**: un `Asiento` persistido; devuelve el número de asiento.
>
> | # | Qué ocurre | Dónde |
> |---|---|---|
> | 1 | Agrupa las líneas por cuenta contable | `src/domain/asiento.ts:18` |
> | 2 | Calcula el 21 % de IVA por línea | `src/domain/asiento.ts:27` |
> | 3 | Comprueba que cuadre debe y haber | `src/domain/asiento.ts:35` |
> | 4 | Guarda cabecera y detalle en una transacción | `src/repos/asiento.ts:12` |
>
> **Lo que no es obvio**
> - Si no cuadra, lanza `AppError` y **no** guarda nada: la transacción de `src/repos/asiento.ts:12` se deshace sola.
> - El redondeo a dos decimales ocurre **una vez**, en `src/domain/asiento.ts:27`. Si se toca, hay que tocarlo ahí y no en el mapper.
> - La función es pura salvo por el acceso a la BD: no hace llamadas HTTP, se pueden testear sin mocks.

---

## §3 Por qué existe

**Cuándo**: «por qué está esto aquí», «para qué sirve», «por qué se hizo así», «qué pasaría si no existiera».

Este es el formato con más peso: la mayoría del código existe para resolver un problema concreto, y sin esa frase el resto de la explicación flota. La regla es **el orden importa**: primero el porqué, luego qué pasaría sin él, y solo al final cómo está escrito.

### Bloques

1. **Propósito** — una frase con `ruta/archivo.ext`: qué es y para qué está.
2. **Problema que soluciona** — la situación concreta que motivó el código: qué fallaba o qué era imposible antes.
3. **Necesidad que cubre** — qué no se podría hacer, o qué sería mucho más caro, sin este código.
4. **Por qué se implementó así** — el razonamiento inferido del contexto: por qué tiene esa forma, ese nombre, esos parámetros. Comentarios, estructura y tests son la evidencia.
5. **Qué pasaría si no existiera** — con el **mismo caso** de la pregunta, en 2 o 3 puntos concretos: qué se rompe, qué habría que escribir a mano, qué duplicación aparecería.
6. **Cómo lo resuelve** — breve: una o dos frases. El recorrido detallado es el formato §2, no este.
7. **Resumen** — problema → solución → beneficio, en dos o tres líneas.

### Reglas propias de este formato

- El caso del bloque 5 es **el mismo** que dio el usuario. Si no dio datos, elige un caso mínimo y explícito, y úsalo igual en todos los bloques.
- Si el porqué no se puede inferir del código, dilo: «esto no se puede saber solo leyendo; la hipótesis más probable es X». No lo presentes como hecho.
- Si hay otra parte del proyecto que resuelve algo parecido, compáralo en una línea.

### Ejemplo relleno

> 1. `crearSesion` (`src/auth/sesion.ts:34`) existe para dejar al usuario identificado después de validar sus credenciales.
> 2. **Problema que soluciona** — el login comprobaba email y contraseña correctos pero no guardaba ningún estado posterior.
> 3. **Necesidad que cubre** — que el servidor recuerde quién está conectado sin guardar contraseñas ni volver a pedirlas.
> 4. **Por qué se implementó así** — recibe el token como parámetro en vez de leerlo de la petición porque la autenticación vive en un módulo aparte, así la función sirve tanto al login web como a un worker. Firma con `SECRET` porque el repo ya usa esa variable para las cookies.
> 5. **Qué pasaría si no existiera** — entrando en `/panel` con `token=abc123`: el login devolvería `200 OK` y nada identificable, y cada ruta protegida tendría que revalidar la contraseña o repetir la validación.
> 6. **Cómo lo resuelve** — firma el token ya validado y lo guarda en una cookie, que el navegador reenvía solo.
> 7. **Resumen** — revalidar credenciales en cada página → firmar y guardar en cookie → el servidor reconoce al usuario sin guardar su contraseña.

---

## §4 Por qué falla

**Cuándo**: «me da error X», «esto no funciona», «por qué revienta», hay un stack trace o un mensaje de error concreto.

### Bloques

1. **Síntoma** — lo que ve el usuario, citado literal.
2. **Causa raíz** — una frase. Sin rodeos.
3. **Mecánica del fallo** — tabla de 2 a 4 pasos con `archivo:línea`.
4. **Cómo confirmarlo** — comando, log o test que lo demuestra.
5. **Arreglo** — el cambio mínimo, con código. Solo si te lo piden; si no, descríbelo.
6. **Qué no hacer** — la solución que parece obvia y por qué está mal.

### Ejemplo relleno

> 1. **Síntoma** — `TypeError: Cannot read properties of undefined (reading 'importe')` en `checkout.ts:88`.
> 2. **Causa raíz** — el mapper crea la línea del pedido sin inicializar `importe`, y `totales()` lo lee asumiendo que existe.
> 3. **Mecánica**
>
> | # | Qué ocurre | Dónde |
> |---|---|---|
> | 1 | El mapper copia solo `precio` e `iva` | `src/mappers/linea.ts:9` |
> | 2 | `importe` se calcula más tarde, en el checkout | `src/domain/checkout.ts:88` |
> | 3 | Con descuentos, esa rama no se ejecuta y `importe` queda `undefined` | `src/domain/checkout.ts:74` |
>
> 4. **Cómo confirmarlo** — `pnpm test src/domain/checkout.test.ts -t "descuento"` falla con el mismo error.
> 5. **Arreglo** — inicializar `importe: 0` en `src/mappers/linea.ts:9`, o calcularlo siempre antes de `totales()`.
> 6. **Qué no hacer** — añadir `importe ?? 0` en `checkout.ts:88`: esconde el bug y devuelve ceros silenciosos cuando el cálculo falla.

---

## §5 Dónde toco

**Cuándo**: «dónde está X», «qué archivo toco para cambiar Y», «es seguro editar Z», «qué se rompe si toco esto».

### Bloques

1. **Respuesta directa** — el archivo y la línea. Primero, siempre. Sin rodeos.
2. **Radio de impacto** — tabla: qué se ve afectado y por qué.
3. **Precauciones** — lista corta: orden de migración, cosas que se rompen en silencio, tests que hay que tocar.
4. **Cómo verificar** — el comando concreto de test, lint o typecheck de este proyecto.

### Ejemplo relleno

> 1. El cálculo del IVA está en `src/domain/impuestos.ts:31` (función `calcularIVA`).
> 2. **Radio de impacto**
>
> | Se ve afectado | Por qué |
> |---|---|
> | Checkout y facturación | Ambos llaman a `calcularIVA` |
> | El informe diario | `src/jobs/informe.ts:22` reusa el resultado |
> | Test de round-trip | Fija el valor a mano, hay que actualizarlo |
>
> 3. **Precauciones** — cambiar el redondeo obliga a migrar las filas ya guardadas; el ERP espera el importe con dos decimales y rechaza de lo contrario.
> 4. **Cómo verificar** — `pnpm test src/domain` y `pnpm typecheck`.

---

## Reglas comunes a los cinco formatos

- Responde primero lo que se preguntó. Si la respuesta es una ruta, la ruta va en la primera línea.
- Si falta información para completar un bloque, **omítelo**. No lo rellenes con suposiciones.
- Una tabla cuando hay pasos o correspondencias; viñetas cuando hay opinion o advertencias.
- Máximo unas 60 líneas por respuesta. Si hace falta más, resume y ofrece el detalle aparte.
