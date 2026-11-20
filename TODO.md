# TODO: portafolios con CAPM y Markowitz

Pendientes encontrados al adaptar el proyecto de curso a un repositorio. Ninguno se corrigió: este proyecto no se pensó para GitHub y aquí solo se anotan.

**Cómo se revisó:** leyendo los `.qmd` y el reporte en PDF. R y Quarto no estaban instalados en el entorno de revisión, así que **nada se renderizó ni se ejecutó**. Los números de línea son de `clientes/06_familia.qmd`; los otros cinco archivos son casi iguales (los de Guillermo y Marcos, que no llevan criptomonedas, tienen unas 29 líneas menos). Donde dice "verificar", no estoy seguro de que sea un error.

## Antes de publicar (bloqueantes)

- [ ] **Coautoría.** Es un trabajo en equipo de dos personas. `reportes/reporte_final_capm.pdf` trae la portada con ambos nombres, el del profesor y la materia. Confirma con tu compañera que está de acuerdo en publicarlo y cómo quiere aparecer. Si no, no subas ese PDF. Los `.qmd` de cada cliente no tienen campo de autor en el encabezado, pero revisa también los PDF de `reportes/` antes de publicarlos.
- [ ] **Nombres dentro del escenario.** El reporte describe a "Erick y Estefanía" como la pareja y a la "Familia Ibarra" como el cliente de 23 millones. Esos nombres coinciden con los del equipo. Decide si lo dejas o lo anonimizas.
- [ ] **Elegir una licencia.** Sin `LICENSE`, el código queda con todos los derechos reservados.

## Reproducibilidad

- [ ] **Los resultados cambian cada día.** `to_date <- Sys.Date()` (línea 85): cada render usa una ventana distinta. Fija una fecha final.
- [ ] **Tasa libre de riesgo escrita a mano y repetida.** El texto dice "a día de hoy 3.48%" (línea 58), y `3.48` aparece en `Rf_ann` (línea 88) y de nuevo en `R.f` (línea 263). Define el valor una sola vez, con fecha y fuente.
- [ ] **Los precios se descargan en cada render** (líneas 93 a 105 y 189 a 192), sin caché. Si Yahoo falla o cambia, el documento cambia o se rompe. Guarda los datos usados en `data/` (ya está en `.gitignore` por los términos de uso de Yahoo; documenta cómo regenerarlos).
- [ ] **Sin lista de dependencias ni versiones.** Considera `renv`.
- [ ] **`Rf_daily` se modifica dos veces.** Se reasigna dividiéndola entre 100 en la línea 301 y otra vez en la 340. Si se re-ejecuta un chunk o se corren fuera de orden, el valor cambia. Usa nombres distintos para cada conversión.

## Posibles errores de método (verificar)

- [ ] **Mezcla de rendimientos logarítmicos y simples.** Los rendimientos son logarítmicos (`diff(log(...))`, líneas 95 y 113). Pero el objetivo se convierte como si fuera simple, `(1 + 0.17)^(1/252) - 1` (línea 298), y se compara con la media de rendimientos logarítmicos (`mu_tg_dec`, línea 302). La anualización `(1 + mu/100)^252 - 1` (líneas 344 y 382) también trata una media logarítmica como simple. Usa una sola convención en todo el documento.
- [ ] **Intervalos de confianza probablemente demasiado estrechos** (líneas 350-351, 371-372, 388-389). Usan `ret_anual ± 3.291 × (sigma_anual / √n)` con `n` observaciones diarias. El error estándar de la media anualizada es `sigma_diaria × 252 / √n = sigma_anual × √252 / √n`, es decir, unas √252 ≈ 16 veces más ancho. Además, 3.291 corresponde a 99.9% de confianza: documenta por qué.
- [ ] **Selección de acciones con el mismo periodo que se usa para optimizar** (filtro `Alpha_J > 0`, línea 175). Sesgo de selección: se eligen las acciones que ya tuvieron alfa positivo en la ventana y luego se evalúa el portafolio en esa misma ventana. No hay validación fuera de muestra.
- [ ] **Beta por regresión sobre rendimientos, no sobre excesos de rendimiento** (línea 135). El CAPM se estima normalmente con `Ri − rf` contra `Rm − rf`. La beta cambia poco, pero el alfa de Jensen se interpreta distinto.
- [ ] **`capm_jensen()` recibe `Rf_daily` pero no lo usa** (línea 129); lee `Rf_ann` del entorno global (línea 147). Pasa el valor como argumento.
- [ ] **Pesos sin restricción de signo.** El portafolio de mínima varianza y el tangente (líneas 226 y 268) se calculan con la fórmula cerrada sin exigir pesos positivos, así que pueden tener posiciones cortas. En la asignación mixta se usa `abs()` para calcular porcentajes (línea 462), lo que oculta el signo. Documenta que se permiten cortos, o impón pesos no negativos.
- [ ] **Criptomonedas.** Se anualizan con 252 días y se alinean con días bursátiles (`merge(..., all = FALSE)`, línea 206), así que se descartan fines de semana. No pasan por el filtro de alfa positivo y siempre se incluyen.

## Errores de código

- [ ] **Índices fijos en la asignación mixta**: `w_abs[1:9]` y `w_abs[10:12]` (líneas 464 y 465) suponen 9 acciones y 3 criptos. Si el filtro de alfa selecciona otra cantidad (y depende de la fecha), los porcentajes salen mal. Usa nombres.
- [ ] **Tabla de nombres incompleta** (`nombres`, líneas 412 a 425): faltan INTC, LOGI y HSY, que son parte de la lista de partida. Si alguna entra en la selección, saldrá su símbolo en lugar de su nombre.
- [ ] **Variables con "15" en el nombre** (`w_tg_15`, `w_rf_15`, `ret_15_ann`, `sig_15_ann`; líneas 306, 307 y 361 a 378) en todos los clientes, aunque el objetivo va de 12% a 25%. Las etiquetas visibles sí coinciden con el objetivo de cada cliente en los archivos finales.
- [ ] **Descarga de acciones con `tryCatch(... NULL)`** (líneas 100 a 103): si un ticker falla se descarta sin avisar. Las criptos (líneas 189 a 192) no tienen ese manejo.
- [ ] **Límites de gráfica fijos** (`seq(0, 4)` y `seq(0, 0.5)`, líneas 234 y 235). Si los datos cambian, los puntos pueden quedar fuera de la gráfica.
- [ ] **Gráficas de los incisos (a) y (b) no se imprimen**: se asignan a `q` (líneas 244 y 278) y solo se muestra en el inciso (c).
- [ ] **Paquetes sin uso**: `dbplyr` y `conflicted` (líneas 63 y 64) no parecen usarse, y `tidyverse` ya incluye `dplyr` y `ggplot2`. El operador `%||%` (línea 408) requiere R 4.4 o posterior.
- [ ] **Texto de cliente con erratas** en los `.qmd` (por ejemplo "mayoria", "asímismo", "Treasury Bilss"). Corregir al limpiar.

## Estructura del repositorio

- [ ] **Los seis `.qmd` son casi idénticos** (unas 480 líneas cada uno; solo cambian el texto, la lista de acciones, `mu_obj_ann` y `total`). Una sola función en `R/` más un archivo de configuración por cliente (o un `.qmd` con `params`) evitaría repetir los errores seis veces.
- [ ] Los PDF de `reportes/` se generaron con las versiones de los `.qmd` de ese momento. Vuelve a renderizar y verifica que coinciden.
- [ ] `_archivo/` tiene: tres plantillas vacías de Quarto, una versión anterior de Basilio (con PDF y figuras), `6 PF.qmd` (versión de Familia con "15%" en el objetivo de 17%) y `6-PF.rmarkdown` (archivo intermedio de Quarto). Revisa si los borras antes de publicar.
