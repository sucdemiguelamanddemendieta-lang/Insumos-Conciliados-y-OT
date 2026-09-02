# Proceso — Conciliación C2 por Principio Activo (Remitido vs. Aplicado)

**Actividad piloto:** (52) Siembra Asociada ADM 26-27
**Cadencia:** miércoles y viernes a las 16:00 (rutina automática)
**Responsable de ejecución:** Betiana (carga y corrección de RE/OT) · Responsable de validación: Mercedes
**Encaja dentro del control interno existente:** `Instructivo — Control unificado de insumos` (C1 Entradas, C2 Consumos, C3 Saldo). Este documento agrega el detalle operativo para hacer C2 **por principio activo** en vez de por producto comercial, que es el nivel que realmente importa cuando dos marcas distintas son el mismo agroquímico.

Regla de oro heredada del instructivo, no se cambia acá: *una diferencia en C3 se origina casi siempre en C1 o C2 — corregir directamente el saldo enmascara el error y lo reproduce al mes siguiente.* Nunca se ajusta un saldo a mano; se corrige el origen (remito, OT, o el mapeo de producto).

---

## 1. Fuentes de datos

| Fuente | Qué aporta | Dónde está |
|---|---|---|
| Mapeo Producto → Principio Activo | Agrupa productos comerciales distintos que son el mismo agroquímico | `mapeos/agroquimicos_pa.csv` (145 productos, desde la hoja `InsumosLabores` de la planilla maestra de GestorMax) |
| Reporte de costeo de actividad | Cantidad remitida por producto, imputada a la actividad, con comprobante y fecha | Export "Costeo de Actividad" desde GestorMax (Fecha, Comprobante, Código, Artículo, Cantidad, Pesos, Dólares) |
| Administrador de Remitos | Confirma comprobantes de remito (físicos RSI y de valorización RVI) por depósito/actividad | Export "Administrador de Remitos — Negocios Especiales" desde GestorMax |
| Documentos primarios — **carpeta de conciliación** | OT escaneadas con las anotaciones del operario (consumo real, cobertura, sobrantes) y los remitos que las respaldan: RyC, remitos propios de ADM (stock de galpón) y de terceros (Cuthill) | Drive `10VirYUGQKUyHwkcU_VtHymV8ddYksssx` — un PDF/JPEG por OT y por remito, con el/los número(s) de OT en el nombre del archivo |
| Documentos primarios — **carpeta de OT emitidas** | La OT tal como sale del Gestor, con el plan impreso, *antes* de volver anotada del campo. Es la única fuente que dice qué OT existen | Drive `15RgLh8xkYK7uXafxTqfs_7wzF-FYx0Ov` — `OT 00000NNN <CAMPO> <proveedor>.pdf` |
| Google Sheet "Base" de GestorMax (pestaña con movimientos) | Fuente potencial más completa/automatizada — **hoy bloqueada**: el archivo es demasiado grande para las herramientas de lectura disponibles (más de 25 pestañas, la primera —"Imputador"— agota el presupuesto de lectura antes de llegar a la pestaña de movimientos). Mientras no se resuelva el acceso, se trabaja con los exports manuales de arriba. | — |

**Las dos carpetas de Drive no son intercambiables y hay que barrer las dos en cada corrida.** La de conciliación solo tiene las OT que ya volvieron anotadas; la de OT emitidas tiene todas las que existen. Mirar solo la primera hace que una OT emitida y ejecutada, cuya hoja todavía no volvió del campo, sea invisible para el control — el consumo ocurrió y el cruce no lo ve. Así se pasaron por alto las OT 752 a 760 hasta la corrida del 31/08. El barrido correcto es: listar la carpeta de OT emitidas, y para cada número buscar su par anotado en la carpeta de conciliación; las que no tienen par van al bloque abierto (ver 2.bis).

**Importante:** los exports de "costeo de actividad" y "administrador de remitos" reflejan lo *cargado/imputado*, no necesariamente lo *retirado físicamente* si hay remitos pendientes de cargar. Por eso el cruce contra los documentos primarios (remitos escaneados y OT) es imprescindible, no un paso opcional.

## 2. Pasos de la conciliación

1. **Armar/actualizar el mapeo Producto → Principio Activo.** Todo producto nuevo que aparezca sin principio activo cargado se marca como pendiente (no se inventa la concentración ni la sal/éster — eso cambia el principio activo real).
2. **Sumar lo remitido por producto**, cruzando el reporte de costeo con el administrador de remitos y con los remitos físicos escaneados de la carpeta de Drive. Incluir:
   - Remitos de Rindes y Cultivos DAS (RyC).
   - Remitos propios de ADM (stock de galpón) — cuentan como remitido.
   - Restar remitos de devolución (RD) cuando existan — un RD reduce el remitido neto.
3. **Sumar lo aplicado por producto**, leyendo cada OT (dosis × hectáreas, o cantidad total anotada) desde el documento primario, no desde el plan/presupuesto.
4. **Convertir ambos lados a Principio Activo** con el mapeo del paso 1. Productos con nombre comercial distinto pero mismo principio activo y concentración se suman juntos (confirmar la equivalencia contra la ficha técnica o con Betiana/Mercedes si hay duda — no asumir por similitud de nombre).
5. **Calcular diferencia = Remitido neto − Aplicado**, por principio activo.
5.b **Aplicación selectiva (Weed-It).** Cuando la fumigación es selectiva, el consumo real NO es el plan impreso (dosis × ha): la máquina detecta clorofila y trata solo la fracción con maleza, con ahorros de hasta el 95%. El plan impreso sobreestima el consumo. Cómo detectarlo y validarlo: calcular el cociente consumo/plan producto por producto dentro de una misma OT — si la aplicación fue selectiva, **el porcentaje es idéntico para todos los productos de esa OT** (es una fracción de superficie, no un ajuste de dosis). Coberturas ya declaradas: OT 722 = 34,9%, OT 723 = 24,0%, OT 727 = 10%, OT 729 = 44,44%; no selectivas (100%): OT 724, 735 (manchoneo 10 ha), 737, 739. Nunca imputar el plan como consumo en una OT selectiva sin pedir el porcentaje real.

6. **Aplicar tolerancia:** el margen de error aceptable es la capacidad de **un envase abierto** del producto en cuestión (ej. 20 L para un producto envasado en bidones de 20 L), no cero absoluto. Si el principio activo agrupa productos con distinto tamaño de envase, no promediar — aclarar cuál envase se está usando de referencia.
7. **Clasificar cada principio activo:**
   - **OK** — diferencia dentro de tolerancia.
   - **NEGATIVO** — aplicado > remitido, fuera de tolerancia. Prioridad alta: revisar primero si falta cargar un remito antes de tocar la OT.
   - **SOBRANTE SIN RD** — remitido > aplicado, fuera de tolerancia, sin remito de devolución que lo justifique.
   - **EN TRÁNSITO** — la diferencia se explica por documentación que todavía no llegó, no por un problema real. Ver la sección 2.bis.
   - **SIN DATOS SUFICIENTES** — falta un documento (OT o remito) para poder concluir.
8. **Doble rechequeo obligatorio antes de reportar:**
   - Pasada 1: agregado por producto.
   - Pasada 2: recálculo independiente, por documento individual (por comprobante y por OT), para confirmar que coincide con la pasada 1.
   - Pasada 3: revisión crítica — duplicados (mismo remito fotografiado/contado dos veces), nombres comerciales que nunca coinciden entre remito y OT, unidades mezcladas, fechas fuera de rango, remitos u OT mencionados pero no presentes en la carpeta.
9. **Lo que no cierra después de las 3 pasadas se documenta como pregunta puntual** (a quién preguntar, qué dato falta) — no se fuerza un cierre en cero con datos que no lo permiten. Ver `pedidos/` para el formato de estas preguntas cuando van dirigidas a Betiana.

## 2.bis. Campaña en curso: OT y remitos que siguen llegando

La campaña está activa y se agregan OT y remitos con el correr de los días. Eso cambia cómo hay que leer una diferencia: **no toda diferencia es un problema; muchas son un desfasaje temporal entre los dos lados del cruce.**

### Fecha de corte

Cada corrida fija una **fecha de corte** (por defecto, el día de la corrida) y la deja escrita en el reporte. Solo se computan OT y remitos con fecha *anterior o igual* al corte. Sin corte explícito, dos corridas no son comparables y el historial pierde sentido.

### El corte no es uno solo: bloque cerrado y bloque abierto

Fijar un único corte en el día de la corrida no alcanza, porque los dos lados del cruce avanzan a distinta velocidad: las OT se emiten el día que se aplica, y el costeo se exporta cada tantos días. Si el corte cae en medio de esa ventana, aparecen negativos enormes que solo miden el atraso del export.

Por eso cada corrida parte el período en dos:

- **Bloque cerrado** — hasta la fecha del último export de costeo disponible *menos* los días que tarda en cargarse un remito. Acá los dos lados están completos y una diferencia significa algo. Es el bloque que se concilia, se reporta y cuenta para escalamiento.
- **Bloque abierto** — de ahí hasta el día de la corrida. Acá hay OT emitidas cuyos remitos todavía no están en el costeo. **No se computa como negativo**: se lista aparte, con las OT y los remitos que sí llegaron, y su única salida es pedir el export de costeo actualizado.

La frontera se determina mirando el propio export: la fecha del último remito cargado. Si el costeo llega al 18/08 y hay OT del 19/08, el bloque cerrado termina antes, no después.

### Diferencia real vs. diferencia por documentación en tránsito

Antes de marcar algo como NEGATIVO o SOBRANTE, verificar si la diferencia se explica por un desfasaje:

- **Una OT aplicada cuyo remito todavía no se cargó** aparece como negativo, y no lo es: el producto salió, el papel no llegó.
- **Un remito cargado cuya OT todavía no se emitió o no pasó a REAL** aparece como sobrante, y tampoco lo es.

Regla práctica: si la diferencia de un principio activo es del orden de lo que mueve **una sola OT o un solo remito reciente** (últimos 7-10 días antes del corte), clasificarla **EN TRÁNSITO** y no como problema. Se deja anotada para seguirla, sin generar pedido ni contar para escalamiento.

Una diferencia es **real** cuando sobrevive a que la documentación del período se complete: sigue abierta después de que llegaron los documentos de esas fechas, o su magnitud excede largamente lo que cualquier documento pendiente podría explicar.

### Efecto sobre el escalamiento

Un principio activo **solo suma corridas consecutivas para escalamiento si entre una corrida y la otra no llegó documentación nueva que lo afecte.** Si llegaron OT o remitos de ese producto, el contador se reinicia: la foto cambió, todavía no es una diferencia que persiste. Sin esta salvedad, una campaña en crecimiento escalaría permanentemente y el escalamiento perdería valor.

### Mantenimiento incremental

- **Productos nuevos:** cada OT o remito puede traer un producto que no está en `mapeos/agroquimicos_pa.csv`. Agregarlo con su principio activo real (de la ficha técnica o preguntando). Nunca inferirlo por parecido de nombre — el nombre comercial no determina la sal ni la concentración.
- **OT de fumigación nuevas:** hay que saber si fueron selectivas y con qué cobertura (ver paso 5.b). Es un dato recurrente, no una pregunta de una sola vez. **Lo más eficiente es que el porcentaje de cobertura quede anotado en la OT al momento de cerrarla**, junto al consumo real; así deja de ser una consulta en cada corrida.
- **La columna «Dosis» de la OT está redondeada a dos decimales.** No sirve para recalcular el plan: en la OT 752 muestra 0,01 kg/ha de metsulfurón cuando el cálculo real usa 0,005, y las notas impresas del tipo «la dosis de metsulfurón son 5 gramos/ha» son advertencias al operario por ese redondeo, no avisos de un error del sistema. Para verificar una dosis hay que dividir el **total** por las hectáreas, nunca leer la columna.
- **Tolerancia:** es por producto y por envase, no acumulativa por campaña. Que haya más OT no agranda el margen aceptable de cada principio activo.

## 3. Formato de reporte

Cada corrida produce:
- La **fecha de corte** usada, explícita.
- Qué documentación nueva entró desde la corrida anterior (OT y remitos), para poder leer los cambios del cuadro.
- Una tabla por Principio Activo (Remitido / Aplicado / Diferencia / Tolerancia / Estado).
- Un detalle por OT con su cruce de remito(s) y qué quedó sin poder cruzar.
- La lista de preguntas pendientes, si las hay, con destinatario sugerido (Betiana para carga/datos operativos, Mercedes para excepciones o escalamiento según el punto 8 del instructivo general).

## 4. Historial de corridas

| Fecha | Actividad | Resultado | Preguntas abiertas |
|---|---|---|---|
| 19/08/2026 | (52) Siembra Asociada ADM 26-27 | 1 principio activo OK, 1 negativo, 6 sobrante sin RD, 2 sin datos suficientes | Remitos de mayo (¿incluidos o adicionales?), OT 745-747 faltantes, remitos "533/534" sin cargar — ver `pedidos/2026-08-19_pedido_betiana.docx` |
| 20/08/2026 | (52) Siembra Asociada ADM 26-27 | 4 PA dentro de tolerancia (Diflufenican exacto en 0); negativos concentrados en OT 722/723/724; resto de la campaña cierra | Match remitos internos ↔ RD no da (0 de 11); faltan RE de OT 722/723/724; 11533/11534 sin DC de reversa — ver `reportes/2026-08-20_conciliacion_ADM_26-27_v2.html` y `pedidos/2026-08-20_pedido_betiana_2.docx` |
| 21/08/2026 | (52) Siembra Asociada ADM 26-27 | Weed-It verificado (dispersión 0,0) explica las OT 722/723/724; 5 PA en tolerancia; sobrantes de Enlist, TTM y Paramer cierran dentro de 2 unidades | Falta consumo real de OT 727/729/735/737/739; Herbifen −140 L no se explica por selectiva (¿remito sin cargar?) — ver `reportes/2026-08-21_conciliacion_ADM_26-27_v3.html` |
| 26/08/2026 | (52) Siembra Asociada ADM 26-27 | Corte 26/08. Sin documentación nueva desde el 21/08 (solo renombre de la OT 740, contenido idéntico) y sin respuesta al pedido. No se rehizo la conciliación: sigue vigente la del 21/08 | **ESCALADO A MERCEDES** — glifosato, cletodim y Herbifen con diferencia en dos cierres consecutivos sin documentación nueva que los afecte. Ver `pedidos/2026-08-26_escalamiento_mercedes.docx` |
| 27/08/2026 | (52) Siembra Asociada ADM 26-27 | Corte 27/08. Betiana aportó las coberturas de las 5 OT pendientes. Todos los negativos bajaron (glifosato −324,66 → −219,00) pero ninguno llegó a tolerancia: 3 de las 5 OT resultaron no selectivas | Remitos Cuthill 8-2332 y R-8-2322 citados como fuente pero ausentes en Drive — canal de provisión no contabilizado. El respaldo documental del 2,4-D sigue sin responder. Ver `reportes/2026-08-27_conciliacion_ADM_26-27_v4.html` |
| 31/08/2026 | (52) Siembra Asociada ADM 26-27 | Corte partido: **bloque cerrado al 12/08** (sin cambios: siguen los 5 negativos del 27/08) y **bloque abierto 13/08→31/08** con 9 OT sin remitos cargados. Llegaron los remitos Cuthill: son **antideriva**, no glifosato — cierran el punto del antideriva sin respaldo (40 L remitidos vs 36,45 aplicados) pero no tocan los negativos. Solmix cierra con +2.897 kg sobre 145.880 (2,0%) | Falta el export de costeo posterior al 19/08 para poder conciliar las OT 752-760; sigue sin responder el respaldo documental del 2,4-D; producto nuevo Flumioxazin sin mapeo ni remito |
| 02/09/2026 | (52) Siembra Asociada ADM 26-27 | Corte 02/09. Las OT 752/753/754 volvieron anotadas con sus tres comprobantes (RyC 12176, 12178 y **remito propio ADM 0003-0000005**): **MCPA y pinoxaden cierran en 0,00 exacto**, metsulfurón +0,050 kg, Vitagrow +1,00 kg, TTM −5,00 L, todos en tolerancia. Bromoxinil +35 L queda EN TRÁNSITO para las OT 757/759. Bloque cerrado al 12/08 sin novedades por 2ª corrida consecutiva → **escalamiento**. Bloque abierto creció de 6 a 13 OT (755-767) | **ESCALADO: glifosato, cletodim y 2,4-D.** Falta el export de costeo posterior al 19/08 (bloquea 13 OT y ~4.000 L). Producto nuevo **Viovan** sin principio activo; Lontrel y Eudor Cletodim Helm sin concentración confirmada. Ver `reportes/2026-09-02_conciliacion_ADM_26-27_v6.html` |

### Estado de escalamiento (actualizar cada corrida)

**Escalado el 02/09/2026: glifosato (−219,00 L), cletodim (−129,62 L) y 2,4-D (−81,20 L agrupando Herbifen y Enlist).** Se cumple la condición: dos corridas consecutivas (31/08 y 02/09) sin que ingrese documentación que los afecte, después del reinicio del contador del 27/08. La hipótesis Cuthill quedó descartada el 31/08 —esos remitos entregan antideriva NPA 15 Full— y desde entonces no apareció ningún comprobante nuevo de estos tres productos ni un export de costeo actualizado. Ver `pedidos/2026-09-02_escalamiento_mercedes_2.docx`.

**La dirección de Mercedes sigue sin confirmar.** El escalamiento se envía al dueño y a Betiana; no se manda a una dirección inferida de metadatos de Drive.

**No confundir con el bloque abierto.** Las diferencias de las OT 755-767 son enormes (glifosato −1.695,32 L, Herbifen −628,87 L) pero **no cuentan para escalamiento**: miden el atraso del export de costeo. El grupo 752-754 lo demuestra — estaba en esa misma lista el 31/08 y el 02/09 cerró en cero exacto en cuanto llegaron los tres elementos que faltaban (OT anotada, remito propio emitido, remitos de RyC cargados).

**Qué retira el escalamiento:** que aparezca un remito —de RyC, propio de ADM o de un tercero— que respalde esos tres principios activos, o un export de costeo que los incluya. Mientras tanto se mantiene abierto y se repite en cada corrida.

Actualizar esta tabla en cada corrida (miércoles y viernes) para tener trazabilidad de si las diferencias se van cerrando o se repiten — si un mismo principio activo queda con diferencia abierta en dos corridas consecutivas, corresponde escalar a Mercedes según el punto 8 del instructivo general.
