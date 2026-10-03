# USD/COP Statistical Learning

Proyecto de análisis y aprendizaje estadístico para estudiar si la información financiera y macroeconómica disponible en una fecha `t` ayuda a anticipar la dirección del USD/COP cinco días hábiles después.

## Pregunta de investigación

¿En qué medida los movimientos recientes del USD/COP, el petróleo WTI, el VIX, el mercado accionario estadounidense y las tasas de interés contienen información útil para clasificar si la TRM subirá durante los siguientes cinco días hábiles?

## Primera entrega

Esta etapa se concentra en adquisición, limpieza, integración, construcción de variables, definición del target, análisis descriptivo y correlaciones. Todavía no se entrenan modelos ni se reportan métricas fuera de muestra.

El notebook principal es [`notebooks/01_usdcop_analysis.ipynb`](notebooks/01_usdcop_analysis.ipynb).

## Variables

Se utilizan siete series base: USD/COP, WTI, VIX, SPY, EFFR, tasa de política monetaria de Colombia y Treasury 10Y. A partir de ellas se construyen retornos de 1, 5 y 20 días, volatilidad móvil, medias móviles, cambios recientes y el diferencial `rate_spread = col_rate - fed_rate`.

El objetivo `target_5d` vale `1` si el USD/COP es mayor cinco días hábiles después y `0` en caso contrario. El diccionario completo está en [`docs/data_dictionary.md`](docs/data_dictionary.md).

## Fuentes

- TRM USD/COP y tasa de política monetaria: Banco de la República.
- WTI, VIX, EFFR y Treasury 10Y: FRED.
- SPY: Yahoo Finance.

Los archivos descargados se conservan en `data/raw/` y las bases resultantes en `data/processed/`. Ver [`data/README.md`](data/README.md).

## Hallazgos iniciales

- El dataset final contiene 5.624 observaciones con target y 28 predictores antes de una reducción para modelado.
- La clase está relativamente balanceada: aproximadamente 51,6 % de `target_5d = 0` y 48,4 % de `target_5d = 1`.
- La correlación individual con el target es débil; la mayor magnitud observada es la de `spy_ret_1d` (aprox. -0,12).
- Hay redundancia entre niveles y transformaciones: `usdcop` con sus medias móviles, `fed_rate` con `us10y`, y `col_rate` con `rate_spread`.

### Conjunto reducido actual: 14 predictores

Para evitar un conjunto demasiado grande, se creó `data/processed/model_dataset_reduced.csv` con exactamente 14 predictores. La selección cubre comportamiento reciente del USD/COP, petróleo, riesgo global y tasas:

```text
usdcop_ret_1d       usdcop_ret_5d       usdcop_ret_20d
usdcop_vol_20d      wti_ret_1d          wti_ret_5d
wti_ret_20d         vix                 spy_ret_1d
spy_ret_5d          us10y               us10y_change_5d
rate_spread         rate_spread_change_5d
```

Con esta selección no queda ningún par con correlación absoluta mayor o igual a 0,70 en la revisión actual. Se retiraron los niveles `usdcop` y `spy`, las medias móviles, `usdcop_ma_gap`, la volatilidad de cinco días, los cambios del VIX y los niveles/cambios redundantes de `fed_rate` y `col_rate`.

Esta es una decisión de reducción, no una conclusión de importancia predictiva. El dataset completo se conserva en `model_dataset.csv` para comparar resultados con la versión reducida.

## Estructura

```text
usd-cop-statistical-learning/
├── README.md
├── LICENSE
├── requirements.txt
├── data/
│   ├── README.md
│   ├── raw/
│   └── processed/
├── notebooks/
│   └── 01_usdcop_analysis.ipynb
└── docs/
    ├── first_delivery.md
    └── data_dictionary.md
```

## Ejecución rápida

```bash
py -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/01_usdcop_analysis.ipynb
```

El notebook descarga algunas series externas, por lo que la ejecución completa requiere conexión a internet.

## Limitaciones y trabajo futuro

El calendario usa días hábiles de lunes a viernes y `forward fill`; todavía no incorpora explícitamente todos los festivos. Las correlaciones son descriptivas y no prueban causalidad ni capacidad predictiva. La siguiente etapa debe implementar un baseline, una división temporal entrenamiento/prueba y modelos evaluados fuera de muestra.

## Licencia

Este proyecto se distribuye bajo la licencia MIT. Ver [`LICENSE`](LICENSE).
