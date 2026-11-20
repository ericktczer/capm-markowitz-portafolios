# Portafolios con CAPM y Markowitz para seis clientes simulados

Proyecto final de la materia de Finanzas (ESFM, IPN, diciembre de 2025), hecho en equipo de dos personas. Una firma de asesoría ficticia, *Little Duck Investments*, arma el plan de inversión inicial de seis clientes con perfiles de riesgo y capitales distintos. Todos los clientes y montos son ficticios.

> **Estado:** código de curso, adaptado a repositorio. Tiene errores metodológicos y de reproducibilidad conocidos, anotados en [`TODO.md`](TODO.md). No son recomendaciones de inversión.

## Qué hace el análisis

Para cada cliente, en R y Quarto:

1. Descarga precios con `quantmod` (Yahoo Finance) de acciones elegidas por sector según el perfil del cliente (12 en el caso de la familia) y del índice S&P 500 (`^GSPC`).
2. Estima la beta de cada acción por regresión contra el mercado y calcula el rendimiento esperado con **CAPM** y el **alfa de Jensen**. Conserva solo las acciones con alfa positivo.
3. Opcionalmente suma Bitcoin, Ethereum y BNB (según el perfil).
4. Construye con **Markowitz** el portafolio de **mínima varianza**, el **portafolio tangente** y un portafolio sobre la recta de mercado de capitales con un **retorno objetivo** por cliente.
5. Asigna el capital del cliente entre bonos del Tesoro de EE.UU. (tasa libre de riesgo), acciones y criptomonedas, y reporta retorno, riesgo anual e intervalos para cada portafolio.

## Clientes

| Archivo | Cliente | Capital (ficticio) | Retorno objetivo | Criptomonedas |
|---|---|---|---|---|
| `clientes/01_pareja.qmd` | Pareja joven | 300,000 | 25% | Sí |
| `clientes/02_basilio.qmd` | Basilio | 2,000,000 | 15% | Sí |
| `clientes/03_guillermo.qmd` | Guillermo | 1,500,000 | 12% | No |
| `clientes/04_armando.qmd` | Armando | 10,000,000 | 21% | Sí |
| `clientes/05_marcos.qmd` | Marcos | 3,500,000 | 13% | No |
| `clientes/06_familia.qmd` | Familia | 23,000,000 | 17% | Sí |

## Estructura

| Carpeta | Contenido |
|---|---|
| `clientes/` | Un documento Quarto por cliente (los seis son casi idénticos; ver `TODO.md`) |
| `reportes/` | PDF de cada cliente y `reporte_final_capm.pdf`, el reporte del proyecto |
| `_archivo/` | Plantillas vacías de Quarto y versiones anteriores. Se conservan por si hacen falta; no forman parte del análisis |

## Cómo reproducirlo

Necesita R, Quarto, una distribución de LaTeX (por ejemplo TinyTeX) y conexión a internet, porque los precios se descargan al renderizar.

```r
install.packages(c("tidyverse", "quantmod", "ggplot2", "ggfortify", "dbplyr", "conflicted"))
```

```bash
quarto render clientes/01_pareja.qmd
```

Estas instrucciones se escribieron leyendo los archivos. **No se renderizó nada** al preparar el repositorio (R y Quarto no estaban instalados en el entorno de revisión). Además, los resultados **cambian en cada ejecución** porque la fecha final es la de hoy y los precios se descargan en ese momento; ver `TODO.md`.

## Siguientes pasos

Todo lo pendiente está en [`TODO.md`](TODO.md), ordenado por prioridad.
