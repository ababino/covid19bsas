# Covid19 en Bs. As.
> Aplicamos el metodo de <a href='https://github.com/ababino/babino2020masks'>babino2020masks</a> a datos de la ciudad de buenos aires


## Install

`pip install babino2020masks`

## Import

```python
import numpy as np
import pandas as pd
from fastcore.all import *
from babino2020masks.core import *
from babino2020masks.lasso import *
from covid19bsas.core import *
```

## Get the Data

```python
df = get_bsas_data2()
```

```python
df.tail()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Tests</th>
      <th>Positives</th>
      <th>Negatives</th>
      <th>Odds</th>
      <th>p</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2021-12-16</th>
      <td>2021-12-16</td>
      <td>20459.0</td>
      <td>1244.0</td>
      <td>19215.0</td>
      <td>0.064741</td>
      <td>0.060805</td>
    </tr>
    <tr>
      <th>2021-12-17</th>
      <td>2021-12-17</td>
      <td>18486.0</td>
      <td>1401.0</td>
      <td>17085.0</td>
      <td>0.082002</td>
      <td>0.075787</td>
    </tr>
    <tr>
      <th>2021-12-18</th>
      <td>2021-12-18</td>
      <td>23305.0</td>
      <td>1180.0</td>
      <td>22125.0</td>
      <td>0.053333</td>
      <td>0.050633</td>
    </tr>
    <tr>
      <th>2021-12-19</th>
      <td>2021-12-19</td>
      <td>15074.0</td>
      <td>1074.0</td>
      <td>14000.0</td>
      <td>0.076714</td>
      <td>0.071249</td>
    </tr>
    <tr>
      <th>2021-12-20</th>
      <td>2021-12-20</td>
      <td>17363.0</td>
      <td>1404.0</td>
      <td>15959.0</td>
      <td>0.087975</td>
      <td>0.080862</td>
    </tr>
  </tbody>
</table>
</div>



## Plot

```python
df.plot(x='Date', y=['Positives', 'Tests'], secondary_y=['Tests'], figsize=(10, 7));
```


![png](docs/images/output_8_0.png)


```python
ax = plot_data_and_fit(df, 'Date', 'Odds', None, None, None, figsize=(10, 7))
ax.set_title(f'{df.tail(1).Date.iloc[0]:%B %d, %Y}, Positivity Odds:{df.tail(1).Odds.values[0]:2.3}');
```

    /home/andres/Documents/corona/babino2020masks/babino2020masks/core.py:156: UserWarning: Attempted to set non-positive bottom ylim on a log-scaled axis.
    Invalid limit will be ignored.
      ax.set_ylim([0.9*min_y, 1.1*max_y])



![png](docs/images/output_9_1.png)


```python
sdf = df.replace([np.inf, -np.inf], np.nan).dropna().copy()
lics = LassoICSelector(sdf['Odds'], 'bic')
lics.fit_best_alpha()
```

```python
#plt.close('all')
sdf['Fit'], sdf['Odds_l'], sdf['Odds_u'] = lics.odds_hat_l_u()
ax = plot_data_and_fit(sdf, 'Date', 'Odds', 'Fit', 'Odds_l', 'Odds_u', figsize=(10, 7))
ax.set_title(f'{sdf.tail(1).Date.iloc[0]:%B %d, %Y}, Fit Positivity Odds:{sdf.tail(1).Fit.values[0]:2.3}');
#plt.xlim([sdf.Date.min(), sdf.Date.max()])
#plt.show()
```


![png](docs/images/output_11_0.png)


```python
sdf['R'], sdf['Rl'], sdf['Ru'] = lics.rt()
ax = plot_data_and_fit(sdf[(sdf.Ru-sdf.Rl).gt(0)], 'Date', None, 'R', 'Rl', 'Ru', figsize=(10, 7), logy=False, palette=[colorblind[1],colorblind[1]])
ax.set_title(f'{sdf.tail(1).Date[0]:%B %d, %Y}, $R_t$:{sdf.tail(1).R.values[0]:2.3}');
```


![png](docs/images/output_12_0.png)

