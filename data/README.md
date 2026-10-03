# Datos

Esta carpeta separa los insumos descargados de las bases generadas por el notebook.

## `raw/`

Contiene archivos originales o descargados desde las fuentes, por ejemplo:

- `graficador_series.xlsx`: TRM del USD/COP.
- `colombia_policy_rate.xlsx`: tasa de política monetaria colombiana.
- `wti.csv` y `vix.csv`: series descargadas de FRED.

SPY, EFFR y Treasury 10Y se descargan durante la ejecución del notebook. Si se desea conservar una copia reproducible, debe guardarse en `raw/` con una fecha o versión claramente identificada.

## `processed/`

Contiene archivos generados por [`notebooks/01_usdcop_analysis.ipynb`](../notebooks/01_usdcop_analysis.ipynb):

- `trm_clean.csv`: TRM limpia.
- `master_dataset.csv`: integración de las siete series base.
- `features_dataset.csv`: variables derivadas sin target.
- `model_dataset.csv`: variables derivadas más `future_return_5d` y `target_5d`.
- `model_dataset_reduced.csv`: 14 predictores seleccionados para la siguiente etapa más `target_5d`.

Los CSV procesados pueden regenerarse; no deben editarse manualmente para cambiar la lógica del análisis.
