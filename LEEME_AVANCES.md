# AVANCES CON FEABLE — v20260701z4 (lista para desplegar)

Carpeta de avances sobre la versión oficial. **El maestro y la página en producción NO fueron tocados.**

## Qué hay aquí

- **`COPIA_SEGURIDAD_1/`** — copia de seguridad 1: descargada HOY (1/7) directo de la página oficial (https://luisfc-collab.github.io/obra/). Verificada byte por byte: idéntica al maestro v20260701z3. **No tocar.**
- **`index.html` + `version.txt` + `config.js`** — la **nueva versión v20260701z4**: es la v3 activa + los 2 cambios. Sintaxis validada.
- **`SQL_ANTES_DE_DESPLEGAR.sql`** — se corre en Supabase ANTES de subir la página (agrega la columna `dias` a `metrados`; aditivo, no borra nada).

## Los 2 cambios implementados

1. **Unidad "litros"** — al definir un metrado aparece "litros" (se guarda como `L`). La base ya la acepta tal cual (texto libre), no necesita cambio.
2. **Actividades globales con días** — al elegir unidad **global (glb)**, el modal pregunta **"¿Cuántos días considerar?"** (obligatorio, mínimo 1). Con eso la actividad ya tiene duración y **se puede programar en el cronograma** ("Programarme"), igual que las demás. La lista muestra "≈ X d". El valor viaja a la base (columna nueva `dias` en `metrados`) y **se sincroniza entre todos los celulares**.

> Mejora sobre la versión de la carpeta de pruebas: ahí los días solo quedaban en el celular que los ponía. Aquí agregué el envío a la base (push) para que supervisores y admin vean los mismos días.

## Cambios exactos vs la v3 activa (8 puntos, nada más)

modal del metrado (campo `metDias`), lista de unidades (`UNITS` + litros), lista de actividades (muestra ≈ X d), `tglTot()` (muestra el campo solo si es glb), `openMetrado()` (precarga días), `metSave()` (valida y guarda días), push a la base (envía `dias`), pull de la base (lee `dias`), `diasDe()` (usa los días para programar) y versión → v20260701z4.

## Para desplegar (cuando Luis dé el OK)

1. Correr `SQL_ANTES_DE_DESPLEGAR.sql` en Supabase (SQL Editor). **Primero esto.**
2. Subir `index.html` y `version.txt` de esta carpeta al repo `LUISFC-collab/obra` (reemplazar) y confirmar commit.
3. Los celulares detectan la v4 y se actualizan solos.
4. Probar: definir un metrado en litros, y una actividad glb → poner días → verla programable en el cronograma desde otro equipo.

*(El `config.js` de aquí es el mismo de producción; normalmente no hace falta resubirlo.)*


---

## v20260701z5 (mismo día, más tarde) — paquete de la revisión general

Aplicado SOLO en FEABLE, orientado a no perder data:

1. **"Cambios por día" completo** (pedido interrumpido del 30/6): el desplegable de días ahora también cuenta y muestra **metrados, actividades nuevas y ediciones**.
2. **Corregir sin borrar (admin):** botón ✎ en el historial de cada partida; corrige cantidad/% y observación, queda registrado en `cambios`.
3. **Subida aislada por tabla:** si una tabla falla, las demás sí suben; y se mantiene la regla anti-pérdida (si algo falló, NO se baja nada de la base).
4. **Reiniciar partida (no-admin) ya no borra avances/fotos de otros supervisores.**
5. **"Invalidar TODOS" exige ser admin.**
6. **Metrado avisa "NO se guardó"** si la memoria del equipo está llena (antes fingía el ✓).
7. **Sellos de tiempo con hora del servidor** (un reloj adelantado ya no pisa datos buenos).

Verificado en vivo el 1/7: la app carga, 0 sin subir, conteo de hoy = 24 avances + 25 metrados.
Pendientes que siguen para después: aprobación de metrados (decisión de Luis), fotos de restricciones/pendientes a Storage (hoy quedan seguras en la base como texto), seguridad RLS por etapas.

---

## z6 → z10 (noche del 1/7) — pedidos de Luis probando en vivo

- **z6:** botones de Activities corregidos: la flecha ⬇ ahora descarga el resumen para mina; la casita 🏠 vuelve al inicio (Main); el documento 📄 sigue siendo el reporte del día.
- **z7:** al refrescar (F5) la app se queda en la misma pantalla (antes volvía al Main). Funciona para Activities (con su fecha), Pendientes, Programación, Bitácora, Cambios por día, Restricción total y lista admin.
- **z8:** el contador naranja de "sin subir" ya solo cuenta lo REALMENTE pendiente de subir a la base (antes marcaba 70 fantasma: los 69 metrados por un desalineo del hash con `dias` — bug mío de z4 — y 1 de programación). Además: barra del día anterior en VERDE y todos los % con su cantidad al lado, p.ej. "52% (1.4 m²)".
- **z9:** FEABLE se distingue a simple vista: encabezado y barra de datos en ROJO; pantallas con nombre: Login · Main · Activities.
- **z10:** el "1" fantasma que quedaba: el hash de programación no consideraba el campo `orden` (bug que también existe en producción). Alineado; contador verificado en 0 tras subir.

Nota: el desalineo de `orden` (z10) existe en la app OFICIAL — cuando toque pasar cambios a producción, incluirlo.

---

## z11 → z14 (noche del 1/7, segunda ronda en vivo con Luis)

- **z11 · ÚLTIMA CARGA con Mod y ✕:** al abrir una actividad, arriba del menú aparece la última carga (valor · fecha · quién). El supervisor con **Mod** propone un nuevo valor y con **✕** pide eliminarla — ambas van como SOLICITUD al admin (panel "🗑 Solicitudes", con Aceptar el cambio / Aceptar y eliminar / Denegar). Si el admin usa esos botones, aplica directo. Viaja entre celulares vía `pendientes` (payload en `coments`).
- **z11 · Conflictos:** tercera opción **"Ninguno · borrar ambas"** — elimina las dos versiones (con confirmación) y queda registrado.
- **z11 · Cambios por día claros:** "Avances reportados" con redacción "Reportó avance: +1.4 m² · por X", y las fotos etiquetadas "📷 N fotos de ESTE avance ↓" (antes flotaban y parecían del metrado).
- **z12 · Desglose del día (Activities) = SOLO metrados:** cada item muestra la cantidad en verde, su foto y su restricción si la hay. Ya no mezcla comentarios/restricciones sueltas.
- **z13 · MAIN ⎋ SALIR:** tocar la palabra MAIN cierra sesión (con confirmación). La flecha de la esquina ahora lleva a **Activities de HOY**.
- **z14 ·** Los items de configuración dicen **"CAMBIO DE METRADO → total: X unidad"** (son cambios de total/unidad, no avances).
- **z15 ·** Desglose del día con acumulado: "+1.4 m² reportados" + "Va acumulado: 1.4 de 2.7 m² (52%)" (globales: "Va acumulado: X% del 100%").
- **z16 ·** Mod de ÚLTIMA CARGA sin recuadro: el valor se edita EN LÍNEA y aparece el botón verde ➤ para enviar (solicitud al admin; si eres admin aplica directo; Enter también envía).
- **z17 · Programarme renovado:** botón ☰ "Actividades" despliega la lista FILTRADA: azul = listas (con duración), celeste = falta definir (al elegir pide los días en línea), rojo = restringidas mostrando sus restricciones. Regla: si hay azules pendientes, obliga a programarlas primero. Al elegir, la lista se oculta; al colocar en el día, reaparece. Las celdas del calendario muestran el metrado restante dividido por día ("2 m/día") y se pintan celeste/rojo según estado; el alto del día crece con lo asignado (la exportación A3 no cambia).
- **z18 ·** Botón 📄 PDF en el Desglose del día: exporta A4 con la disciplina actual, cada metrado con su acumulado, restricciones y FOTOS grandes (2 por fila, hasta media página).
- **z19-z20 · Programarme girable:** botón 🔄 en la barra: en Android gira la pantalla de verdad (pantalla completa horizontal); si el celular no lo permite (o rotación bloqueada), gira la vista por CSS igual. Al salir de Programarme vuelve solo a vertical. En vertical el panel ☰ Actividades arranca PLEGADO y compacto (máx. 42% de alto con su propio scroll); en horizontal, desplegado.
- **z21 ·** El indicador flotante de envío (engranaje/contador/check) ahora va CENTRADO arriba — ya no choca con los íconos de la esquina.
- **z22-z23 · Horizontal de verdad:** en horizontal (girado con 🔄 o por rotación real del celular) Programarme se reorganiza en DOS COLUMNAS: actividades a la izquierda (lista estática con su scroll) y calendario a la derecha. El calendario acepta PINCH de dos dedos para acercar/alejar (tope de alejamiento 0.55x, máximo 2x); la lista no hace zoom. La app detecta la resolución del equipo (res-s / res-m / res-l) y adapta ancho del panel, tamaño de letra y alto de celdas. El indicador de envío quedó centrado arriba (z21).
- **z24 ·** Cada grupo de la lista de Programarme (Listas / Falta definir / Restringidas) tiene su propia casilla desplegable ▸/▾ (Listas abierta por defecto).
- **z25 ·** Al entrar a Programarme la app pasa a PANTALLA COMPLETA (se oculta la barra del navegador en Android/Chrome; en iPhone Safari no existe esa opción — instalada como app no hay barra). En horizontal usa TODO el ancho del equipo (ya no los 440px de celular) y los perfiles res-s/m/l ajustan panel, letras y celdas.
- **z26 ·** El desplegable de Programarme muestra el RESTANTE de cada actividad: "Resta: 12 m de 20 → 6 d · rinde 2/d" (globales: "Resta programar: X% del global").
- **z27 ·** Lista con scroll propio y DIVISOR arrastrable entre lista y calendario para ampliar/reducir el panel.
- **z28 ·** Las actividades se pueden ARRASTRAR desde la lista hasta el cuadrito del día (mouse y táctil: deslizar hacia el calendario); respeta las reglas (primero las azules; las que piden días se eligen con toque).
- **z29 ·** Bloqueado el "jalar para recargar" (pull-to-refresh) en Chrome/Safari.
- **z30 ·** Cada actividad tiene un BORDE DE COLOR único a la izquierda (mismo color en lista y calendario) para reconocerla aunque todas sean azules.
- **z31 ·** MAIN limpio: solo la palabra, clic sobre ella = cerrar sesión (se quitó el "⎋ SALIR" que distorsionaba).
- **z32-z33 · Calendario regulable:** el ANCHO de los días se regula independiente del zoom: pinch con dos dedos EN HORIZONTAL (lado a lado) cambia el ancho de columnas (con scroll horizontal si no entran), pinch diagonal/vertical sigue siendo el zoom; también botones ↔− / ↔+ en la barra. Los colores de las actividades ahora van POR POSICIÓN EN EL DÍA (1.ª = verde, 2.ª naranja, 3.ª morado, …), no por actividad; la lista quedó sin bordes de color.
- **z34-z35 ·** Ajuste de ancho por LÍNEAS entre las letras de los días (D L M M J V S): agarras cerca de la línea punteada y arrastras — todas las columnas se igualan a ese ancho (mouse y táctil). Se quitaron los botones ↔. Botón ⛶ = pantalla completa con un toque (la barra del navegador solo puede ocultarse con un gesto del usuario; instalada como app no hay barra). El Mod/✕ del ADMIN REAL aplica directo aunque esté en vista supervisor (queda registrado en Cambios por día) y sus auto-solicitudes no aparecen en el panel de Solicitudes.
- **z36 ·** Las RESTRICCIONES DEL PLAN (las que venían del cronograma, p.ej. "Suministro de tablero eléctrico") ahora sí cuentan como restringidas en Programarme — verificado: Eléctrica 10, Civil 5, Mecánica 2. Aplica a lista y chips del calendario ("⚠ Del plan: ...").
- **z37 ·** La lista de Programarme va agrupada por SISTEMA dentro de cada estado (subtítulos grises).
- **z38 ·** El alto del listado se limita según el dispositivo (res-s 36% · res-m 44% · res-l 52% en vertical; columna completa en horizontal) y las actividades se deslizan dentro con su propio scroll.
- **Refresco de datos (22:53):** backup fresco de producción (621 filas — supervisores siguieron cargando) y volcado LIMPIO a FEABLE: se vaciaron las 20 tablas y se recargaron; a los 11 dispositivos del laboratorio se les envió limpieza local (cmd debug) para que no resuciten data de prueba. Verificado espejo exacto 621/621 y engranaje en 0. Los 'Conflictos (2)' que se ven son de PRODUCCIÓN (reales, de los supervisores), no errores del proceso.

---

## z39 (madrugada del 2/7) — URGENTE: prioridad POR HORA en la programación

**El problema que Luis reportó** ("se borran y se cambian de fecha en automático, lo intenté 5+ veces"):

1. Cuando alguien quitaba una actividad del cronograma, TODOS los equipos guardaban esa orden de borrado PARA SIEMPRE (lista negra que nunca se olvidaba). Si se volvía a programar, cualquier otro equipo la borraba de nuevo al sincronizar.
2. Al bajar datos, la app perdía la HORA de cada programación; al volver a subir usaba la hora del momento, y datos viejos le ganaban a los nuevos (por eso "cambiaban de fecha solas").
3. Programar/quitar no quedaba registrado como cambio.

**El fix (todo se decide por la HORA de la última acción — el último supervisor gana):**

- Cada programación viaja y se guarda con la hora real de la acción (`ts`), y esa hora se conserva al bajar datos.
- El borrado del cronograma es una "lápida" CON HORA: solo mata programaciones MÁS VIEJAS que ella (borrado condicional `ts=lt.` en la base). Si alguien re-programa DESPUÉS, la re-programación gana y la lápida se purga sola en todos los equipos.
- Las lápidas se comparten en cada subida (aunque el equipo haya estado offline al borrar) y se sueltan solas cuando ya no están en la base.
- Reordenar dentro de un día también actualiza la hora (del servidor).
- Programar y quitar quedan REGISTRADOS en Cambios por día ("Programada: N día(s) desde F" / "Quitada del cronograma (para reprogramar)") y viajan a todos.
- DB: NO hizo falta crear nada (programacion.ts y borrados.ts ya existían).

Verificado EN VIVO con Luis probando a la vez: programó 9 actividades (se quedaron), las quitó (se propagó a todos), Hugo programó M2010 (visible en todos), engranaje 0. Nota: hubo una ventana de ~2 min con 503 de Supabase (reinicio del proyecto) que retrasó escrituras — se recuperó solo.

**OJO producción:** el mismo bug EXISTE en la app oficial (z3). Al desplegar, incluir z39 completo (y el fix de hash `orden` de z10). Sin cambios de SQL adicionales.

## z40 (madrugada del 2/7) — CARGAS de la actividad con su día + cambio de fecha

- En el menú de una actividad, la tarjeta ÚLTIMA CARGA ahora es **"CARGAS DE ESTA ACTIVIDAD (N)"**: TODAS las cargas (la más reciente primero), cada una con su valor, su DÍA y quién la hizo (lista con scroll, máx ~4 visibles).
- Cada carga tiene: **📅 Cambiar de día** (nuevo), **Mod** (valor) y **✕** (eliminar). El 📅 abre un selector de fecha EN LÍNEA (sin ventanas) + botón verde ➤. No permite fecha futura.
- Supervisor → genera SOLICITUD al admin (tipo `fechparte`, con payload en coments): "CAMBIAR FECHA de la carga +4 m del 2026-06-30 al 2026-06-27". El admin la ve en 🗑 Solicitudes con "✓ Aceptar el cambio / ✕ Denegar". Al aceptar, la carga se mueve — **incluso a una fecha pasada** (ese es el objetivo) — y se sincroniza a todos.
- ADMIN REAL → el 📅 aplica directo (igual que Mod/✕), queda en Cambios por día; sus auto-solicitudes no aparecen en su panel.
- Verificado E2E: solicitud de Shan (simulada) → panel → aprobar → fecha pasada aplicada y en la base → revertida → engranaje 0. La data real quedó intacta.

## z41 (madrugada del 2/7) — especialidad fija para supervisores + cambio de día SIEMPRE por solicitud

- **Supervisores fijos a su especialidad:** el selector de disciplina del Main (que gobierna Activities) y el de Programarme quedan BLOQUEADOS para no-admin: sin flechita, y al tocar sale "Solo tu especialidad: X". Cada supervisor entra directo a la programación y actividades de SU disciplina (la que eligió al loguearse). El ADMIN en modo ADMIN sigue cambiando libre; en "vista supervisor" queda bloqueado igual que ellos (simulación fiel).
- **📅 Cambiar el día de una carga = SIEMPRE solicitud** (pedido de Luis sobre z40): se quitó la aplicación directa del admin. Todos (admin incluido) generan solicitud `fechparte`; las del propio admin SÍ aparecen en su panel 🗑 Solicitudes para que las Acepte/Deniegue (a diferencia de Mod/✕ que siguen directos para el admin, z35).
- Verificado en vivo: candados activos en vista supervisor y libres en admin; 📅 del admin creó solicitud sin tocar la fecha, visible en su panel; denegada de prueba; engranaje en ✓ 0.

## z42 (madrugada del 2/7) — solicitudes sin duplicados + respaldo FEABLE

- **"Ya fue enviada":** todas las solicitudes al admin (cambiar valor Mod, cambiar día 📅, eliminar carga ✕, eliminar actividad, poner avance en 0) ahora revisan si ya existe UNA IGUAL sin resolver (misma carga o misma actividad, contando las que llegan de otros equipos). Si existe, sale "📨 Esa solicitud YA fue enviada al admin — espera su respuesta" y NO se crea otra — el panel del admin no se llena de repetidos.
- **Solicitudes en la DB (duda de Luis):** ya estaban completas — viajan por la tabla `pendientes` con fecha, ts (hora exacta de la acción), `updated_at` (trigger keep_newest) y tombstones (`cfg:pendientes:*` en `borrados`). No hubo que crear nada.
- **Respaldo completo:** carpeta nueva `RESPALDO_FEABLE_2026-07-02/` con la página v20260701z42 (5 archivos + esquema SQL) y `db/backup_obra_feable_completo.json` (**791 filas · 20 tablas**). Instrucciones de restauración en su `LEEME_RESPALDO.md`.

## z43 (madrugada del 2/7) — sync ligero (la "cola" del cronograma)

**Diagnóstico:** cada ciclo de sincronización re-ejecutaba TODAS las limpiezas remotas históricas EN SERIE: ~26 cargas borradas × 2 (partes+fotos) + ~25 lápidas de programación × 2 = **~100 peticiones seriales por ciclo (20-30 s)** → de ahí la cola al programar varias actividades seguidas.

**Fix z43:** registro local de "ya limpiado en el servidor" (`sgto4_dpdone` / `sgto4_pddone` por id+hora): cada sync solo procesa lo NUEVO; una re-limpieza completa por seguridad se hace UNA sola vez al abrir la app, y todo en PARALELO (Promise.all) en vez de en serie. Sin cambiar ninguna regla anti-pérdida (si algo falla, no se marca como hecho y se reintenta). Medido: el ciclo estable quedó en ~25 lecturas + solo las escrituras reales.

**Ojo:** durante la prueba se detectó que los 503 intermitentes de la noche eran un **incidente de Supabase** (banner oficial "We are investigating a technical issue") — no de nuestra base (CPU 2%, disco 3%, RAM 39%, 98.6% éxito). La app aguanta: guarda local y sube al recuperarse.

Pendiente menor para un z44: el pull consulta `tormenta` 5 veces por ciclo (redundante) y hay un tombstone basura `__TEST_BORRADO__` de calendario que genera un DELETE 400 perpetuo + 3 borrados genéricos (metrados/calendario) que se repiten cada ciclo — aplicarles el mismo registro "ya hecho".

## z44 (madrugada del 2/7) — cierre definitivo del "se borran solas" + test nocturno completo

**Bug cazado EN VIVO durante el test:** el iPhone de Luis despertó, se actualizó y en su primer sync re-subió una lápida VIEJA de C0999 guardada en su memoria local (pisando el estado nuevo por la regla del "último en subir"); en paralelo, una carrera entre "colocar" y "bajar datos" hizo que la fila recién puesta nunca subiera y la lápida vieja se la tragara.

**Fixes z44 (3 candados finos del "gana la última hora"):**
1. El PUSH ahora SUBE la fila si es más nueva que la lápida (antes cualquier lápida la callaba).
2. Al BAJAR lápidas, si la fila local es más nueva, la lápida NO se aplica y se purga del servidor.
3. Los equipos dormidos ya NO resucitan lápidas purgadas al despertar (solo comparten lápidas que aún no habían compartido).

**Test que pidió Luis (superado):** como "Prueba Claude" (supervisor Civil) se cargaron las 23 actividades civiles listas, 1 cada 5 segundos → 23/23 en la base, las 22 lápidas viejas purgadas solas. Luego con "Prueba Claude 2": se QUITARON las 23 (Civil quedó en 0 con 23 lápidas compartidas ✓) y con "Prueba Claude 3" en z44 se REPUSIERON las 23 → 23/23 en la base, 0 lápidas, 0 pendientes, y 10+ minutos de vigilancia con monitor sin UNA sola desaparición. El cronograma Civil queda PROGRAMADO (6/7 → 3/8, por sistema, secuencial).

Nota: los nombres "Prueba Claude 2/3" quedaron como autores de la programación civil — si se quiere se reasigna o se re-hace con el nombre real en un toque.

## z44 → z47 (mañana del 2/7) — la cacería completa del "se borra la data" + cell_at

Serie de fixes tras el reporte de Luis de que persistía el borrado. Cada versión cerró un hueco REAL, pero la causa que seguía mordiendo era la última:

- **z44:** una fila recién puesta VENCE a una lápida más vieja tanto al subir como al bajar, y los equipos dormidos ya no resucitan lápidas purgadas (se cazó al iPhone re-subiendo lápidas viejas al despertar).
- **z45:** la bajada de datos ya no se traga colocaciones locales recientes (<2 min) que el servidor aún no refleja — misma protección de ventana que ya tenían los avances.
- **z46:** (a) fix: las filas rescatadas por z45 ya no se marcaban como "ya enviadas" sin haber subido (marca interna `_lo` hasta que el servidor las confirme); (b) **cell_at**: hora CRUDA del reloj del equipo al momento de cada cambio, guardada en el caché y subida a la base en partes, metrados, programación, cambios, pendientes, lápidas y calendario (columnas creadas en la DB FEABLE; añadidas también a SQL_ANTES_DE_DESPLEGAR.sql). El trío queda: `cell_at` = reloj del celular · `ts` = hora real de la acción corregida a servidor · `created_at/updated_at` = hora de llegada al servidor.
- **z47 — LA CAUSA RAÍZ del día:** la app abierta en DOS pestañas/ventanas del MISMO navegador comparte el almacén local pero cada una tiene su copia en memoria: al guardar, una PISA lo de la otra (así se borraba lo que Luis programaba mientras la otra pestaña — suya o de pruebas — seguía viva). **Candado de pestaña única**: la segunda pestaña muestra "La app ya está abierta en otra ventana", queda pausada (ni guarda ni sincroniza) y tiene botón "Usar la app AQUÍ" que pausa a la otra. Verificado en vivo: overlay correcto, la activa colocó 3 actividades en plena carrera de sync → 3/3 en la base con cell_at poblado, estables, engranaje 0.

Nota de higiene: los webhooks de Supabase están vacíos (descartado). Regla de trabajo nueva: Claude NO usa el Chrome de Luis para pruebas de la app mientras él la está usando.

## z48 (mediodía del 2/7) — Realtime: los cambios llegan al instante

- Idea de Luis (docs de Supabase Realtime). Implementado como **campana, no como reemplazo**: la app abre un canal en vivo con Supabase (8 tablas publicadas: programación, partes, metrados, borrados, pendientes, cambios, nuevas, calendario); cuando OTRO equipo cambia algo, la campana suena y la app sincroniza al toque (~2-3 s en vez de esperar el ciclo de 15-30 s).
- El motor de sincronización de siempre NO cambió: si el canal se cae (mala señal, campo), la app sigue con su ciclo normal. Los de campo sin señal siguen igual que siempre: guardan local y suben al reconectar — y ahora los demás lo VEN llegar al instante.
- La pestaña pausada por el candado (z47) no reacciona a la campana (no sincroniza).
- Verificado en vivo: canal SUBSCRIBED y eventos INSERT/DELETE recibidos en ~0.7 s (prueba con fila temporal, limpiada).
- Para producción: el bloque de publicación Realtime quedó añadido a `SQL_ANTES_DE_DESPLEGAR.sql`.

## z49 (mediodía del 2/7) — adiós ventana bloqueante: la ventana en uso manda sola

Pedido de Luis (el bloqueo le estorbó al cambiar entre sus dos equipos). El candado de pestaña única se volvió AUTOMÁTICO y silencioso:
- La ventana que estás usando (la que tiene el foco) toma el mando sola.
- La otra se pausa con un AVISO DELGADO naranja arriba ("⏸ En pausa... toca aquí para seguir en esta") — sin pantalla que bloquee.
- Al volver a una ventana pausada (con solo enfocarla), retoma AUTOMÁTICAMENTE: se recarga rapidito (mantiene tu pantalla), lee el estado fresco y toma el mando; la otra pasa a pausa.
- La protección anti-pisadas sigue intacta por debajo: la pausada no guarda ni sincroniza.
Verificado en vivo con dos pestañas: pausa con banner (sin overlay), retome automático al enfocar, y relevo del mando entre ventanas.
Nota: entre PC y celular NUNCA hubo bloqueo (son memorias distintas) — el aviso solo aplica a ventanas del MISMO navegador.

## z50-z51 (tarde del 2/7) — Reporte del día: Previsualizar + PDF tipo presentación

En cada tarjeta de día del Main ahora hay DOS botones (reemplazan al "Reporte del día" de la tarjeta; el reporte-texto sigue en el botón 📄 de Activities):

- **👁 Previsualizar:** se abre EN LA APP para revisar: resumen del día (avances · actividades · especialidades · fotos) y, por especialidad (con su color), cada avance con su valor, acumulado, supervisor, sistema, obs, incidencia y miniaturas de fotos. Desde ahí mismo hay botón 📽 PDF.
- **📽 PDF:** genera el reporte BONITO tipo presentación (láminas apaisadas 16:9, como PowerPoint exportado a PDF): PORTADA azul con proyecto, fecha y tarjetas de totales + supervisores; SEPARADOR de color por especialidad; UNA LÁMINA POR AVANCE CON FOTO — fotos protagonistas (2 por lámina máx., avances con más fotos usan láminas extra) + panel con "AVANCE DEL DÍA" en grande, acumulado, supervisor, cuadrilla, incidencia y observaciones; y al final de cada especialidad una lámina-TABLA con los avances sin foto. Pie de lámina con proyecto, fecha y numeración. Se abre el diálogo de imprimir → "Guardar como PDF".
- Incluye TODAS las especialidades reportadas del día (no solo la del usuario), tal como pidió Luis.
- Verificado con el 1/7: 37 avances · 29 actividades · 3 especialidades · 7 fotos → 13 láminas correctas (portada, separadores, fotos grandes, tablas). z51 = fix cosmético del texto del botón PDF en la previsualización.

## z52-z53 (tarde del 2/7) — PDF del día en 3 LÁMINAS (una por especialidad) + botón Cronograma

- **PDF del día rediseñado a pedido de Luis: exactamente UNA lámina por especialidad** (Mecánica, Eléctrica, Civil — las que tengan avances ese día). Cada lámina 16:9: banda del color de la especialidad con nombre grande, fecha, proyecto y chips (avances · fotos); cuerpo con la LISTA COMPLETA de avances a la izquierda (código, actividad, avance en verde, acumulado, supervisor, obs, ⚠ incidencias; el tamaño de letra se adapta a la cantidad) y el MOSAICO DE FOTOS a la derecha (hasta 9 con su pie; si hay más, indica "+N más en la app"). Especialidad sin fotos → la lista usa todo el ancho a 2 columnas. Pie con proyecto, fecha y "lámina i de N".
- z53: el diálogo de imprimir se abre recién cuando las FOTOS terminaron de cargar (antes un internet lento podía dejar fotos en blanco en el PDF).
- El botón del Main ahora dice **"📅 Cronograma <especialidad>"** (ej. "Cronograma Mecánica") según la especialidad del usuario.
- Validado con el 1/7: exactamente 3 láminas (Civil 13 avances + 4 fotos en mosaico; Eléctrica 10 en 2 columnas; Mecánica).

## z54 (tarde del 2/7) — el % Real dice la verdad + terminadas fuera del programador

Observación del supervisor de Luis (C2100 al 100% real desde el 30/6 pero mostrando "Real 0%", grupo en 0%, chip "Sin iniciar", y aún programable):

**Causa:** el avance estaba PENDIENTE DE APROBACIÓN del admin (apr=0) y el % "Real" solo contaba avances aprobados, mientras otras celdas de la misma tarjeta contaban todo → el 0% junto al 100% en la misma fila.

**Fix z54:**
1. El % Real (tarjeta, grupo, estado, resumen del Main, "Resta" del programador) ahora cuenta TODOS los avances reportados — lo de campo es real desde que se reporta.
2. Si hay avances sin aprobar, la tarjeta muestra un chip **⏳ N** ("pendiente de aprobación del admin") — el control del admin sigue: puede aprobar o rechazar en su panel, y el rechazo ajusta el %.
3. **Las partidas TERMINADAS (≥100%) ya no aparecen en el desplegable de Programarme** (ni en Listas ni en Falta definir); el contador muestra "✔ Terminadas: N" para que se sepa a dónde fueron.

Validado con C2100: antes 0%/"Sin iniciar"/programable → ahora 100%/"Terminado"/⏳1/fuera del programador.

## z55 (tarde del 2/7) — un color por actividad en el cronograma

Antes el color de las viñetas era por POSICIÓN en el día (la 1ª de cada día siempre verde), así que actividades distintas en días distintos se veían iguales (ej. NUEVA-1 y A1190, ambas verdes). Ahora **cada actividad tiene SU color** (asignado por orden de aparición en el cronograma): el mismo en todos sus días, y distinto al de las demás. La primera actividad del cronograma sigue siendo verde.

## z55-z56 (tarde del 2/7) — colores por actividad + mes visible + modo Visualizar/Modificar

- **z55:** el color de las viñetas del cronograma ahora es POR ACTIVIDAD (constante en todos sus días; actividades distintas = colores distintos). Antes era por posición en el día y actividades diferentes salían iguales.
- **z56 · Mes sobre el calendario:** título grande centrado sobre la grilla ("JULIO 2026"; en vista semana muestra el rango, ej. "JUN – JUL 2026").
- **z56 · Pregunta al entrar al Cronograma:** "¿Qué deseas hacer?" con dos opciones — **👁 Solo visualizar** (protege el trabajo del especialista: se oculta la lista ☰ de actividades, el divisor y los botones de quitar/reordenar; queda SOLO el cronograma armado, la navegación, Exportar A3 y el regresar al Main; cualquier intento de tocar avisa "modo visualizar") y **✏️ Voy a modificar**. Un botoncito 👁/✏️ en la barra permite alternar en cualquier momento. Cada entrada al cronograma vuelve a preguntar (arranca protegido).
