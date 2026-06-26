# Tareas KNIME — HDI Seguros Colombia

Ejercicios prácticos usando los datos en `data/` para aprender los nodos fundamentales de KNIME.

---

## Datos disponibles

| Archivo | Descripción | Filas |
|---|---|---|
| `data/clientes.csv` | Información de asegurados (nombre, ciudad, segmento) | 15 |
| `data/polizas.csv` | Pólizas emitidas por tipo, prima, estado y canal | 23 |
| `data/siniestros.csv` | Reclamaciones con valor reclamado y aprobado | 15 |
| `data/primas_mensual.csv` | Producción mensual agregada por ciudad y ramo | 42 |

---

## Tarea 1 — Lectura y exploración de datos

**Objetivo:** cargar los CSV y conocer la estructura de los datos.

**Nodos a usar:** `CSV Reader`, `Statistics`, `Data Explorer`

**Pasos:**
1. Leer `clientes.csv` con el nodo **CSV Reader**.
2. Conectar a **Statistics** para ver media, min y max de columnas numéricas.
3. Conectar a **Data Explorer** para inspeccionar distribuciones.

**Pregunta:** ¿Cuántos clientes hay por segmento (Basic / Standard / Premium)?

---

## Tarea 2 — Filtrado y selección de columnas

**Objetivo:** filtrar pólizas activas y seleccionar solo las columnas relevantes.

**Nodos a usar:** `CSV Reader`, `Row Filter`, `Column Filter`

**Pasos:**
1. Leer `polizas.csv`.
2. Usar **Row Filter** para conservar solo filas donde `estado = Activa`.
3. Usar **Column Filter** para quedarte con: `id_poliza`, `id_cliente`, `tipo_seguro`, `prima_anual`, `suma_asegurada`.
4. Conectar **Statistics** al resultado.

**Pregunta:** ¿Cuál es la prima anual promedio de las pólizas activas?

---

## Tarea 3 — Unión de tablas (Join)

**Objetivo:** enriquecer las pólizas con la información del cliente.

**Nodos a usar:** `CSV Reader` (x2), `Joiner`

**Pasos:**
1. Leer `clientes.csv` y `polizas.csv` en nodos separados.
2. Conectar ambos al nodo **Joiner**.
   - Join column izquierda: `id_cliente` (clientes)
   - Join column derecha: `id_cliente` (pólizas)
   - Tipo: **Inner Join**
3. Revisar el resultado: cada póliza debe mostrar el nombre y ciudad del cliente.

**Pregunta:** ¿Qué ciudad tiene más pólizas de tipo Automóvil activas?

---

## Tarea 4 — Agrupación y agregación

**Objetivo:** calcular el total de prima emitida y siniestros por tipo de seguro.

**Nodos a usar:** `CSV Reader`, `GroupBy`

**Pasos:**
1. Leer `primas_mensual.csv`.
2. Conectar al nodo **GroupBy**.
   - Group by: `tipo_seguro`
   - Aggregation: `prima_emitida_COP` → Sum, `siniestros_ocurridos` → Sum, `valor_siniestros_COP` → Sum
3. Agregar una columna calculada con el nodo **Math Formula**:
   - `siniestralidad (%) = (valor_siniestros_COP / prima_emitida_COP) * 100`

**Pregunta:** ¿Qué ramo tiene la siniestralidad más alta?

---

## Tarea 5 — Análisis de siniestros

**Objetivo:** identificar los siniestros con mayor impacto económico.

**Nodos a usar:** `CSV Reader`, `Joiner`, `Sorter`, `Top k Row Filter`

**Pasos:**
1. Leer `siniestros.csv` y `polizas.csv`.
2. Unirlos por `id_poliza` con **Joiner**.
3. Agregar una columna con **Math Formula**: `diferencia = valor_reclamado - valor_aprobado`.
4. Ordenar de mayor a menor `valor_aprobado` con **Sorter**.
5. Usar **Top k Row Filter** para quedarte con los 5 siniestros de mayor valor aprobado.

**Pregunta:** ¿Cuál es el siniestro con mayor brecha entre valor reclamado y valor aprobado?

---

## Tarea 6 — Visualización

**Objetivo:** crear gráficas para comunicar los resultados.

**Nodos a usar:** `Bar Chart`, `Pie/Donut Chart`, `Line Plot`

**Pasos:**
1. **Bar Chart** con el resultado de la Tarea 4: `tipo_seguro` en X, `prima_emitida_COP` en Y.
2. **Pie/Donut Chart** con `clientes.csv` agrupado por `segmento`.
3. **Line Plot** con `primas_mensual.csv` filtrado por ciudad=Bogotá: `mes` en X, `prima_recaudada_COP` en Y, separado por `tipo_seguro`.

---

## Tarea 7 — Flujo completo con exportación

**Objetivo:** construir un workflow end-to-end que genere un reporte de productividad.

**Nodos a usar:** `CSV Reader`, `Joiner`, `GroupBy`, `Math Formula`, `CSV Writer`, `Excel Writer`

**Pasos:**
1. Leer `polizas.csv` + `clientes.csv` → Join por `id_cliente`.
2. Filtrar solo pólizas `Activa`.
3. Agrupar por `ciudad` y `tipo_seguro` → suma de `prima_anual`.
4. Calcular participación porcentual de cada ciudad: `(prima_ciudad / prima_total) * 100`.
5. Exportar el resultado a `data/reporte_produccion.xlsx` con **Excel Writer**.

**Pregunta:** ¿Qué ciudad concentra el mayor volumen de prima en pólizas de Vida?

---

## Criterios de éxito

- [ ] Tarea 1 completada — datos cargados sin errores
- [ ] Tarea 2 completada — filtrado correcto, 0 pólizas vencidas en resultado
- [ ] Tarea 3 completada — Join sin filas huérfanas
- [ ] Tarea 4 completada — siniestralidad calculada por ramo
- [ ] Tarea 5 completada — top 5 siniestros identificados
- [ ] Tarea 6 completada — 3 gráficas generadas
- [ ] Tarea 7 completada — archivo Excel exportado correctamente
