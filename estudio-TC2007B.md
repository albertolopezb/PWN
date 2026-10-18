# TC2007B · Hoja del día del examen (guías abiertas)

Opción múltiple, 4 opciones, 1 correcta. Idea central de todas las guías: **la conclusión nunca debe superar la evidencia** (casi todo "garantiza / siempre / solo" es falso).

---

## 1. Método rápido por pregunta (15–30 s)

1. Ubica el tema → guía (tabla de la sección 3).
2. **Califica cada opción con la tabla de puntos (sección 2). La de MENOR puntaje es la más probable.**
3. Si dos quedan cerca (diferencia ≤ 3): Ctrl+F de una palabra rara de la pregunta en esa guía y compara con «Respuestas posibles» / tabla de errores / vocabulario.
4. Desconfía de una opción que **invierta una definición** (DI ↔ inversión, autenticación ↔ autorización, remember ↔ ViewModel).
5. La correcta suele ser paráfrasis de una frase de la guía: busca la frase, no la pregunta.

---

## 2. Tabla de puntos (menor puntaje = más probable la correcta)

Versión de una línea (para la barra de direcciones):

```
MENOR=correcta | +5 c/absoluto(solo siempre todo nunca garantiza basta) +6 porque +8 demuestra/asegura +3 debe/empieza no | -3 c/coma -1 y/puede/según -7 no garantiza/no basta/no siempre -8 o
```

**SUMAN (señal de opción incorrecta)**

| Qué ves | Puntos | Cómo contar |
|---|---|---|
| Absolutos: siempre, nunca, automáticamente, garantiza (y terminaciones), imposible, totalmente, completamente, cualquier, todo/toda/todos/todas, ningún/ninguno/ninguna, solo, solamente, únicamente, exclusivamente, basta, nada, único/única | **+5** | cada aparición |
| Causa: porque, ya que, puesto que | **+6** | una vez por opción |
| Empieza por no, nunca, nada, ningún/ninguno/ninguna | **+3** | una vez |
| Obligación: debe, tiene que, es obligatorio, es necesario | **+3** | una vez por opción |
| Conclusión: demuestra, comprueba, asegura, elimina | **+8** | una vez por opción |

**RESTAN (señal de opción correcta)**

| Qué ves | Puntos | Cómo contar |
|---|---|---|
| Coma o punto y coma (no cuentan comas entre cifras) | **−3** | cada signo |
| La palabra completa "y" | **−1** | una vez por opción |
| Matiz: puede/pueden, podría/podrían, depende, según, suele/suelen, conviene, no basta, no garantiza (y terminaciones) | **−1** | una vez por opción |
| Límite: no garantiza, no basta, no necesariamente, no implica, no demuestra, no equivale, no siempre, no asegura (también "no se" + esas formas) | **−7** | una vez por opción |
| La palabra completa "o" | **−8** | una vez por opción |

Reglas: se acumulan (incluso en negaciones: "no garantiza" suma +5 por *garantiza*, resta −1 por matiz y −7 por límite). No cuentes la "o" dentro de "solo" ni la "y" dentro de otra palabra. Cada familia se aplica **una sola vez** aunque aparezcan varias de sus expresiones (salvo absolutos y comas, que cuentan cada aparición). Las demás palabras son literales.

**Comprobado contra las 218 preguntas del examen de entrenamiento:** la opción de menor puntaje era la correcta en **170/218 (78 %)**, y empataba como la más baja en otras 19 (≈ **82 %** contando los empates a la suerte). Puntaje medio: correctas **−5.7**, incorrectas **+3.0**.
Cuidado: esas reglas parecen ajustadas a ese mismo banco; el final puede escribirse distinto. Úsalo como filtro, **no** como regla única: si una opción de menor puntaje contradice claramente la guía, gana la guía.

---

## 3. ¿Qué guía y qué buscar? (Ctrl+F dentro de esa guía)

| Tema de la pregunta | Guía | Ctrl+F |
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
| "¿qué garantiza / sobrevive / demuestra?" | Síntesis | `no garantiza`, tablas 2, 4 y 5 |

En cada guía, las secciones que más rinden: **Errores frecuentes** (Síntoma → Causa → Qué revisar), **Vocabulario**, **Conexión transversal** y **Respuestas posibles**.

---

## 4. Lo esencial por guía (una línea por idea)

**02 Arquitectura/estado/navegación**
- UI dibuja y emite eventos · ViewModel estado de pantalla · Dominio reglas sin Android · Datos repositorio/BD. UDF: estado baja, eventos suben.
- Compose solo observa estado observable. `MutableList` + `.add()` no notifica → lista inmutable reasignada o `mutableStateListOf`. Estado derivado: se calcula, no se duplica.
- remember (recomposición) < rememberSaveable (rotación) < ViewModel (rotación, no muerte de proceso) < Room/DataStore (todo). SavedStateHandle: valores pequeños.
- Dos ViewModels con propietarios distintos no comparten datos → repositorio común o propietario compartido (destino / grafo padre / Activity).
- Navegación: plantilla `x/{id}` vs ruta `x/7`; nombre y `NavType` deben coincidir; `launchSingleTop` / `popUpTo`; callbacks en vez de pasar `NavController`.

**03 Corrutinas**
- Suspender ≠ bloquear; concurrencia ≠ paralelismo. `launch` → Job; `async` → Deferred + `await`. Dos `suspend` seguidas = secuencial.
- Cancelación cooperativa; **no tragar `CancellationException`**. `LaunchedEffect` se repite según sus claves. `viewModelScope` vive con el ViewModel.

**04 REST/Retrofit**
- DTO ≠ dominio (mapeador). `baseUrl` termina en `/`. Estados: cargando / éxito / éxito vacío / error.
- `IOException` = red; `HttpException` = HTTP no exitoso. GET suele reintentarse; POST puede duplicar → idempotencia **si la API la soporta**. Ocultar un botón no es autorización.

**05 Room/flujos**
- Entity / DAO / Database + KSP. Un Flow del DAO persiste por Room, no por ser Flow. `stateIn(WhileSubscribed)` no borra datos. Inicial ≠ vacío. Sin migración → fallo o pérdida.

**06 Autenticación**
- Autenticación = quién; autorización = qué (decide el servidor). Access (corto) vs refresh. JWT no implica cifrado.
- 401 login → corregir · 401 access vencido → refresh · 401 en refresh → invalidar sesión · 403 → informar, no reintentar a ciegas.
- Tokens sin cifrar en DataStore = legibles; Keystore + AES-GCM. Refresh concurrente con rotación → exclusión mutua. Rol local no es seguridad.

**07 Diseño/accesibilidad**
- Token semántico (`primary`/`onPrimary`), no valores literales. Modifier al elemento raíz. `weight` para texto grande. Preview y Compose test no acreditan TalkBack completo.

**08 Tiempo real/segundo plano**
- SSE unidireccional; `callbackFlow` + `awaitClose` libera. `LifecycleStartEffect` para la conexión. `Last-Event-ID` solo si el servidor guarda historial.
- Notificación: canal + permiso. WorkManager **no es exacto** (Doze); `enqueueUniquePeriodicWork` evita duplicados.

**09 Hilt**
- DI = recibir colaboradores (puede ser manual). Inversión = principio; DI = técnica. `@Binds` interfaz→implementación; `@Provides` construir lo ajeno.
- `@InstallIn` (dónde) ≠ `@Singleton` (una instancia). `MissingBinding` → corrige tu módulo.

**10 Imágenes/cámara**
- Coil con auth necesita su OkHttp/interceptor. `content://` con permiso temporal. Bitmap pesa ancho × alto × bytes, no el JPEG → `inSampleSize`. EXIF: orientación y privacidad. `@Multipart`. 413 tamaño, 422 contrato. Subir imagen + crear registro = dos operaciones.

**11 Pruebas**
- Local (JVM) vs instrumentada. Reloj inyectado. Un fake **no** prueba el servidor real. `runTest`. Mutante sobreviviente = prueba no lo detecta. Cobertura = código ejecutado, no corrección.

**12 WebSocket**
- Polling / SSE / WebSocket según necesidad. Handshake (Upgrade); 403 = credencial. `StateFlow` estado de conexión, `SharedFlow` eventos.
- Socket viejo: comparar `webSocket !== actual` (`@Volatile` ≠ atomicidad). Backoff exponencial + jitter con límite. `send()` sin error no prueba que se guardó.

---

## 5. Tablas de la síntesis (las más preguntables)

**Qué sobrevive:** remember (recomposición) · ViewModel (+ rotación) · rememberSaveable (+ rotación, no garantizado tras muerte de proceso) · SavedStateHandle (valores pequeños) · Room/DataStore (disco).

**Error → respuesta:** IOException → reintentar con política · 401 login → corregir · 401 access vencido → refresh · 401 refresh → invalidar sesión · 403 → informar · 409 → conflicto · 413/422 → corregir contenido · 429/503 → backoff · CancellationException → propagar.

**Repetición:** botón deshabilitado, filtro por ID, `notify(id)`, Last-Event-ID, clave de idempotencia: cada uno protege algo distinto y **ninguno garantiza exactly-once**.

**Qué demuestra cada prueba:** unitaria = la regla ejercitada · fake = comportamiento con dependencias simuladas, no el servicio real · instrumentada = código Android incluido · Compose test = semántica seleccionada · integración = piezas reales incluidas · E2E = un recorrido · mutación = sensibilidad a mutaciones concretas.

**Seguridad:** sesión → token → HTTP → caché → UI; autoriza el servidor; cifrar token no limpia caché ni logs. QR con URL válida = solo formato.

---

## 6. Temas probables que NO salieron en el banco

Repasa estas en las guías: **02** recomposición, estado derivado · **03** bloqueo vs suspensión, `Deferred`, cancelación cooperativa · **04** IOException vs HttpException, interceptor, mapeador, actualización optimista · **05** KSP, StateFlow · **06** claim, bearer, AES-GCM, modelo de amenazas · **07** token semántico, objetivo táctil · **08** Doze, idempotencia · **09** `@HiltViewModel`, `@Module`, dependencia circular · **10** ImageLoader, ContentResolver, TakePicture · **11** valor límite, determinismo, mutante sobreviviente · **12** Upgrade, protocolo de aplicación, interfaz sellada, túnel.
