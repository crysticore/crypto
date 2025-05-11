# Documentación de Limpieza y Análisis Exploratorio

## Dataset 1: NEAR

Tras la adquisición del dataset, se eliminaron columnas irrelevantes para el análisis: `Name` y `Symbol`, ya que todas las entradas correspondían exclusivamente a monedas **NEAR**. También se descartaron las columnas `Cat1` y `Cat2`, debido a que contenían únicamente valores nulos.

Después de esta limpieza, se conservaron las variables:
- `Date`
- `Volume`
- `MarketCap`
- `Price`

El dataset final contiene **137 observaciones completas**. Se transformó la columna `Date` al tipo `datetime` y se verificó que no existían registros duplicados.

### Outliers
Se detectaron los siguientes porcentajes de valores atípicos:
- `Volume`: **0.29%**
- `MarketCap`: **0%**
- `Price`: **4.27%**

Debido a que estos porcentajes son bajos, se consideró que no era necesario tratarlos para este análisis.

### Correlaciones

El análisis visual mediante un `pairplot` y un mapa de calor mostró las siguientes correlaciones relevantes:

| Variables           | Correlación |
|---------------------|-------------|
| MarketCap - Volume  | 0.55        |
| MarketCap - Price   | 0.88        |

Estas correlaciones tienen sentido, dado que el valor de capitalización de mercado está influenciado tanto por el volumen como por el precio de la moneda.

---

## Dataset 2: Coincodex

Este dataset contiene **1669 registros** con las siguientes variables:

- `Date`
- `Open`
- `High`
- `Low`
- `Close`
- `Volume`
- `MarketCap`

Se transformó `Date` al tipo `datetime` y se verificó que no existen valores nulos ni duplicados.

### Análisis Visual

El `pairplot` reveló fuertes relaciones entre las variables financieras (`Open`, `High`, `Low`, `Close`), lo cual es esperado en series temporales de precios.

El diagrama de caja evidenció **outliers en `Volume` y `MarketCap`**.

### Suavizado de MarketCap

Para eliminar los outliers en `MarketCap`, se aplicó un **suavizado exponencial simple** con la siguiente línea de código:

```python
from statsmodels.tsa.holtwinters import SimpleExpSmoothing
ses_model = SimpleExpSmoothing(data['Market Cap']).fit(smoothing_level=0.2, optimized=False)
```

Esto suavizó la serie de `MarketCap` y eliminó los outliers detectados.

### Porcentaje de Outliers

| Variable    | Porcentaje (%) |
|-------------|----------------|
| Open        | 4.73           |
| High        | 5.15           |
| Low         | 4.43           |
| Close       | 4.91           |
| Volume      | 7.91           |
| Market Cap  | 0.48           |

### Correlaciones Relevantes

| Variable 1    | Variable 2    | Correlación |
|---------------|---------------|-------------|
| Open          | High          | 0.997       |
| Open          | Low           | 0.997       |
| Open          | Close         | 0.993       |
| Open          | Volume        | 0.680       |
| Open          | Market Cap    | 0.855       |
| High          | Low           | 0.996       |
| High          | Close         | 0.997       |
| High          | Volume        | 0.693       |
| High          | Market Cap    | 0.850       |
| Low           | Close         | 0.996       |
| Low           | Volume        | 0.671       |
| Low           | Market Cap    | 0.861       |
| Close         | Volume        | 0.684       |
| Close         | Market Cap    | 0.854       |
| Volume        | Market Cap    | 0.728       |

**Nota:** La correlación entre `Volume` y `MarketCap` (0.728) es esperada, ya que un mayor volumen de operaciones suele estar asociado a un mayor interés del mercado, lo que también puede reflejarse en una capitalización de mercado más alta.

---
