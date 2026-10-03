# Primera entrega: análisis exploratorio USD/COP

## Problema y objetivos

El proyecto estudia si las condiciones financieras y macroeconómicas observables hasta una fecha `t` contienen información útil para anticipar la dirección del USD/COP durante los cinco días hábiles siguientes. La primera entrega construye una base reproducible y deja preparado el problema de clasificación binaria.

## Datos

Se integran la TRM USD/COP, WTI, VIX, SPY, EFFR, tasa de política colombiana y Treasury 10Y. El notebook alinea las series en un calendario de días hábiles y usa `forward fill` para conservar el último valor conocido.

El resultado disponible en `data/processed/model_dataset.csv` tiene 5.624 observaciones, 28 predictores y el target `target_5d`.

## Decisiones metodológicas

Se construyen retornos de 1, 5 y 20 días, volatilidad móvil de la TRM, medias móviles, cambios de cinco días y el diferencial de tasas. Las primeras observaciones sin historial suficiente se eliminan. El target se calcula con información futura de forma intencional y las últimas cinco filas se excluyen por no tener horizonte completo.

No se aplican modelos en esta etapa. El análisis de correlación se usa para detectar redundancia, no para afirmar causalidad ni seleccionar variables de manera definitiva.

## Reducción a 14 predictores

Para evitar que la primera versión del modelo sea demasiado grande, se generó `data/processed/model_dataset_reduced.csv` con 14 predictores. La selección final es:

```text
usdcop_ret_1d, usdcop_ret_5d, usdcop_ret_20d, usdcop_vol_20d,
wti_ret_1d, wti_ret_5d, wti_ret_20d, vix,
spy_ret_1d, spy_ret_5d, us10y, us10y_change_5d,
rate_spread, rate_spread_change_5d
```

La selección mantiene varios horizontes para retornos, una medida de volatilidad, riesgo global y dos representaciones de tasas. En la revisión actual no queda ningún par con `|r| >= 0,70` entre estos 14 predictores.

Las variables retiradas y la razón principal son:

- `usdcop`, `spy`, `usdcop_ma_5` y `usdcop_ma_20`: niveles muy correlacionados y tendencias comunes;
- `usdcop_ma_gap`: correlación de 0,859 con `usdcop_ret_20d`;
- `usdcop_vol_5d`: correlación de 0,750 con `usdcop_vol_20d`;
- `fed_rate` y `col_rate`: redundancia con `us10y` y `rate_spread`;
- `fed_rate_change_5d` y `col_rate_change_5d`: se priorizó el cambio del diferencial;
- `vix_change_1d` y `vix_change_5d`: se conserva el nivel `vix` para evitar la pareja altamente correlacionada con retornos de SPY.

Como referencia, en el conjunto completo los pares más altos con umbral `|r| >= 0,70` incluyen:

| Par | Correlación aproximada | Decisión preliminar |
|---|---:|---|
| `usdcop` – `usdcop_ma_5` | 0,999 | retirar niveles redundantes |
| `usdcop` – `usdcop_ma_20` | 0,997 | retirar niveles redundantes |
| `usdcop_ma_5` – `usdcop_ma_20` | 0,998 | retirar medias como predictores iniciales |
| `usdcop_ret_20d` – `usdcop_ma_gap` | 0,859 | conservar solo `usdcop_ret_20d` inicialmente |
| `fed_rate` – `us10y` | 0,798 | conservar `us10y` y retirar `fed_rate` inicialmente |
| `col_rate` – `rate_spread` | 0,777 | conservar `rate_spread` y retirar `col_rate` |
| `usdcop_vol_5d` – `usdcop_vol_20d` | 0,750 | conservar `usdcop_vol_20d` |
| `spy_ret_1d` – `vix_change_1d` | -0,727 | conservar ambas solo si el modelo lo justifica |

También aparecen correlaciones altas entre niveles de `spy` y `usdcop` (`0,826`), probablemente influenciadas por tendencias comunes de largo plazo. Por eso se recomienda usar retornos y no niveles en la primera versión del modelo.

La reducción no borra columnas del dataset original: deja un criterio claro para comparar un modelo completo contra uno más compacto en la siguiente entrega.

## Hallazgos

La variable objetivo está relativamente balanceada: cerca de 51,6 % de observaciones en la clase 0 y 48,4 % en la clase 1. Las correlaciones individuales con el target son débiles; la mayor magnitud en la revisión fue `spy_ret_1d`, aproximadamente -0,12.

Esto sugiere que no debe esperarse una señal lineal fuerte de una variable aislada. La utilidad predictiva, si existe, debe verificarse mediante combinaciones de variables y evaluación fuera de muestra.

## Conclusión y siguiente paso

Con esta etapa quedan listas las bases, las variables y el target. El siguiente paso es comparar, con división temporal, un baseline, el conjunto completo y el conjunto reducido. Esa comparación debe usar métricas fuera de muestra y, si hace falta, VIF o regularización para revisar la selección.
