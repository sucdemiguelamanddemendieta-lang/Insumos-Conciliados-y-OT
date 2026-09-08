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
| Aporte de insumos de ADM (stock propio en la agronomía) | Insumos que están físicamente en el depósito de RyC pero son **propiedad de ADM**, comprados y pagados en ejercicios anteriores. RyC los documenta como **«Remito de Cliente»**, no como «Salida de insumos», así que **no aparecen en el export de costeo de actividad**. Cuentan como remitido. | `datos/aporte_insumos_ADM.csv` y el export de respaldo en `datos/fuentes/` |
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
- **El costeo de actividad no contiene todo el remitido: hay tres canales y solo uno entra por ahí.** El export se arma con los movimientos de «SALIDA INSUMOS, (52) SIEMBRA ASOCIADA», así que deja afuera (1) los **remitos propios de ADM** por stock de galpón, (2) los remitos de **terceros** (Cuthill), y (3) el **aporte de insumos de ADM en stock en la agronomía**, que RyC documenta como «Remito de Cliente» porque la mercadería ya era de ADM —comprada y pagada en ejercicios anteriores—. Este tercer canal fue el que sostuvo tres negativos falsos durante seis corridas: 480 L que existían, se aplicaron y estaban en las OT, pero eran invisibles del lado remitido. **Antes de declarar un faltante, verificar los tres canales, no solo el costeo.**
- **Nunca dar por reemplazado un remito sin el documento a la vista.** El error más caro de esta campaña fue concluir, el 20/08, que el remito 11754 reemplazaba a los 11533 y 11534 *por parecido de contenido*, sin tenerlos escaneados. Eran remitido adicional: 480 L que faltaban del lado remitido y que sostuvieron tres negativos falsos durante seis corridas. **Un reemplazo se afirma con los dos documentos delante y coincidencia línea por línea** (como en 11629/11795 y 11682/11801, 5 de 5 y 4 de 4); con cualquier cosa menos que eso, se pregunta.
- **La marca comercial anotada en la OT es una herramienta de trazabilidad, no un detalle.** El remito 11534 fue el único de la campaña que entregó cletodim *Arrow*; los reescaneos de las OT 723 y 724 declaraban Arrow y el resto de los remitos traía Sino Agri. Esa sola coincidencia ató el remito a sus OT sin ambigüedad. **Anotar siempre la marca real aplicada**, aunque el principio activo sea el mismo: es lo que permite emparejar un remito con su OT cuando los números no alcanzan.
- **Un remito posterior a la OT no la respalda automáticamente, pero tampoco se descarta sin preguntar.** Cuando aparece un remito con fecha posterior a las OT que tienen faltante, y justo de los productos que faltan, hay dos lecturas posibles: entrega corriente para las OT siguientes, o regularización de producto ya retirado. **No se decide por la fecha sola** — hay que preguntarlo. Un indicio útil: si el remito trae un producto que recién aparece en OT de esos días, es entrega corriente; si trae solo productos viejos en cantidades que calzan con el faltante, sospechar regularización.
- **Un remito a nombre de ADM SRL con el mismo contenido que uno de la Siembra Asociada es una reemisión, no una entrega adicional.** RyC factura primero a ADM y después reemite a la Siembra Asociada con otro número: 11629→11795 y 11682→11801, con 5/5 y 4/4 líneas idénticas. Antes de sumar un remito "nuevo" al remitido, comparar la canasta completa contra los remitos ya cargados, y aplicar el **test por descarte**: sumarlo y ver qué le pasa a los principios activos que hoy cierran. Si un producto que cerraba exacto pasa a tener cientos de litros de sobrante sin remito de devolución, el remito era una reemisión. Este test decidió la cuestión en la corrida del 04/09 (flurocloridona pasaba de +5,30 a +425,30 L).
- **Un porcentaje de cobertura declarado no reemplaza al consumo real anotado.** En la OT 727 la cobertura informada del 10 % daba 9,24 L de Panzer Gold; el operario había anotado 22,00 L como "gasto total", que a la dosis de la OT son 10 ha tratadas de 42, no 4,2. El 10 % era el ahorro, no la superficie. **Cuando existe el consumo real manuscrito, ese dato manda sobre cualquier porcentaje**; el porcentaje solo se usa mientras la OT no haya vuelto anotada.
- **No agrupar productos comerciales por principio activo antes de haber cerrado cada uno por separado.** Panzer Gold y Round Up Full II son los dos glifosato, pero de sales distintas (dimetilamina y potásica) y con canales de provisión distintos: el Panzer viene por remitos de RyC y el Round Up por remito propio de ADM. Agrupados daban un faltante único de −288 L que no se podía atacar; separados, el Round Up cierra en +14 L y todo el faltante queda localizado en el Panzer Gold. **Primero cerrar por producto comercial, después agrupar por principio activo** — al revés se pierde la pista del comprobante.
- **La columna «Remito» de la OT es el punto de partida para rastrear un faltante.** Una OT sin remito citado es candidata inmediata: en la corrida del 03/09, siete OT sin cita concentraban más consumo que el faltante entero de Panzer Gold y de cletodim. Antes de suponer una fuga, listar las OT sin remito citado y ordenarlas por volumen.
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
| 03/09/2026 | (52) Siembra Asociada ADM 26-27 | Corte 12/08. Betiana pidió trazabilidad OT por OT. Se reconstruyó línea por línea desde los documentos: **7 OT no citan ningún remito** (727, 729, 730, 731, 742, 743, 747) y su consumo cubre el 164 % del faltante de Panzer Gold y el 167 % del de cletodim. **Separando los dos glifosatos, el Round Up cierra (+14,00 L) contra el remito propio R375; todo el faltante es Panzer Gold (−302,00 L)**. Cifras corregidas: glifosato −288,00 (antes −219,00, apareció el Panzer de la OT 742 que estaba ilegible); cletodim −121,22 (antes −129,62); 2,4-D −81,20 sin cambios | Revisar las 7 OT sin remito citado, empezando por 747/743/731. Herbifen solo se cubre al 70 % — falta otra fuente. Sigue faltando el costeo posterior al 19/08. Ver `datos/trazabilidad_OT_glifosato_cletodim_24D.csv` y `reportes/2026-09-03_conciliacion_ADM_26-27_v7.html` |
| 04/09/2026 | (52) Siembra Asociada ADM 26-27 | Corte 12/08. Betiana reescaneó 9 OT y subió el remito 11682. **Los dos remitos a nombre de ADM (11629 y 11682) son reemisiones de remitos ya contados** (= 11795 y 11801, 5/5 y 4/4 líneas idénticas): dan el respaldo documental que faltaba a las OT 727/729/730/731/732 pero no suman remitido. El consumo real de los reescaneos **agranda** el faltante: Panzer Gold −315,62 (antes −302,00), cletodim −127,82, Herbifen −108,50. La OT 727 pesó: el 10% de cobertura daba 9,24 L de Panzer y el operario anotó 22,00 ("gasto total" = 10 ha de 42, no 4,2). Round Up +14,00, flurocloridona +5,30 y paraquat −8,00 cierran | Falta el Costeo de Actividad reexportado a hoy: es lo único que impide extender el control a la OT 760 como pidió Betiana. Confirmar que Arrow y Sino Agri son ambos cletodim 24%. Ficha técnica del Viovan. Ver `reportes/2026-09-04_conciliacion_ADM_26-27_v8.html` |
| 04/09/2026 (2ª) | (52) Siembra Asociada ADM 26-27 | **Corte 03/09.** Llegó el export de costeo al 03/09 con cuatro remitos que no estaban (12205 y 12207 del 20/08, 12343 y 12345 del 02/09). El bloque abierto se destrabó: de 13 principios activos, **7 pasan a cerrar** (Herbifen −8,87, bromoxinil +34,70, MCPA +12,60, pinoxaden +3,20, flumioxazin +3,98, Lontrel +0,97, Vitagrow +0,20). Los 6 restantes quedan EN TRÁNSITO, no como faltante: la columna del plan es plan impreso y ninguna de las 13 OT volvió anotada. El bloque hasta la OT 754 no se mueve: Panzer −315,62, cletodim −127,82, Herbifen −108,50 | **Pregunta nueva y precisa:** ¿los remitos 12205 y 12343 son entrega de esas fechas o regularizan producto retirado antes? Traen 1.480 L de Panzer y 620 de Herbifen, justo lo que falta. Ficha técnica del Viovan (ya está en el costeo, sin PA). Concentraciones: Arrow/Sino Agri, Axial/Pinxit/Pinoxamyl, Legacy/50% SC. Paraquat de la OT 767 sin remito. Ver `reportes/2026-09-04_conciliacion_ADM_26-27_v9.html` |
| 07/09/2026 | (52) Siembra Asociada ADM 26-27 | Corte 03/09. **Betiana corrigió el supuesto del 20/08: el remito 11754 NO reemplaza a los 11533 y 11534.** Son remitido adicional (Panzer 360, cletodim Arrow 160, Herbifen 120, TTM 40, Veltroma 2,80 kg) y no estaban contados. **Sobre consumo real no queda ningún negativo:** glifosato +58,38 (antes −301,62), cletodim +32,18 (antes −127,82), 2,4-D +33,50 (antes −86,50). El único signo negativo es paraquat −8,00, dentro de tolerancia. **Escalamiento retirado.** Confirmado además: Arrow/Sino Agri/Eudor Helm son los tres cletodim 24%; Viovan es fungicida (picoxistrobin + prothioconazole); Lontrel es clopiralid-olamina 47,5%; La Palma y las OT 712/726 son de Ganadería | En el acumulado quedan 3 negativos, **todos del lado del plan impreso** de las OT 755-767, que no volvieron anotadas: glifosato −156,94, cletodim −118,22, TTM −58,98. Aclarar la diferencia que menciona Betiana sobre el 11754 (el escaneado coincide con el costeo). Remito del paraquat de la OT 767. Ver `reportes/2026-09-07_conciliacion_ADM_26-27_v10.html` |
| 08/09/2026 | (52) Siembra Asociada ADM 26-27 | Corte 03/09. El dueño aportó el detalle de los remitos 11533 y 11534: son **aporte de insumos de ADM Agro** — producto en stock en la agronomía pero propiedad de ADM, **comprado y pagado en 2025** (factura 0020-00022008 del 23/04/2025). RyC los emitió como **«Remito de Cliente»**, no como «Salida de insumos», y por eso no entraban en el circuito del costeo de actividad. Las 6 líneas coinciden con lo incorporado el 07/09: **las cifras no cambian**, ahora tienen respaldo formal con comprobante, factura y depósito | Ninguna sobre este punto: queda cerrado. Siguen los 3 negativos del acumulado, todos del lado del plan impreso de las OT 755-767. Ver `datos/aporte_insumos_ADM.csv` y `reportes/2026-09-08_conciliacion_ADM_26-27_v11.html` |

### Estado de escalamiento (actualizar cada corrida)

**RETIRADO el 07/09/2026.** El escalamiento a Mercedes se había preparado por glifosato, cletodim y 2,4-D. Los tres pasaron a positivo con la corrección de Betiana sobre los remitos 11533 y 11534 — con documentación, no con un ajuste de saldo. El documento `pedidos/2026-09-02_escalamiento_mercedes_2.docx` queda archivado; nunca llegó a enviarse, así que no hay nada que retractar ante Mercedes.

**Ningún principio activo en condición de escalamiento.** Los negativos que quedan en el acumulado (glifosato −156,94, cletodim −118,22, TTM −58,98) están enteramente del lado del plan impreso de las OT 755-767, que no volvieron anotadas: son EN TRÁNSITO por definición de 2.bis y no cuentan para escalamiento.

**El contador vuelve a arrancar de cero** para cualquier diferencia nueva que aparezca a partir de acá.

Actualizar esta tabla en cada corrida (miércoles y viernes) para tener trazabilidad de si las diferencias se van cerrando o se repiten — si un mismo principio activo queda con diferencia abierta en dos corridas consecutivas, corresponde escalar a Mercedes según el punto 8 del instructivo general.
