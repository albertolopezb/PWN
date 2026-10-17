# TC2007B · Plan de examen final (guías abiertas)

Basado en lo que leí de startdroid.com: las 11 guías (02–12), la síntesis transversal y el banco de 218 preguntas del examen de entrenamiento (con sus 3 distractores cada una).

---

## 0. Cómo es el examen (deducido de los datos)

- El banco de entrenamiento = las preguntas de **autoevaluación** de cada guía (15–20 por guía, 5 de la síntesis). Correcta = "Respuesta posible"; las otras 3 = "errores frecuentes".
- Cada guía tiene 3 tipos de pregunta: **Comprensión**, **Interpretación de código**, **Aplicación y diagnóstico**. El final traerá lo mismo con otros casos.
- Cada guía declara "Lo imprescindible para el examen" y dice: *"Se evalúa la capacidad de explicar, interpretar fragmentos y justificar decisiones"* y *"Salvo indicación expresa, no se exige memorizar nombres, rutas, versiones, intervalos, tamaños"*. **No estudies datos exactos; estudia el porqué.**
- Principio que se repite en toda la colección: **«la conclusión nunca debe superar la evidencia»**. Casi todo "garantiza / siempre / solo" es falso.

---

## 1. Conocimientos indispensables (resumen)

### Guía 02 · Arquitectura, estado y navegación (Compose)
- **Capas:** UI (dibuja, emite eventos) · ViewModel (estado de pantalla, atiende eventos) · Dominio (reglas, sin Android) · Datos (repositorio/BD). Es una forma de organizar responsabilidades, no 4 carpetas obligatorias.
- **UDF/MVVM:** estado baja, eventos suben. **Fuente única de verdad** evita copias que discrepan.
- **Compose solo recompone si el estado es observable.** `mutableStateOf(mutableListOf())` + `.add()` NO notifica → usar lista inmutable reemplazada (`products = products + "B"`) o `mutableStateListOf`.
- **Qué sobrevive:** `remember` (recomposición) < `rememberSaveable` (rotación, valores guardables) < `ViewModel` (rotación, NO muerte de proceso) < `Room/DataStore` (todo, está en disco). `SavedStateHandle` = valores pequeños.
- **State hoisting** cambia el dueño del estado; no lo hace sobrevivir a recreaciones.
- Estado derivado: calcularlo (total = qty × precio), no guardarlo duplicado.
- **Navegación:** callbacks (`onItemClick`) en vez de pasar `NavController` a la UI; `launchSingleTop`/`popUpTo` evitan pilas repetidas; el nombre del argumento y su `NavType` deben coincidir.
- `private set` = solo el ViewModel modifica el estado.

### Guía 03 · Corrutinas
- **Suspensión ≠ bloqueo.** Suspender libera el hilo; `Thread.sleep` lo bloquea. **Concurrencia ≠ paralelismo.**
- `launch` → `Job` (no devuelve resultado, no espera). `async` → `Deferred<T>` + `await()`. `join()` espera un Job.
- Dos `suspend` seguidas = **secuenciales** (suman tiempos). Para concurrencia: lanzar los `async` primero y hacer `await` después.
- **Concurrencia estructurada:** `coroutineScope` espera a sus hijos; si uno falla/cancela, se coordina.
- **Cancelación cooperativa:** requiere puntos suspendibles o comprobar el estado. **Nunca tragarse `CancellationException`** en `catch (Exception)`.
- Ámbitos: `viewModelScope` (vive con el ViewModel), `rememberCoroutineScope` / `LaunchedEffect` (vive con la composición y sus **claves**). Trabajo lanzado directo en composición se repite en cada recomposición.
- Dispatchers: `Main` UI, `IO` red/disco, `Default` CPU. **Main-safe** = se puede llamar desde Main sin bloquear.

### Guía 04 · API REST y Retrofit
- Retrofit describe el contrato HTTP; OkHttp es el cliente. **DTO** (forma del JSON) ≠ **dominio**; el **mapeador** los convierte. **Repositorio** aísla el origen de datos.
- `baseUrl` termina en `/`. Falta `INTERNET` → error de permiso.
- **LoadState/UiState:** distinguir **cargando / éxito / éxito vacío / error**. "Vacío para siempre" = no se separaron.
- `IOException` = red/transporte; `HttpException` = respuesta HTTP no exitosa.
- **Reintentos:** GET suele ser seguro; POST puede duplicar si la respuesta se perdió. Botón deshabilitado ≠ idempotencia. Solución: clave de idempotencia **si la API la soporta**.
- **Autorización en el cliente (ocultar botón) no es seguridad;** el servidor valida.
- Navegar antes de la confirmación = formulario desaparece tras error.

### Guía 05 · Room y flujos
- **SQLite** motor; **Room** capa tipada encima: `Entity` (tabla), `DAO` (consultas), `Database`. Requiere **KSP**.
- Un `Flow<List<Entidad>>` del DAO es observable; **persiste por Room, no por ser Flow**.
- `StateFlow` en ViewModel = estado actual; `stateIn(WhileSubscribed)` controla la suscripción, no borra datos. `collectAsStateWithLifecycle` respeta el ciclo de vida.
- Estado inicial ≠ lista vacía (null mezcla "no cargó" con "no hay").
- **Migraciones:** cambio de esquema sin migración → crash o pérdida (migración destructiva).
- Entity ≠ modelo de dominio ≠ DTO. DataStore = preferencias/objetos pequeños, no tablas.

### Guía 06 · Autenticación y sesión
- **Autenticación** = quién eres; **autorización** = qué puedes hacer (decide el servidor).
- **Access token** (corto, accede a recursos) vs **refresh token** (pide nuevos). **JWT** = formato con claims; puede estar firmado, **no implica cifrado**. Bearer = quien lo tenga lo usa.
- **Códigos:** 401 en login = credenciales malas (no refrescar) · 401 con access vencido = refresh controlado · 401 en refresh = sesión inválida · 403 = sin permiso (no reintentar a ciegas).
- Sesión como **estado observable** compartido (si no, "login correcto pero sigue en login").
- Tokens en DataStore **sin cifrar** = legibles; **Android Keystore** guarda claves no exportables, **AES-GCM** cifra con integridad. Cifrar no protege caché ni logs.
- Refresh concurrente con **rotación** → exclusión mutua (si no, se invalida la sesión). Authenticator vs Interceptor. Logout debe limpiar local y (si es posible) revocar en servidor.
- Roles locales no son seguridad: el servidor responde 403.

### Guía 07 · Material 3, diseño y accesibilidad
- **Token semántico** = nombrado por función (`primary`, `onPrimary`), no por apariencia. Evita valores literales dispersos.
- `ColorScheme`, tipografía (escala), `Shapes`, espaciado centralizados en el tema; `CompositionLocal` para tema personalizado.
- **Modifier:** aplicar al elemento raíz y respetar el modifier recibido; el orden importa.
- Accesibilidad **comprobable**: contraste (pares `primary/onPrimary`), texto ampliado (usar `weight`, no anchos fijos), `contentDescription`, objetivos táctiles, `imePadding`.
- `@Preview` y Compose UI Test no acreditan TalkBack completo. Paleta de marca vs Material You son **decisiones**, el principio es adaptarse.

### Guía 08 · Tiempo real, notificaciones, segundo plano
- **Pull** (polling) vs **Push** (servidor inicia). **SSE** = unidireccional sobre HTTP largo; WebSocket = bidireccional.
- `callbackFlow` adapta callbacks → Flow; **`awaitClose` libera recursos**.
- Conexión ligada a la visibilidad: `LifecycleStartEffect`. Desconexión periódica: `readTimeout` menor que el heartbeat.
- `Last-Event-ID` reanuda **solo si** el servidor conserva historial. Duplicados: ID único. `notify(id, …)` reemplaza la del mismo id.
- **Notificaciones:** necesitan canal y permiso; `PendingIntent` = acción posterior del sistema.
- **WorkManager:** trabajo diferible, persistente, **no exacto** (Doze, restricciones); `enqueueUniquePeriodicWork` evita workers repetidos. "Forzar detención" lo impide por diseño.

### Guía 09 · Inyección de dependencias y Hilt
- **DI** = recibir colaboradores desde fuera. **Inversión de dependencias** = principio (depender de abstracciones). DI es la técnica. Se puede hacer **manual** (contenedor).
- No toda clase necesita interfaz. DI no vuelve testeable por sí sola.
- Hilt: `@HiltAndroidApp` (Application), `@AndroidEntryPoint` (Activity), `@HiltViewModel`/`hiltViewModel`, `@Inject` (constructor).
- **`@Binds`** = interfaz → implementación (abstracto). **`@Provides`** = construir lo que no controlas (Retrofit, Room).
- **`@InstallIn` = dónde vive el binding; `@Singleton` = una sola instancia.** No son lo mismo.
- Errores: `MissingBinding` (falta @Binds/@Provides), `DuplicateBindings`, dependencia circular. Se corrige tu código, no el generado.
- Hilt no hace persistir datos en memoria ni cambia el ciclo de vida del ViewModel.

### Guía 10 · Imágenes, cámara, archivos
- **Coil** carga/cachea; si la imagen requiere auth, su `ImageLoader` debe usar un OkHttp con interceptor (si no, 401).
- `content://` **URI** = referencia con permiso temporal; puede dejar de abrir tras restaurar. `FileProvider` (autoridad + XML de rutas), `TakePicture`, Photo Picker.
- **Memoria:** el bitmap pesa `ancho × alto × bytes/pixel`, no el tamaño del JPEG. `inJustDecodeBounds` + `inSampleSize` evitan `OutOfMemory`.
- **EXIF:** orientación (si se ignora, foto rotada) y **privacidad** (GPS).
- **Multipart** requiere `@Multipart`/`@Part`. 413 = demasiado grande; 422 = contrato.
- Subir imagen + crear registro = **dos operaciones**, no transacción (huérfanos/duplicados).
- `SavedStateHandle`/`rememberSaveable`: valores pequeños, no archivos; nunca un Bitmap.
- Caché puede mostrar imagen privada tras logout/cambio de cuenta.

### Guía 11 · Pruebas
- **Local (JVM)** vs **instrumentada (Android real)**. AAA. **Valores límite.**
- **Determinismo:** inyectar reloj (`ahora`), no leer la hora.
- **Fake** = implementación simple del contrato; la prueba con fake **no** demuestra que el servidor real funcione.
- Corrutinas: `runTest`, dispatcher de prueba, `advanceUntilIdle`/`runCurrent`.
- Compose UI Test inspecciona **semántica**; no prueba TalkBack completo.
- **Mutación:** cambia el código a propósito; **mutante sobreviviente** = pruebas no lo detectaron (o es equivalente). **Cobertura = código ejecutado, no corrección.**
- Una prueba verde puede estar mal (aserción incompleta): verla fallar a propósito.

### Guía 12 · WebSocket y servidor propio
- Elegir: polling (cambia poco), SSE (unidireccional), WebSocket (bidireccional, baja latencia).
- **Handshake** (HTTP **Upgrade**). 403 en handshake = credencial. Autenticar/autorizar al abrir.
- **Protocolo de aplicación** tipado (interfaz sellada). `StateFlow` para estado de conexión; `SharedFlow` para eventos (se pierden sin colector).
- **Socket obsoleto:** callback viejo (`onClosed`) cambia el estado de la conexión nueva → comparar `webSocket !== actual`; `@Volatile` da visibilidad, **no atomicidad**.
- **Reconexión con backoff exponencial + jitter**, con límite y cancelación.
- Reconectar y agregar historial → duplicados (reemplazar/deduplicar por id). Mensaje local sin confirmación del servidor = optimista.
- `send()` sin excepción no prueba que se guardó; eco del servidor = confirmación acordada. Difundir ≠ exactly-once.
- Túnel: URL temporal cambia → nueva invitación. QR bien formado solo demuestra formato.

### Síntesis transversal (hay 5 preguntas del banco aquí; son las más "de razonar")
- **Intención / estado / evento / fuente observable.** Tras volver a la pantalla: ¿reconstruyo estado, recibo un evento histórico o consulto la fuente?
- **Qué sobrevive** (tabla de la guía): remember, ViewModel, rememberSaveable, SavedStateHandle, Room/DataStore, @Singleton.
- **Errores y respuesta:** IOException→reintentar con política · 401 login→corregir · 401 access vencido→refresh · 401 en refresh→invalidar sesión · 403→informar · 409→conflicto · 413/422→corregir contenido · 429/503→backoff · CancellationException→propagar.
- **Repetición:** botón deshabilitado, filtrar por ID, `notify(id)`, Last-Event-ID, clave de idempotencia: cada uno protege algo distinto y **ninguno garantiza exactly-once**.
- **Qué afirma cada prueba** (unitaria, fake, instrumentada, UI, integración, E2E, mutación) y qué NO.
- **Seguridad por capas:** sesión → token → HTTP → caché → UI. Autorización en el servidor.
- **Principio vs elección** (ej.: principio = liberar recursos; elección = `LifecycleStartEffect`).

---

## 2. Tips para buscar con las guías abiertas

### Reglas de oro
1. **Primero identifica la guía por el tema**, luego Ctrl+F **dentro de esa guía** (11 guías pesan ~30–100 KB cada una; buscar en la equivocada hace perder tiempo).
2. En cada guía, ve directo a estas 3 secciones (casi toda pregunta sale de ahí):
   - **«Preguntas de autoevaluación» + «Respuestas posibles»** (así se construyó todo el banco).
   - **«Errores frecuentes / Diagnóstico»** (tabla Síntoma → Causa → Qué revisar): las preguntas "Aplicación y diagnóstico" se resuelven aquí.
   - **«Vocabulario esencial»** y **«Conexión transversal»** (matices tipo "no garantiza").
3. Para "Interpretación de código": busca el **nombre de la anotación/función** (`@Binds`, `awaitClose`, `inSampleSize`) con Ctrl+F; el código está en los apartados numerados.
4. Si la pregunta es de razonamiento sobre garantías, busca en la **Síntesis transversal**.

### Qué buscar según la pregunta (guía → palabras clave)
| Si la pregunta habla de… | Guía | Ctrl+F |
|---|---|---|
| Recomposición, rotación, ViewModel, NavController | 02 | `rememberSaveable`, `launchSingleTop`, `fuente única` |
| suspend, launch, async, cancelación, dispatcher | 03 | `await`, `coroutineScope`, `CancellationException`, `main-safe` |
| Retrofit, DTO, 4xx/5xx, estados de carga, reintento | 04 | `LoadState`, `IOException`, `idempot`, `baseUrl` |
| Room, DAO, Flow, StateFlow, migración | 05 | `stateIn`, `no such column`, `migración`, `WhileSubscribed` |
| Tokens, JWT, 401/403, Keystore, logout | 06 | `refresh`, `rotación`, `Keystore`, `Authenticator` |
| Material 3, tema, contraste, accesibilidad | 07 | `onPrimary`, `semántico`, `contentDescription`, `weight` |
| SSE, notificaciones, WorkManager, Doze | 08 | `awaitClose`, `Last-Event-ID`, `PendingIntent`, `enqueueUnique` |
| Hilt, @Binds/@Provides, MissingBinding | 09 | `@InstallIn`, `@Singleton`, `MissingBinding`, `contenedor` |
| Cámara, URI, bitmap, EXIF, multipart, Coil | 10 | `FileProvider`, `inSampleSize`, `EXIF`, `@Multipart`, `ImageLoader` |
| JUnit, fakes, runTest, Compose test, mutación | 11 | `fake`, `runTest`, `mutante`, `cobertura`, `ahora` |
| WebSocket, handshake, reconexión, socket viejo | 12 | `!==`, `backoff`, `handshake`, `eco`, `túnel` |
| "¿qué garantiza / qué sobrevive / qué demuestra?" | Síntesis | `no garantiza`, tablas 2, 4, 5 |

### Cómo reconocer la opción correcta (patrón medido en las 218 preguntas)
- Las **3 opciones malas** son de 3 tipos fijos: (a) **absoluta** ("siempre", "solo", "garantiza", "automáticamente", "por definición"), (b) **conceptos invertidos** (se cambian definiciones: DI ↔ inversión, remember ↔ ViewModel, autenticación ↔ autorización), (c) **absurda/ajena** (mezcla temas no relacionados, ej. "la inversión de dependencias pertenece a bases de datos").
- **Medido:** palabras absolutas aparecen en ~0.57 por opción mala vs ~0.08 por opción correcta (7×). "solo" aparece en 141 malas vs 8 buenas; "siempre" 50 vs 1; "automáticamente" 33 vs 0; "garantiza" 24 vs 1; "nunca" 27 vs 1.
- La correcta suele ser **matizada**: "puede", "depende", "según el contrato", "si procede", "no garantiza", "bajo ciertas condiciones". Se usan casi el doble en las correctas.
- **El largo no sirve** (la correcta es la más larga solo en 20 % de los casos, igual que al azar).
- **Método rápido (10–20 s por pregunta):**
  1. Descarta las que tengan absolutos.
  2. Descarta las que inviertan una definición (verifícalo en «Vocabulario»).
  3. De las 1–2 restantes, haz Ctrl+F de una palabra rara de la pregunta en la guía correcta y compara con «Respuestas posibles»/tabla de errores.
- El examen real no repite las preguntas, pero **la respuesta correcta sigue siendo una paráfrasis de una frase de la guía**: busca la frase, no la pregunta.

### Prácticas (avisos, inventario, charla…)
Úsalas **solo** como apoyo para entender un fragmento de código (por ejemplo `callbackFlow` o `Authenticator`). El examen está basado en las guías, que son independientes del ejemplo; y las guías avisan que los nombres/rutas de las prácticas son ilustrativos.

---

## 3. El prompt probable y qué esperar

### Reconstrucción del prompt
Casi seguro algo como:

> *«Con base en la guía de estudio NN [adjunta], genera **N** preguntas de opción múltiple (4 opciones, una correcta). Mezcla comprensión, interpretación de fragmentos de código y aplicación/diagnóstico. Las 3 incorrectas deben ser errores frecuentes y plausibles. No repitas las preguntas de autoevaluación. Que el estudiante pueda razonar y resolverlas con la guía abierta; no exijas memorizar nombres, versiones ni rutas. Cada pregunta valdrá 2 puntos.»*

Pistas: 50 preguntas × 2 pts = 100; reparto proporcional entre guías (≈ 4–5 por guía, 20 para la distribución actual de 11 guías + síntesis); interfaz por tipos "Por qué…", "Qué diferencia…", "Una app/pantalla…". Esperable: variantes por alumno con las mismas guías.

### Plantillas de pregunta más frecuentes (del banco)
- **"¿Por qué…?"** (33 de 218): ¿por qué X no garantiza Y?
- **"¿Qué diferencia hay entre A y B?"**
- **Caso:** "Una app/pantalla/prueba… ¿qué pasa y qué harías?"
- **Código:** "¿Qué riesgo presenta este fragmento? / ¿qué hace?"

### Temas que NO salieron en el banco pero sí están en las guías (candidatos a preguntas nuevas)
Calculado comparando el vocabulario de cada guía con el banco. Prepara estas respuestas en tus propias palabras y sabe en qué sección de la guía están:

| Guía | Conceptos no preguntados (probables en el examen) |
|---|---|
| 02 | **Recomposición**, **estado derivado**, ViewModelStoreOwner |
| 03 | **Bloqueo vs suspensión**, `Deferred<T>`, **cancelación cooperativa** |
| 04 | **IOException vs HttpException**, **Interceptor**, **Mapeador**, `Response<T>`, UiState/LoadState, **Actualización optimista**, CRUD, serialización |
| 05 | **KSP**, **StateFlow**, SQLite, Mapper, persistencia |
| 06 | **Claim**, **Bearer token**, **AES-GCM**, **Modelo de amenazas** |
| 07 | **Token semántico**, **paleta tonal**, Shapes, tipografía, **objetivo táctil**, componente reutilizable |
| 08 | **Doze**, **idempotencia**, ciclo de vida, WebSocket vs SSE |
| 09 | **`@HiltViewModel`**, `@Module`, **dependencia circular**, ViewModelComponent |
| 10 | **ImageLoader**, **ContentResolver**, **TakePicture**, almacén de objetos |
| 11 | **Prueba instrumentada**, **valor límite**, **determinismo**, dispatcher de prueba, `runTest`, **mutante sobreviviente**, Compose UI Test |
| 12 | **Upgrade**, **Broadcast**, **protocolo de aplicación**, **interfaz sellada**, **túnel** |

### Preguntas nuevas que pueden salir (con respuesta esperada)
1. **(02)** *Un contador no se actualiza al llamar `count++` sobre una variable normal. ¿Por qué?* → No es estado observable; Compose solo recompone al leer `mutableStateOf`/`mutableIntStateOf`.
2. **(02)** *¿Por qué `private set` en el estado del ViewModel?* → Solo el ViewModel lo modifica; centraliza validaciones.
3. **(03)** *Dos llamadas `suspend` seguidas tardan 4 s y 3 s. ¿Cuánto tardan y cómo se acorta?* → 7 s; iniciar ambos `async` primero, luego `await`; suspender no es concurrir.
4. **(03)** *Un `catch (e: Exception)` rodea una `delay`. ¿Qué problema?* → Absorbe `CancellationException`; hay que relanzarla.
5. **(03)** *Una operación se repite en cada recomposición. ¿Causa?* → Trabajo lanzado directo en composición / claves de `LaunchedEffect` inadecuadas.
6. **(04)** *La pantalla muestra vacío permanente. ¿Qué falta?* → Distinguir carga/error/éxito vacío en `LoadState`.
7. **(04)** *¿Por qué ocultar el botón "borrar" no basta?* → Autorización en el servidor.
8. **(04)** *`IOException` al enviar un POST: ¿se puede reintentar?* → Depende; quizá ya se escribió; requiere idempotencia.
9. **(05)** *La lista no se actualiza tras un insert.* → Se lee una instantánea o no se recolecta el Flow del DAO.
10. **(05)** *Subes la versión del esquema sin migración.* → Crash o pérdida de datos si es destructiva.
11. **(06)** *Un 403 con rol de docente en pantalla.* → El rol local no es autoridad; el servidor decide.
12. **(06)** *Dos peticiones refrescan a la vez.* → Rotación + sin exclusión mutua invalida sesión.
13. **(06)** *Tokens legibles en archivos.* → Preferences DataStore sin cifrar; usar Keystore/AES-GCM.
14. **(07)** *Texto ilegible en modo oscuro.* → Colores literales o par `primary/onPrimary` mal ajustado.
15. **(07)** *La letra grande rompe una fila.* → Anchos fijos; usar `weight`/truncamiento.
16. **(08)** *Eventos duplicados tras reconectar.* → Cursor mal, múltiples colectores; deduplicar por ID.
17. **(08)** *El worker no corre a los 15 min exactos.* → WorkManager es diferible, no exacto (Doze, restricciones).
18. **(08)** *No aparece una notificación.* → Canal inexistente, permiso denegado o notificaciones deshabilitadas.
19. **(09)** *`MissingBinding` de una interfaz.* → Falta `@Binds`/`@Provides`; se corrige tu módulo, no el código generado.
20. **(09)** *Dos implementaciones de la misma interfaz.* → Hilt necesita un vínculo explícito; si hay provisiones en conflicto, `DuplicateBindings`. Hilt no elige por ti.
21. **(10)** *OutOfMemory al subir foto de 3 MB.* → Bitmap completo; `inJustDecodeBounds` + `inSampleSize`.
22. **(10)** *Foto rotada tras subir.* → Se ignoró EXIF.
23. **(10)** *Imagen privada visible tras cerrar sesión.* → Caché local; revisar política de caché.
24. **(11)** *Prueba con fake pasa; en producción falla.* → Fake no verifica API real/red.
25. **(11)** *Un mutante sobrevive.* → Falta aserción o entrada que active el defecto (o es equivalente).
26. **(11)** *La prueba depende de la hora del día.* → Inyectar el reloj.
27. **(12)** *Callback `onClosed` del socket viejo pone "Desconectado".* → Guardia `webSocket !== actual`; `@Volatile` no basta.
28. **(12)** *Reconexión cada milisegundo.* → Backoff exponencial con jitter, límite y cancelación.
29. **(12)** *Mensaje aparece local pero no en historial.* → Actualización optimista sin confirmación del servidor.

### Cómo preparar el resto del tiempo (si solo tienes 1–2 h)
1. 20 min: lee la **Síntesis** completa (5 min de lectura) y sus 5 preguntas.
2. 40 min: haz **un intento del examen de entrenamiento** y revisa solo los errores con su enlace a la guía.
3. 20 min: para cada guía lee «Lo imprescindible», «Errores frecuentes» y «Vocabulario» (no el resto).
4. Abre el día del examen: la tabla de la sección 2 y esta lista.
