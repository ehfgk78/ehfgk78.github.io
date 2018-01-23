---
layout: post
title:  "데이터 사이언스 07 시계열 데이터 02-주가예측-prophet"
date: 2018-01-22 00:00 +0900
categories: [Data-Science]
tags: [Data-Science, prophet]
---

* Kramdown table of contents
{:toc .toc}

# 1. 주가예측 - statsmodels

> [파이썬으로 배우는 알고리즘 트레이딩 - pandas_datareader모듈](https://wikidocs.net/4370)

> [파이썬으로 주식데이터 가져오기](https://gomjellie.github.io/%ED%8C%8C%EC%9D%B4%EC%8D%AC/pandas/%EC%A3%BC%EC%8B%9D/2017/06/09/pandas-datareader-stock.html)

> [Pandas 에서 주가 데이터 가져오기 - 안수찬 블로그](https://ansuchan.com/pandas-with-kospi/)



## 1) 모듈명 변경 및 설치 

> [pandas-datareader 공식문서](https://pandas-datareader.readthedocs.io/)

* 과거 pandas.io.data 모듈이  `pandas_datareader.data`로 바뀜 
* 모듈 설치

```sh
➜  pip install pandas-datareader

Collecting pandas-datareader
  Downloading pandas_datareader-0.5.0-py2.py3-none-any.whl (74kB)
    100% |████████████████████████████████| 81kB 606kB/s 
Installing collected packages: requests-ftp, requests-file, pandas-datareader
  Running setup.py install for requests-ftp ... done
Successfully installed pandas-datareader-0.5.0 requests-file-1.4.3 requests-ftp-0.3.1
```

## 2) 대한항공 주가 

* 종목 코드 : 003490
*  2015년 1월 1일부터 현재까지의 주가



```python
import warnings
warnings.filterwarnings("ignore")

import itertools
import pandas as pd
import numpy as np

import statsmodels.api as sm
import matplotlib.pyplot as plt
plt.style.use('fivethirtyeight')
%matplotlib inline

# 한글폰트 
import matplotlib.font_manager as fm
# 폰트 적용
font_location = '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf'
font_name = fm.FontProperties(fname=font_location).get_name()

from matplotlib import rc
rc('font', family=font_name)
```


```python
import pandas_datareader.data as web
from datetime import datetime
```


```python
# 대한항공 2015. 1. 1.부터 현재까지의 주가 

start = datetime( 2015, 1, 1 )
end = datetime.now()

# yahoo 파이낸스 
KA = web.DataReader('003490.KS', 'yahoo', start, end)
```


```python
KA.head()
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
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
    <tr>
      <th>Date</th>
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
      <th>2015-01-02</th>
      <td>43374.101563</td>
      <td>44282.398438</td>
      <td>40966.898438</td>
      <td>41012.300781</td>
      <td>41012.300781</td>
      <td>1062595</td>
    </tr>
    <tr>
      <th>2015-01-05</th>
      <td>41330.300781</td>
      <td>42329.500000</td>
      <td>40194.800781</td>
      <td>41966.101563</td>
      <td>41966.101563</td>
      <td>912246</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>42374.898438</td>
      <td>43192.398438</td>
      <td>41466.500000</td>
      <td>41466.500000</td>
      <td>41466.500000</td>
      <td>948055</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>39059.398438</td>
      <td>40558.199219</td>
      <td>37197.199219</td>
      <td>39513.601563</td>
      <td>39513.601563</td>
      <td>3256086</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>39604.398438</td>
      <td>40240.199219</td>
      <td>38605.199219</td>
      <td>39331.898438</td>
      <td>39331.898438</td>
      <td>549048</td>
    </tr>
  </tbody>
</table>
</div>




```python
KA['Close'].plot(
    style='--',
    figsize=(12, 6)
)
pd.rolling_mean( KA['Close'], 7).plot( lw=2 )
plt.title('대한항공 종가 시세')
plt.legend( ['종가시세', '이동평균선(7일)' ] )
plt.show()
```


![png]({{ site.url }}/data/dataScience/07-02img/output_5_0.png)



## 3) 삼성전자 주가

* 종목 코드:  005930    


```python
start = datetime( 2010, 1, 1 )
end = datetime.now()

SamSung = web.DataReader('005930.KS', 'yahoo', start, end)
SamSung['Close'].plot(
    style='--',
    figsize=(12, 6)
)
pd.rolling_mean( KA['Close'], 7).plot( lw=2 )
plt.title('삼성전자')
plt.legend( ['종가시세', '이동평균선(7일)' ] )
plt.show()
```


![png]({{ site.url }}/data/dataScience/07-02img/output_7_0.png)



```python
SamSung.head(3)
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
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
    <tr>
      <th>Date</th>
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
      <th>2010-01-04</th>
      <td>803000.0</td>
      <td>809000.0</td>
      <td>800000.0</td>
      <td>809000.0</td>
      <td>740499.0625</td>
      <td>239016</td>
    </tr>
    <tr>
      <th>2010-01-05</th>
      <td>826000.0</td>
      <td>829000.0</td>
      <td>815000.0</td>
      <td>822000.0</td>
      <td>752398.2500</td>
      <td>558517</td>
    </tr>
    <tr>
      <th>2010-01-06</th>
      <td>829000.0</td>
      <td>841000.0</td>
      <td>826000.0</td>
      <td>841000.0</td>
      <td>769789.5000</td>
      <td>458977</td>
    </tr>
  </tbody>
</table>
</div>




```python
len(SamSung)
```




    1991




```python
from pylab import rcParams
rcParams['figure.figsize'] = 12, 8

# 삼성전자 주식 종가에 대한 분해
y = SamSung['Close'][ 1600 : ] 

decomposition = sm.tsa.seasonal_decompose(  y, freq=12 )

fig = decomposition.plot()
plt.show()
```


![png]({{ site.url }}/data/dataScience/07-02img/output_10_0.png)



```python
# Define the p, d and q parameters to take any value between 0 and 2
p = d = q = range(0, 2)

# Generate all different combinations of p, q and d triplets
pdq = list( itertools.product(p, d, q))

# Generate all different combinations of seasonal p, q and d triplets
seasonal_pdq = [ (x[0], x[1], x[2], 12)  for x in pdq ]

print('Example of parameter combinations for Seasonal ARIMA ...')
print('SARIMAX:  {} x {}'.format(pdq[1], seasonal_pdq[1]) )
print('SARIMAX:  {} x {}'.format(pdq[1], seasonal_pdq[2]) )
print('SARIMAX:  {} x {}'.format(pdq[2], seasonal_pdq[3]) )
print('SARIMAX:  {} x {}'.format(pdq[2], seasonal_pdq[4]) )
```

    Example of parameter combinations for Seasonal ARIMA ...
    SARIMAX:  (0, 0, 1) x (0, 0, 1, 12)
    SARIMAX:  (0, 0, 1) x (0, 1, 0, 12)
    SARIMAX:  (0, 1, 0) x (0, 1, 1, 12)
    SARIMAX:  (0, 1, 0) x (1, 0, 0, 12)



```python
warnings.filterwarnings("ignore")

y = SamSung['Close']

select_candi = 10000000
param_candi = ( 0, 0, 0 )
param_seasonal_candi = ( 0, 0, 0)

count=0
end_count = len(pdq)

for param in pdq:   
    for param_seasonal in seasonal_pdq:
        try:
            mod = sm.tsa.statespace.SARIMAX( 
                                            y,
                                            order=param,
                                            seasonal_order=param_seasonal,
                                            enforce_stationarity=False,
                                            enforce_invertibility=False
                                           )
            results = mod.fit()
            count += 1
            if count <= 5:
                print('ARIMA{}x{}12 - AIC:{}'.format(param, param_seasonal, results.aic))
            
            if results.aic < select_candi:
                select_candi = results.aic
                param_candi = param
                param_seasonal_candi = param_seasonal
        except:
            continue
            
print(param_candi, param_seasonal_candi, select_candi)            
```

    ARIMA(0, 0, 0)x(0, 0, 1, 12)12 - AIC:60253.297878071855
    ARIMA(0, 0, 0)x(0, 1, 1, 12)12 - AIC:49644.270908598235
    ARIMA(0, 0, 0)x(1, 0, 0, 12)12 - AIC:49928.79116493459
    ARIMA(0, 0, 0)x(1, 0, 1, 12)12 - AIC:49903.76087716084
    ARIMA(0, 0, 0)x(1, 1, 0, 12)12 - AIC:49669.749916549496
    (1, 1, 1) (0, 0, 1, 12) 45391.1132768003



```python
mod = sm.tsa.statespace.SARIMAX(
    y,
    order=(1, 1, 1),
    seasonal_order=(0, 0, 1, 12),
    enforce_stationarity=False,
    enforce_invertibility=False
)

results = mod.fit()

print( results.summary().tables[1] )
```

    ==============================================================================
                     coef    std err          z      P>|z|      [0.025      0.975]
    ------------------------------------------------------------------------------
    ar.L1         -0.6241      0.182     -3.431      0.001      -0.981      -0.268
    ma.L1          0.6733      0.172      3.925      0.000       0.337       1.010
    ma.S.L12       0.0194      0.019      0.997      0.319      -0.019       0.058
    sigma2      5.519e+08   5.22e-11   1.06e+19      0.000    5.52e+08    5.52e+08
    ==============================================================================



```python
results.plot_diagnostics( figsize=(12, 10) )
plt.show()
```


![png]({{ site.url }}/data/dataScience/07-02img/output_14_0.png)



```python
pred = results.get_prediction(
    start=pd.to_datetime('2015-1-2'),
    dynamic=False
)

pred_ci = pred.conf_int()
```


```python
# 관측 데이터  1973년 부터 끝까지 
ax = y[ '2000' : ].plot( label='observed', figsize=(12, 8) )

# 예측
pred.predicted_mean.plot(
    ax=ax,
    label='One-step ahead Forecast',
    alpha=.7
)

ax.fill_between(
    pred_ci.index,
    pred_ci.iloc[ : , 0 ],
    pred_ci.iloc[ : , 1 ],
    color='k',
    alpha=.2
)

ax.fill_betweenx(
    ax.get_ylim(),
    pd.to_datetime('2015-01-01'),
    y.index[-1],
    alpha=.3,
)

ax.set_xlabel('Date')
ax.set_ylabel('best')
plt.legend()

plt.show()
```


![png]({{ site.url }}/data/dataScience/07-02img/output_16_0.png)



```python
y = SamSung['Close'].resample('MS').mean()
```


```python
warnings.filterwarnings("ignore")

select_candi = 10000000
param_candi = ( 0, 0, 0 )
param_seasonal_candi = ( 0, 0, 0)

count=0
end_count = len(pdq)

for param in pdq:   
    for param_seasonal in seasonal_pdq:
        try:
            mod = sm.tsa.statespace.SARIMAX( 
                                            y,
                                            order=param,
                                            seasonal_order=param_seasonal,
                                            enforce_stationarity=False,
                                            enforce_invertibility=False
                                           )
            results = mod.fit()
            count += 1
            if count <= 5:
                print('ARIMA{}x{}12 - AIC:{}'.format(param, param_seasonal, results.aic))
            
            if results.aic < select_candi:
                select_candi = results.aic
                param_candi = param
                param_seasonal_candi = param_seasonal
        except:
            continue
            
print(param_candi, param_seasonal_candi, select_candi)        
```

    ARIMA(0, 0, 0)x(0, 0, 1, 12)12 - AIC:2588.3600182104815
    ARIMA(0, 0, 0)x(0, 1, 1, 12)12 - AIC:2077.9442515545934
    ARIMA(0, 0, 0)x(1, 0, 0, 12)12 - AIC:2411.5750802284015
    ARIMA(0, 0, 0)x(1, 0, 1, 12)12 - AIC:2388.3744838038806
    ARIMA(0, 0, 0)x(1, 1, 0, 12)12 - AIC:2100.2859990842962
    (0, 1, 1) (1, 1, 1, 12) 1798.848144371688



```python
mod = sm.tsa.statespace.SARIMAX(
    y,
    order=( 0, 1, 1 ),
    seasonal_order=( 1, 1, 1, 12 ),
    enforce_stationarity=False,
    enforce_invertibility=False
)

results = mod.fit()

print( results.summary().tables[1] )
```

    ==============================================================================
                     coef    std err          z      P>|z|      [0.025      0.975]
    ------------------------------------------------------------------------------
    ma.L1          0.2222      0.243      0.916      0.360      -0.253       0.698
    ar.S.L12      -0.8107      0.208     -3.888      0.000      -1.219      -0.402
    ma.S.L12      -0.0022      0.278     -0.008      0.994      -0.546       0.542
    sigma2      1.145e+10   7.04e-12   1.63e+21      0.000    1.15e+10    1.15e+10
    ==============================================================================



```python
results.plot_diagnostics( figsize=(12, 10) )
plt.show()
```


![png]({{ site.url }}/data/dataScience/07-02img/output_20_0.png)




```python
pred = results.get_prediction(
    start=pd.to_datetime('2015-01-01'),
    dynamic=False
)

pred_ci = pred.conf_int()
```


```python
# 관측 데이터  2003년 부터 끝까지 
ax = y[ '2003' : ].plot( label='observed', figsize=(12, 8) )

# 예측
pred.predicted_mean.plot(
    ax=ax,
    label='One-step ahead Forecast',
    alpha=.7
)

ax.fill_between(
    pred_ci.index,
    pred_ci.iloc[ : , 0 ],
    pred_ci.iloc[ : , 1 ],
    color='k',
    alpha=.2
)

ax.fill_betweenx(
    ax.get_ylim(),
    pd.to_datetime('2015-01-01'),
    y.index[-1],
    alpha=.3,
)

ax.set_xlabel('Date')
plt.legend()

plt.show()
```


![png]({{ site.url }}/data/dataScience/07-02img/output_22_0.png)



# 2. 주가예측 - Prophet

> https://facebook.github.io/prophet/

> https://github.com/facebook/prophet


## 1) install

* Ubuntu16.04LTS,  Python 3.6.2 환경
* 패키지로 설치하는 것이 편하고 안전하다. 

```sh
➜  pip install fbprophet

Collecting fbprophet
  Downloading fbprophet-0.2.1.tar.gz
Collecting pystan>=2.14 (from fbprophet)
  Downloading pystan-2.17.1.0-cp36-cp36m-manylinux1_x86_64.whl (68.1MB)
  100% |████████████████████████████████| 68.1MB 
  Downloading Cython-0.27.3-cp36-cp36m-manylinux1_x86_64.whl (3.1MB)
  100% |████████████████████████████████| 3.1MB 236kB/s 
Installing collected packages: Cython, pystan, fbprophet
  Running setup.py install for fbprophet ... \
```

## 2) 기아자동차 주식


```python
import warnings
warnings.filterwarnings("ignore")

import itertools
import pandas as pd
import numpy as np

import statsmodels.api as sm
import matplotlib.pyplot as plt
plt.style.use('fivethirtyeight')
%matplotlib inline
import pandas_datareader.data as web
from datetime import datetime

# 한글폰트 
import matplotlib.font_manager as fm
# 폰트 적용
font_location = '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf'
font_name = fm.FontProperties(fname=font_location).get_name()

from matplotlib import rc
rc('font', family=font_name)


from fbprophet import Prophet
```


```python
start = datetime( 1990, 1, 1 )
end = datetime( 2017, 6, 30 )

# 기아자동차 주식
KIA = web.DataReader('000270.KS', 'yahoo', start, end )
KIA.head()
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
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
    <tr>
      <th>Date</th>
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
      <th>2000-01-04</th>
      <td>7404.520020</td>
      <td>7665.240234</td>
      <td>7300.229980</td>
      <td>7665.240234</td>
      <td>6111.007324</td>
      <td>636300.0</td>
    </tr>
    <tr>
      <th>2000-01-05</th>
      <td>7404.520020</td>
      <td>7404.520020</td>
      <td>7248.089844</td>
      <td>7248.089844</td>
      <td>5778.440918</td>
      <td>686100.0</td>
    </tr>
    <tr>
      <th>2000-01-06</th>
      <td>7331.520020</td>
      <td>7519.240234</td>
      <td>6935.220215</td>
      <td>6935.220215</td>
      <td>5529.009277</td>
      <td>379000.0</td>
    </tr>
    <tr>
      <th>2000-01-07</th>
      <td>6987.359863</td>
      <td>7143.799805</td>
      <td>6778.790039</td>
      <td>6778.790039</td>
      <td>5404.296875</td>
      <td>701400.0</td>
    </tr>
    <tr>
      <th>2000-01-10</th>
      <td>6841.359863</td>
      <td>7102.080078</td>
      <td>6810.069824</td>
      <td>7091.649902</td>
      <td>5653.720703</td>
      <td>1076700.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
KIA['Close'].plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7f1f1d6a3160>




![png]({{ site.url }}/data/dataScience/07-02img/output_26_1.png)



```python
# 2016-12-31까지 

KIA_trunc = KIA[ : '2016-12-31' ]
KIA_trunc.head(3)
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
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
    <tr>
      <th>Date</th>
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
      <th>2000-01-04</th>
      <td>7404.52002</td>
      <td>7665.240234</td>
      <td>7300.229980</td>
      <td>7665.240234</td>
      <td>6111.007324</td>
      <td>636300.0</td>
    </tr>
    <tr>
      <th>2000-01-05</th>
      <td>7404.52002</td>
      <td>7404.520020</td>
      <td>7248.089844</td>
      <td>7248.089844</td>
      <td>5778.440918</td>
      <td>686100.0</td>
    </tr>
    <tr>
      <th>2000-01-06</th>
      <td>7331.52002</td>
      <td>7519.240234</td>
      <td>6935.220215</td>
      <td>6935.220215</td>
      <td>5529.009277</td>
      <td>379000.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df = pd.DataFrame(
    {
        'ds': KIA_trunc.index,
        'y'  : KIA_trunc['Close']
    }    
)

df.reset_index( inplace=True )
```


```python
del df['Date']
```


```python
df.head(3)
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
      <th>ds</th>
      <th>y</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2000-01-04</td>
      <td>7665.240234</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2000-01-05</td>
      <td>7248.089844</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2000-01-06</td>
      <td>6935.220215</td>
    </tr>
  </tbody>
</table>
</div>




```python
m = Prophet()
m.fit(df)
```




    <fbprophet.forecaster.Prophet at 0x7f1f1cb3a7f0>




```python
future = m.make_future_dataframe( periods=365 )
future.tail(3)
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
      <th>ds</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4663</th>
      <td>2017-12-27</td>
    </tr>
    <tr>
      <th>4664</th>
      <td>2017-12-28</td>
    </tr>
    <tr>
      <th>4665</th>
      <td>2017-12-29</td>
    </tr>
  </tbody>
</table>
</div>




```python
forecast = m.predict( future )
forecast[ [ 'ds', 'yhat', 'yhat_lower', 'yhat_upper' ] ].tail()
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
      <th>ds</th>
      <th>yhat</th>
      <th>yhat_lower</th>
      <th>yhat_upper</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4661</th>
      <td>2017-12-25</td>
      <td>35736.021198</td>
      <td>26901.256042</td>
      <td>43506.681983</td>
    </tr>
    <tr>
      <th>4662</th>
      <td>2017-12-26</td>
      <td>35781.066339</td>
      <td>26897.317236</td>
      <td>42529.643307</td>
    </tr>
    <tr>
      <th>4663</th>
      <td>2017-12-27</td>
      <td>35749.136369</td>
      <td>26619.638417</td>
      <td>43537.664311</td>
    </tr>
    <tr>
      <th>4664</th>
      <td>2017-12-28</td>
      <td>35714.091179</td>
      <td>26785.438078</td>
      <td>43183.694451</td>
    </tr>
    <tr>
      <th>4665</th>
      <td>2017-12-29</td>
      <td>35650.791639</td>
      <td>26849.725225</td>
      <td>43201.114843</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.figure( figsize=( 12, 8) )
plt.plot( KIA['Close'] )
plt.show()
```


![png]({{ site.url }}/data/dataScience/07-02img/output_34_0.png)



```python
m.plot(forecast)
```



![png]({{ site.url }}/data/dataScience/07-02img/output_35_0.png)



![png]({{ site.url }}/data/dataScience/07-02img/output_35_1.png)



```python
m.plot_components( forecast )
```



![png]({{ site.url }}/data/dataScience/07-02img/output_36_0.png)



![png]({{ site.url }}/data/dataScience/07-02img/output_36_1.png)



## 3) 기아자동차 주식 - Growth Model


```python
df = pd.read_csv( './data_science/07. example_wp_R.csv' )
df['y' ] = np.log( df['y'] )
```


```python
df['cap'] = 8.5
```


```python
m = Prophet( growth='logistic' )
m.fit(df)
```




    <fbprophet.forecaster.Prophet at 0x7f1f271417f0>




```python
future = m.make_future_dataframe( periods=1826 )
future['cap'] = 8.5
fcst = m.predict( future )
m.plot( fcst )
```



![png]({{ site.url }}/data/dataScience/07-02img/output_41_0.png)



