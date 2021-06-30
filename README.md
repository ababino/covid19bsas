# Covid19 en Bs. As.
> Aplicamos el metodo de <a href='https://github.com/ababino/babino2020masks'>babino2020masks</a> a datos de la ciudad de buenos aires


## Install

`pip install babino2020masks`

## Import

```python
import pandas as pd
from fastcore.all import *
from babino2020masks.core import *
from babino2020masks.lasso import *
from covid19bsas.core import *
```

    Matplotlib is building the font cache; this may take a moment.


## Get the Data

```python
df = get_bsas_data()
```

## Plot

```python
df.plot(x='Date', y=['Positives', 'Tests'], secondary_y=['Tests']);
```


![png](docs/images/output_7_0.png)


```python
ax = plot_data_and_fit(df, 'Date', 'Odds', None, None, None, figsize=(10, 7))
ax.set_title(f'{df.tail(1).Date[0]:%B %d, %Y}, Positivity Odds:{df.tail(1).Odds.values[0]:2.3}');
```


![png](docs/images/output_8_0.png)


```python
sdf = df.dropna().copy()
lics = LassoICSelector(sdf['Odds'], 'bic')
lics.fit_best_alpha()
```

```python
sdf['Fit'], sdf['Odds_l'], sdf['Odds_u'] = lics.odds_hat_l_u()
ax = plot_data_and_fit(sdf, 'Date', 'Odds', 'Fit', 'Odds_l', 'Odds_u', figsize=(10, 7))
```


![png](docs/images/output_10_0.png)


```python
sdf['R'], sdf['Rl'], sdf['Ru'] = lics.rt()
ax = plot_data_and_fit(sdf, 'Date', None, 'R', 'Rl', 'Ru', figsize=(10, 7), logy=False, palette=[colorblind[1],colorblind[1]])
```


![png](docs/images/output_11_0.png)

