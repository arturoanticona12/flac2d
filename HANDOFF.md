# HANDOFF — Spence FY31 → FY39 (MS y MSO), FLAC2D

Fecha: 2026-10-04. Sesión con flac-bench (laptop Stantec con licencia FLAC2D 9.00.184).

## 1. Objetivo

Llevar los modelos FLAC2D del **Muro Sur (MS)** y del **Muro Suroeste (MSO)** del FY31 al **FY39**, con coronamiento **MS 1646** y **MSO 1648**.

- La geometría crece con la misma lógica del FY31.
- Todo lo demás se mantiene como en los archivos de modelamiento y en el memo `STC WP2 FY31 Memo_Modelo Dinamico Deposito_Rev B.pdf`.
- **Entregable final:** una nueva revisión del memo (probablemente Rev C, por confirmar) con FY31 y FY39 para MS y MSO.

Reglas del usuario, vigentes:

- Trabajar solo en esta carpeta; la única excepción es `bench.py` de flac-bench.
- No actualizar la skill `flac2d-fish` con lo leído en los códigos de terceros.
- Las propiedades de los materiales se toman tal cual de los archivos; las discrepancias con el memo se informan y no se corrigen.
- Todo desplazamiento reportado, en puntos o en figuras, va **relativo a la base del modelo**.
- Pausar el posproceso cuando falte memoria en esta PC (7.7 GB).
- Las corridas sísmicas largas usan id `fy39-…`; los lotes cortos, `0-fy39-…`.

La memoria del proyecto está en `C:\Users\Wilson\.claude\projects\h--Mi-unidad-...-Memo-Autoridad\memory\` (índice en `MEMORY.md`). Conviene leerla primero.

## 2. Qué se hizo

| Etapa | Estado |
|---|---|
| Identificar la geometría del memo FY31 | Es la de los `.sav` y los DXF `FLAC * WT*`, no la de los DXF 1629 ni la de Slide |
| Geometría FY39 | Sello de fundación horizontal, corte 1.5H:1V en la arena limosa, sin remanentes absurdos, excavación simplificada (hecha en la etapa 0), napas WT1..WT6 que pasan por el pie FY39 |
| Malla FY39 propia, hecha con la skill | MS 8582 zonas, MSO 7126; 0 triángulos; ángulo mínimo 24–26° (igual al FY31) |
| Estático por etapas | MS OK (`0-fy39-03a`); MSO OK (`0-fy39-03c`) tras corregir los grupos de borde de `zone face skin` por posición |
| Periodo fundamental (corrida elástica sin amortiguar) | f1 MS 2.9 Hz, MSO 2.83 Hz; Rayleigh 0.005 centrado en f1 |
| **Dinámico MS FY39** (`fy39-05b`) | **Terminado.** Asentamiento relativo a la base A/B/C = 0.23/0.24/0.61 m (GM07) y 0.20/0.22/0.54 m (GM08). Resultados en `MS fy39/resultados/` |
| Sustento "FY39 ≈ FY31" para el cliente | Aprobado por el usuario: `sustento_asentamiento_FY39_vs_FY31.md` |
| Dinámico MSO FY39, relave MC heredado (`fy39-05c`) | **Falló:** el dt colapsó en t ≈ 26 s (GM07) y 32.8 s (GM08), en la superficie del relave junto a la franja elástica |
| MSO con relave PM4Silt | En curso (ver §4) |

## 3. Decisiones tomadas por el usuario

1. **MSO sin franja elástica:** el relave de x −169.4…−119.4 ya no se asigna elástico. La línea quedó comentada en `MSO fy39/04_dyn.dat`. Es una desviación respecto al FY31 y hay que declararla en el memo.
2. **El MS no se repite:** conserva la franja elástica del FY31 (Blocks 2, 5, 6, 7) y su relave MC φ28.
3. **Relave del MSO FY39 con PM4Silt genérico:**
   - Su_Rat 0.25;
   - G_o 282 y G_exp 0.70, que reproducen la Gmax del modelo;
   - pois 0.33;
   - **h_po 12**, calibrado con el DSS cíclico oficial: 14.5 ciclos a 3 % con CSR 0.175 y 45 ciclos con CSR 0.1375;
   - costra Mohr-Coulomb en superficie para σ'v < 40 kPa, con c = 10 kPa;
   - sin fase post-sismo.
4. Se sacaron de la cola del banco los lotes del usuario que no tenían resultado. Se movieron a `flac-bench-intercambio\ida_archivo` y se pueden reponer. El lote 37 del usuario sigue corriendo.
5. No se hacen corridas de sensibilidad para el cliente; basta el sustento.

## 4. Problema pendiente (bloqueante): estado inicial del relave PM4Silt del MSO

El estático por etapas deja el relave con **K ≈ Ka ≈ 0.34**. Con Su_Rat 0.25, 1266 de 1511 zonas parten con τ ≥ su. Se reimpuso K0 = 0.525 y se re-equilibró con el relave elástico, pero K relaja a 0.46:

- 341 zonas PM4 necesitan Su_Rat local de hasta **0.36 (1.43×)**, por encima del tope documentado de 1.3×;
- la costra tiene τ/c = 1.04.

El guardia cortó la corrida en los lotes `0-fy39-10b` y `0-fy39-10b2`, tal como estaba previsto. **Según la regla L-100, esto lo decide el usuario; no hay que subir el tope en silencio.**

- **En curso:** lote `0-fy39-10e-mso-diag-r`, que exporta τ/su por zona. Se procesa con `scratchpad/mapa_r_pm4.py`, que genera `mapa_r_pm4.png` y el resumen por zona.
- **Opciones para presentar al usuario, con el mapa:**
  - (a) subir Su_Rat global a ~0.30 y recalibrar h_po, para que sea consistente con K ≈ 0.4–0.46 del estático;
  - (b) mantener 0.25 y subir el tope local a ~1.45×, documentándolo;
  - (c) otra alternativa que proponga el usuario.

## 5. Próximos pasos

1. Leer el resultado de `0-fy39-10e`, correr `mapa_r_pm4.py` y presentar las opciones (a)/(b) al usuario.
2. Con la decisión, regenerar el lote con `python armar_lote10b.py 12.0`, ajustando los parámetros en `_pm4silt_mso.fis`. Enviarlo con un id nuevo (`0-fy39-10b3…`). Ese lote incluye el pre-sismo, la prueba sin sismo (L-83) y 2 s de GM07, que sirven para medir el costo por segundo de sismo.
3. **Solo cuando ese lote termine bien**, armar GM07/GM08 con `python armar_lote10c.py <id_lote_predyn>` y enviarlos con `--paralelo` y un timeout amplio (≥ 12 h por caso). La laptop corre lotes en paralelo: un lote que restaura de `done/<otro>` no puede enviarse antes de que el otro termine.
4. Posprocesar el MSO con `posproceso_fy39.py`: relativo a la base, amplificación espectral y de Fourier, ru del relave, figura de mecanismo. Verificar que el sustento "FY39 ≈ FY31" se cumpla también en el MSO. Copiar los resultados a `MSO fy39/resultados/`.
5. Redactar la nueva revisión del memo con FY31 y FY39, MS y MSO. Debe incluir:
   - geometría FY39;
   - malla y transmisión de ondas;
   - periodo y Rayleigh;
   - resultados relativos a la base;
   - amplificación;
   - el sustento aprobado;
   - las discrepancias heredadas;
   - las desviaciones del MSO FY39: sin franja elástica y relave PM4Silt genérico sin ensayos del sitio.

## 6. Archivos importantes

**Entregables (carpeta del proyecto)**
- `MS fy39/` y `MSO fy39/`:
  - `01_static.dat`, `04_dyn.dat` (MSO: franja elástica comentada), `Hist_points.dat`, `export_his.dat`;
  - `00_GEO_MS_1646.sav` y `00_GEO_MSO_1648.sav`;
  - `FLAC * FY39 WT1..6.dxf`, `geo_*_FY39.dxf/png`, `malla_*_FY39.png`.
  - Las copias `FLAC * FY31 WT*.dxf` dentro de estas carpetas sobran y no se usan.
- `MS fy39/resultados/`: tablas CSV y figuras PNG/PDF del MS (historias, asentamiento, corte, amplificación, periodo, mecanismo).
- `sustento_asentamiento_FY39_vs_FY31.md`: texto aprobado para el memo y notas internas.

**Scripts (scratchpad de la sesión: es TEMPORAL, copiar lo necesario antes de cerrar)**
`C:\Users\Wilson\AppData\Local\Temp\claude\h--Mi-unidad-...-Memo-Autoridad\7c4aa163-f31b-445d-8e25-a41a9dc95207\scratchpad\`

| Grupo | Archivos |
|---|---|
| Geometría y entregables | `geo_fy39.py`, `gen_fy39.py` |
| Estático y MS dinámico | `armar_lotes_run.py` |
| PM4Silt del MSO | `_pm4silt_mso.fis`, `armar_lote10cal.py`, `armar_lote10b.py`, `armar_lote10c.py`, `mapa_r_pm4.py` |
| Posproceso | `posproceso_fy39.py`, `analisis_mecanismo.py`, `figura_mecanismo.py`, `newmark_talud.py` |
| Diagnóstico | `_diag_dt.fis`, `armar_lote08dt.py`, `zona_colapso.py`, `relave_superficie.py`, `periodo.py`, `control_estatico.py` |
| Comunes | `_export_res.fis`, `_pp_muro_fy39.fis` |

**flac-bench**
- Uso: `python "H:\Mi unidad\00_WORKSPACE\50_HERRAMIENTAS_SOFTWARE\flac-bench\local\bench.py" pack|status|wait`.
- Los resultados quedan en la laptop en `C:/flac-bench/done/<id>/`.
- Base estática del MSO: `0-fy39-03c-mso-estatico/01_static_6-stage06`.

## 7. Discrepancias heredadas a declarar en el memo (sin corregir)

- El relave del MS en el dinámico es drenado (φ28), aunque el memo dice "no drenado".
- UBCHyst se usa también en el estático, aunque el memo dice Mohr-Coulomb.
- La cohesión y la densidad del dren difieren de las del 1A.
- El factor de la base es 0.8 en el MS y 0.85 en el MSO.
- Tracción en la roca elástica.
- Asentamiento acumulado del relave de 7–16 m en el estático.
- En el relave, f_max transmisible es solo 2–7 Hz; queda declarado como limitación.
