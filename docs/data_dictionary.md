# Diccionario de datos

| Variable | Descripción | Tipo o transformación |
|---|---|---|
| `date` | Fecha de observación | Índice temporal |
| `usdcop` | Tasa Representativa del Mercado USD/COP | Nivel |
| `wti` | Precio del petróleo WTI | Nivel |
| `vix` | Índice de volatilidad del mercado estadounidense | Nivel |
| `spy` | Proxy del S&P 500 mediante ETF SPY | Nivel |
| `fed_rate` | Effective Federal Funds Rate | Nivel |
| `col_rate` | Tasa de política del Banco de la República | Nivel |
| `us10y` | Rendimiento Treasury estadounidense a 10 años | Nivel |
| `rate_spread` | `col_rate - fed_rate` | Diferencial |
| `*_ret_1d`, `*_ret_5d`, `*_ret_20d` | Retornos recientes de USD/COP, WTI o SPY | Variación porcentual |
| `usdcop_vol_5d`, `usdcop_vol_20d` | Volatilidad histórica del USD/COP | Desviación estándar móvil |
| `usdcop_ma_5`, `usdcop_ma_20` | Medias móviles de la TRM | Nivel suavizado |
| `usdcop_ma_gap` | Distancia relativa de la TRM frente a `usdcop_ma_20` | Tendencia |
| `vix_change_1d`, `vix_change_5d` | Cambios recientes del VIX | Variación porcentual |
| `*_change_5d` | Cambio de cinco días en tasas o diferencial | Diferencia |
| `usdcop_future_5d` | USD/COP cinco días hábiles adelante | Variable futura |
| `future_return_5d` | Retorno futuro a cinco días | Variable futura |
| `target_5d` | 1 si el USD/COP sube; 0 si no sube | Target binario |

Las variables futuras solo se usan para construir y describir el target; nunca deben entrar como predictores.
