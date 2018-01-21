---
layout: post
title:  "데이터 사이언스 01 주장의 근거- 서울시 CCTV"
description: 
categories: Data-Science
tags: [Data-Science, pandas, matplotlib]
redirect_from:
  - /2018/01/05/
---

* Kramdown table of contents
{:toc .toc}


# 참조
> [python matplotlib 에서 한글 텍스트 표시하기](https://ansuchan.com/matplotlib-with-korean/)

> [PinkWink의 DataScience](http://pinkwink.kr/category/Theory/DataScience?page=1)

---

#  1. 데이터 얻기 
## 1) 서울시 구별 CCTV 현황 
* [서울 열린데이터 광장](http://data.seoul.go.kr/)   http://data.seoul.go.kr/
* `서울시 자치구 년도별 CCTV 설치 현황.csv`  ➜ `01.CCTV_in_Seoul.csv`

## 2) 서울시 구별 인구현황 데이터
* [서울 통계](http://stat.seoul.go.kr/)   http://stat.seoul.go.kr/ 
* 인구 > 주민등록인구 > 주민등록인구(구별)
* `OctagonExcel.xls`  ➜ `01.population_in_Seoul.xls` 

---


# 2. 서울시 구별 CCTV 현황 및 구별 인구 데이터 읽기 
## 1) pandas (powerful Python data analysis toolkit) 설치

```bash
$ pip install pandas

Collecting pandas
  Downloading pandas-0.22.0-cp36-cp36m-manylinux1_x86_64.whl (26.2MB)
    100% |████████████████████████████████| 26.3MB 34kB/s 
Collecting numpy>=1.9.0 (from pandas)
  Using cached numpy-1.13.3-cp36-cp36m-manylinux1_x86_64.whl
Requirement already satisfied: python-dateutil>=2 in /home/learn/.pyenv/versions/3.6.2/envs/Jupyter/lib/python3.6/site-packages (from pandas)
Collecting pytz>=2011k (from pandas)
  Using cached pytz-2017.3-py2.py3-none-any.whl
Requirement already satisfied: six>=1.5 in /home/learn/.pyenv/versions/3.6.2/envs/Jupyter/lib/python3.6/site-packages (from python-dateutil>=2->pandas)
Installing collected packages: numpy, pytz, pandas
Successfully installed numpy-1.13.3 pandas-0.22.0 pytz-2017.3
```

## 2) 서울시 CCTV 데이터 읽기


```python
import pandas as pd
```


```python
CCTV_Seoul = pd.read_csv('./data_science/01.CCTV_in_Seoul.csv', encoding='utf-8')
```


```python
CCTV_Seoul.head()
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
      <th>기관명</th>
      <th>소계</th>
      <th>2013년도 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>강남구</td>
      <td>3238</td>
      <td>1292</td>
      <td>430</td>
      <td>584</td>
      <td>932</td>
    </tr>
    <tr>
      <th>1</th>
      <td>강동구</td>
      <td>1010</td>
      <td>379</td>
      <td>99</td>
      <td>155</td>
      <td>377</td>
    </tr>
    <tr>
      <th>2</th>
      <td>강북구</td>
      <td>831</td>
      <td>369</td>
      <td>120</td>
      <td>138</td>
      <td>204</td>
    </tr>
    <tr>
      <th>3</th>
      <td>강서구</td>
      <td>911</td>
      <td>388</td>
      <td>258</td>
      <td>184</td>
      <td>81</td>
    </tr>
    <tr>
      <th>4</th>
      <td>관악구</td>
      <td>2109</td>
      <td>846</td>
      <td>260</td>
      <td>390</td>
      <td>613</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 기관명  이름 (columns) 바꾸기
CCTV_Seoul.columns[0]
```




    '기관명'




```python
CCTV_Seoul.rename( columns={ CCTV_Seoul.columns[0] : '구별'}, inplace=True )
CCTV_Seoul.head()
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
      <th>구별</th>
      <th>소계</th>
      <th>2013년도 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>강남구</td>
      <td>3238</td>
      <td>1292</td>
      <td>430</td>
      <td>584</td>
      <td>932</td>
    </tr>
    <tr>
      <th>1</th>
      <td>강동구</td>
      <td>1010</td>
      <td>379</td>
      <td>99</td>
      <td>155</td>
      <td>377</td>
    </tr>
    <tr>
      <th>2</th>
      <td>강북구</td>
      <td>831</td>
      <td>369</td>
      <td>120</td>
      <td>138</td>
      <td>204</td>
    </tr>
    <tr>
      <th>3</th>
      <td>강서구</td>
      <td>911</td>
      <td>388</td>
      <td>258</td>
      <td>184</td>
      <td>81</td>
    </tr>
    <tr>
      <th>4</th>
      <td>관악구</td>
      <td>2109</td>
      <td>846</td>
      <td>260</td>
      <td>390</td>
      <td>613</td>
    </tr>
  </tbody>
</table>
</div>



## 3) 서울시 인구현황 데이터 읽기
* 엑셀 읽는데 문제 발생 

```
pop_Seoul = pd.read_excel('./data_science/01.population_in_Seoul.xls', encoding='utf-8')
ModuleNotFoundError: No module named 'xlrd'
```
* xlrd 설치 

```
$ pip install xlrd

Collecting xlrd
  Downloading xlrd-1.1.0-py2.py3-none-any.whl (108kB)
    100% |████████████████████████████████| 112kB 670kB/s 
Installing collected packages: xlrd
Successfully installed xlrd-1.1.0
```


```python
pop_Seoul = pd.read_excel('./data_science/01.population_in_Seoul.xls', encoding='utf-8')
pop_Seoul.head()
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
      <th>기간</th>
      <th>자치구</th>
      <th>세대</th>
      <th>인구</th>
      <th>인구.1</th>
      <th>인구.2</th>
      <th>인구.3</th>
      <th>인구.4</th>
      <th>인구.5</th>
      <th>인구.6</th>
      <th>인구.7</th>
      <th>인구.8</th>
      <th>세대당인구</th>
      <th>65세이상고령자</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>기간</td>
      <td>자치구</td>
      <td>세대</td>
      <td>합계</td>
      <td>합계</td>
      <td>합계</td>
      <td>한국인</td>
      <td>한국인</td>
      <td>한국인</td>
      <td>등록외국인</td>
      <td>등록외국인</td>
      <td>등록외국인</td>
      <td>세대당인구</td>
      <td>65세이상고령자</td>
    </tr>
    <tr>
      <th>1</th>
      <td>기간</td>
      <td>자치구</td>
      <td>세대</td>
      <td>계</td>
      <td>남자</td>
      <td>여자</td>
      <td>계</td>
      <td>남자</td>
      <td>여자</td>
      <td>계</td>
      <td>남자</td>
      <td>여자</td>
      <td>세대당인구</td>
      <td>65세이상고령자</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2017.3/4</td>
      <td>합계</td>
      <td>4219001</td>
      <td>10158411</td>
      <td>4975437</td>
      <td>5182974</td>
      <td>9891448</td>
      <td>4849195</td>
      <td>5042253</td>
      <td>266963</td>
      <td>126242</td>
      <td>140721</td>
      <td>2.34</td>
      <td>1353486</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2017.3/4</td>
      <td>종로구</td>
      <td>73668</td>
      <td>164640</td>
      <td>80173</td>
      <td>84467</td>
      <td>155109</td>
      <td>76155</td>
      <td>78954</td>
      <td>9531</td>
      <td>4018</td>
      <td>5513</td>
      <td>2.11</td>
      <td>26034</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2017.3/4</td>
      <td>중구</td>
      <td>60130</td>
      <td>134174</td>
      <td>66064</td>
      <td>68110</td>
      <td>125332</td>
      <td>62011</td>
      <td>63321</td>
      <td>8842</td>
      <td>4053</td>
      <td>4789</td>
      <td>2.08</td>
      <td>21249</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 복잡한 header를 정리하기 
##  header는 2번째 줄부터 
##  사용할 coloumn은  B, D, G, J, N 
##  FutureWarning: the 'parse_cols' keyword is deprecated, use 'usecols' instead
pop_Seoul = pd.read_excel(
                         './data_science/01.population_in_Seoul.xls', 
                         header=2,
                         usecols='B, D, G, J, N',
                         encoding='utf-8'
                         )
pop_Seoul.head()
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
      <th>자치구</th>
      <th>계</th>
      <th>계.1</th>
      <th>계.2</th>
      <th>65세이상고령자</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>합계</td>
      <td>10158411.0</td>
      <td>9891448.0</td>
      <td>266963.0</td>
      <td>1353486.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>종로구</td>
      <td>164640.0</td>
      <td>155109.0</td>
      <td>9531.0</td>
      <td>26034.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>중구</td>
      <td>134174.0</td>
      <td>125332.0</td>
      <td>8842.0</td>
      <td>21249.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>용산구</td>
      <td>243922.0</td>
      <td>228960.0</td>
      <td>14962.0</td>
      <td>36727.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>성동구</td>
      <td>312933.0</td>
      <td>304879.0</td>
      <td>8054.0</td>
      <td>40902.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Column 이름을 바꾸기 
pop_Seoul.rename(
    columns={
        pop_Seoul.columns[0] : '구별',
        pop_Seoul.columns[1] : '인구수',
        pop_Seoul.columns[2] : '한국인',
        pop_Seoul.columns[3] : '외국인',
        pop_Seoul.columns[4] : '고령자'
    },
    inplace=True
)

pop_Seoul.head()
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
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>합계</td>
      <td>10158411.0</td>
      <td>9891448.0</td>
      <td>266963.0</td>
      <td>1353486.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>종로구</td>
      <td>164640.0</td>
      <td>155109.0</td>
      <td>9531.0</td>
      <td>26034.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>중구</td>
      <td>134174.0</td>
      <td>125332.0</td>
      <td>8842.0</td>
      <td>21249.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>용산구</td>
      <td>243922.0</td>
      <td>228960.0</td>
      <td>14962.0</td>
      <td>36727.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>성동구</td>
      <td>312933.0</td>
      <td>304879.0</td>
      <td>8054.0</td>
      <td>40902.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
import numpy as np
```

# ❈ pandas tutorials

## 1) pandas.Series( [배열] ) 
* input: 배열
* output:  columns화 

```python
pandas.Series([1, 3, 5, numpy.nan, 6.8]) 

0    1.0
1    3.0
2    5.0
3    NaN
4    6.8
dtype:  float64
```

## 2) pandas.date_range( '일시', period=기간)
* Pandas는 시계열 데이터 처리에 강점을 가진다.

```python
pandas.date_range('20130101', periods=6)

DatetimeIndex(['2013-01-01', '2013-01-02', '2013-01-03', '2013-01-04',
               '2013-01-05', '2013-01-06'],
              dtype='datetime64[ns]', freq='D')
```

## 3) pandas.DataFrame( index=배열1, columns=배열2)
* Pandas에서 가장 많이 사용하는 형태 
* index가  행렬의 행(row)에 해당한다. 
* pandas.DataFrame.**index**
* pandas.DataFrame.**columns**
* pandas.DataFrame.**values**
* pandas.DataFrame.**info() **
* pandas.DataFrame.**describe()**
* pandas.DataFrame.**sort_values(by='컬럼명', ascending='True/False')**
* pandas.**DataFrame['칼럼이름']**    `pandas.DataFrame['A']`
* pandas.**DataFrame[인덱스 범위 n : m]**    `pandas.DataFrame[0 : 3]`
* pandas.DataFrame.**loc['인덱스 번호']**   `pandas.DataFrame.loc['2013-01-01']`
* pandas.DataFrame.**loc[ i : j ,  [ m : n ] ]**   `pandas.DataFrame.loc[ '20130101' : '20130104', ['A', 'B']  ]`
* pandas.DataFrame.**iloc[ i : j ,  [ m : n ] ]**
* 조건부 슬라이싱  `df[ df.A > 0 ]`,  `df[ df > 0 ]`  여기서  `df = pandas.DataFrame(행렬, index, columns)`
* DataFrame 복사  `df2 = df.copy()`
* pandas.DataFrame.**apply(함수)**   `df.apply(lambda x: x.max() - x.min() )`
* 조건판단     `df[df['E'].isin(['two', 'four'])`


```python
# CCTV 숫자가 적은 순으로 구별 정리
CCTV_Seoul.sort_values( by='소계', ascending=True).head()
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
      <th>구별</th>
      <th>소계</th>
      <th>2013년도 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9</th>
      <td>도봉구</td>
      <td>825</td>
      <td>238</td>
      <td>159</td>
      <td>42</td>
      <td>386</td>
    </tr>
    <tr>
      <th>2</th>
      <td>강북구</td>
      <td>831</td>
      <td>369</td>
      <td>120</td>
      <td>138</td>
      <td>204</td>
    </tr>
    <tr>
      <th>5</th>
      <td>광진구</td>
      <td>878</td>
      <td>573</td>
      <td>78</td>
      <td>53</td>
      <td>174</td>
    </tr>
    <tr>
      <th>3</th>
      <td>강서구</td>
      <td>911</td>
      <td>388</td>
      <td>258</td>
      <td>184</td>
      <td>81</td>
    </tr>
    <tr>
      <th>24</th>
      <td>중랑구</td>
      <td>916</td>
      <td>509</td>
      <td>121</td>
      <td>177</td>
      <td>109</td>
    </tr>
  </tbody>
</table>
</div>




```python
# CCTV 숫자가 많은 순으로 구별 정리
CCTV_Seoul.sort_values( by='소계', ascending=False).head()
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
      <th>구별</th>
      <th>소계</th>
      <th>2013년도 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>강남구</td>
      <td>3238</td>
      <td>1292</td>
      <td>430</td>
      <td>584</td>
      <td>932</td>
    </tr>
    <tr>
      <th>18</th>
      <td>양천구</td>
      <td>2482</td>
      <td>1843</td>
      <td>142</td>
      <td>30</td>
      <td>467</td>
    </tr>
    <tr>
      <th>14</th>
      <td>서초구</td>
      <td>2297</td>
      <td>1406</td>
      <td>157</td>
      <td>336</td>
      <td>398</td>
    </tr>
    <tr>
      <th>4</th>
      <td>관악구</td>
      <td>2109</td>
      <td>846</td>
      <td>260</td>
      <td>390</td>
      <td>613</td>
    </tr>
    <tr>
      <th>21</th>
      <td>은평구</td>
      <td>2108</td>
      <td>1138</td>
      <td>224</td>
      <td>278</td>
      <td>468</td>
    </tr>
  </tbody>
</table>
</div>




```python
CCTV_Seoul['최근증가율'] = \
( CCTV_Seoul['2016년'] + CCTV_Seoul['2015년'] + CCTV_Seoul['2014년'] ) / CCTV_Seoul['2013년도 이전'] * 100

CCTV_Seoul.sort_values(by='최근증가율', ascending=False).head()
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
      <th>구별</th>
      <th>소계</th>
      <th>2013년도 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
      <th>최근증가율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>22</th>
      <td>종로구</td>
      <td>1619</td>
      <td>464</td>
      <td>314</td>
      <td>211</td>
      <td>630</td>
      <td>248.922414</td>
    </tr>
    <tr>
      <th>9</th>
      <td>도봉구</td>
      <td>825</td>
      <td>238</td>
      <td>159</td>
      <td>42</td>
      <td>386</td>
      <td>246.638655</td>
    </tr>
    <tr>
      <th>12</th>
      <td>마포구</td>
      <td>980</td>
      <td>314</td>
      <td>118</td>
      <td>169</td>
      <td>379</td>
      <td>212.101911</td>
    </tr>
    <tr>
      <th>8</th>
      <td>노원구</td>
      <td>1566</td>
      <td>542</td>
      <td>57</td>
      <td>451</td>
      <td>516</td>
      <td>188.929889</td>
    </tr>
    <tr>
      <th>1</th>
      <td>강동구</td>
      <td>1010</td>
      <td>379</td>
      <td>99</td>
      <td>155</td>
      <td>377</td>
      <td>166.490765</td>
    </tr>
  </tbody>
</table>
</div>



## 4) 서울시 인구 데이터 정리하기 


```python
pop_Seoul.head()
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
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>합계</td>
      <td>10158411.0</td>
      <td>9891448.0</td>
      <td>266963.0</td>
      <td>1353486.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>종로구</td>
      <td>164640.0</td>
      <td>155109.0</td>
      <td>9531.0</td>
      <td>26034.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>중구</td>
      <td>134174.0</td>
      <td>125332.0</td>
      <td>8842.0</td>
      <td>21249.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>용산구</td>
      <td>243922.0</td>
      <td>228960.0</td>
      <td>14962.0</td>
      <td>36727.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>성동구</td>
      <td>312933.0</td>
      <td>304879.0</td>
      <td>8054.0</td>
      <td>40902.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# '합계' 줄 제거 
pop_Seoul.drop([0], inplace=True )
pop_Seoul.head()
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
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>종로구</td>
      <td>164640.0</td>
      <td>155109.0</td>
      <td>9531.0</td>
      <td>26034.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>중구</td>
      <td>134174.0</td>
      <td>125332.0</td>
      <td>8842.0</td>
      <td>21249.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>용산구</td>
      <td>243922.0</td>
      <td>228960.0</td>
      <td>14962.0</td>
      <td>36727.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>성동구</td>
      <td>312933.0</td>
      <td>304879.0</td>
      <td>8054.0</td>
      <td>40902.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>광진구</td>
      <td>372414.0</td>
      <td>357743.0</td>
      <td>14671.0</td>
      <td>43579.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# '구별' 데이터 무결성 확인
pop_Seoul['구별'].unique()
```




    array(['종로구', '중구', '용산구', '성동구', '광진구', '동대문구', '중랑구', '성북구', '강북구',
           '도봉구', '노원구', '은평구', '서대문구', '마포구', '양천구', '강서구', '구로구', '금천구',
           '영등포구', '동작구', '관악구', '서초구', '강남구', '송파구', '강동구', nan], dtype=object)




```python
# nan 부분 확인
pop_Seoul[pop_Seoul['구별'].isnull()]
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
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>26</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
# nan이 들어간 행을 drop으로 삭제하자. 
pop_Seoul.drop([26], inplace=True)
pop_Seoul.tail()
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
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>21</th>
      <td>관악구</td>
      <td>522849.0</td>
      <td>505188.0</td>
      <td>17661.0</td>
      <td>69486.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>서초구</td>
      <td>447177.0</td>
      <td>442833.0</td>
      <td>4344.0</td>
      <td>52738.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>강남구</td>
      <td>565731.0</td>
      <td>560827.0</td>
      <td>4904.0</td>
      <td>64579.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>송파구</td>
      <td>668366.0</td>
      <td>661750.0</td>
      <td>6616.0</td>
      <td>75301.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>강동구</td>
      <td>446760.0</td>
      <td>442654.0</td>
      <td>4106.0</td>
      <td>55902.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
pop_Seoul['외국인비율'] = pop_Seoul['외국인'] / pop_Seoul['인구수'] * 100
pop_Seoul['고령자비율'] = pop_Seoul['고령자'] / pop_Seoul['인구수'] * 100
pop_Seoul.head()
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
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
      <th>외국인비율</th>
      <th>고령자비율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>종로구</td>
      <td>164640.0</td>
      <td>155109.0</td>
      <td>9531.0</td>
      <td>26034.0</td>
      <td>5.788994</td>
      <td>15.812682</td>
    </tr>
    <tr>
      <th>2</th>
      <td>중구</td>
      <td>134174.0</td>
      <td>125332.0</td>
      <td>8842.0</td>
      <td>21249.0</td>
      <td>6.589950</td>
      <td>15.836898</td>
    </tr>
    <tr>
      <th>3</th>
      <td>용산구</td>
      <td>243922.0</td>
      <td>228960.0</td>
      <td>14962.0</td>
      <td>36727.0</td>
      <td>6.133928</td>
      <td>15.056862</td>
    </tr>
    <tr>
      <th>4</th>
      <td>성동구</td>
      <td>312933.0</td>
      <td>304879.0</td>
      <td>8054.0</td>
      <td>40902.0</td>
      <td>2.573714</td>
      <td>13.070529</td>
    </tr>
    <tr>
      <th>5</th>
      <td>광진구</td>
      <td>372414.0</td>
      <td>357743.0</td>
      <td>14671.0</td>
      <td>43579.0</td>
      <td>3.939433</td>
      <td>11.701762</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 인구수가 많은 순으로  정렬
pop_Seoul.sort_values(by='인구수', ascending=False).head()
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
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
      <th>외국인비율</th>
      <th>고령자비율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>24</th>
      <td>송파구</td>
      <td>668366.0</td>
      <td>661750.0</td>
      <td>6616.0</td>
      <td>75301.0</td>
      <td>0.989877</td>
      <td>11.266432</td>
    </tr>
    <tr>
      <th>16</th>
      <td>강서구</td>
      <td>607877.0</td>
      <td>601391.0</td>
      <td>6486.0</td>
      <td>75046.0</td>
      <td>1.066992</td>
      <td>12.345590</td>
    </tr>
    <tr>
      <th>23</th>
      <td>강남구</td>
      <td>565731.0</td>
      <td>560827.0</td>
      <td>4904.0</td>
      <td>64579.0</td>
      <td>0.866843</td>
      <td>11.415143</td>
    </tr>
    <tr>
      <th>11</th>
      <td>노원구</td>
      <td>562278.0</td>
      <td>558432.0</td>
      <td>3846.0</td>
      <td>73588.0</td>
      <td>0.684003</td>
      <td>13.087476</td>
    </tr>
    <tr>
      <th>21</th>
      <td>관악구</td>
      <td>522849.0</td>
      <td>505188.0</td>
      <td>17661.0</td>
      <td>69486.0</td>
      <td>3.377839</td>
      <td>13.289879</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 외국인 숫자 순으로 정렬
pop_Seoul.sort_values(by='외국인', ascending=False).head()
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
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
      <th>외국인비율</th>
      <th>고령자비율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>19</th>
      <td>영등포구</td>
      <td>401908.0</td>
      <td>368818.0</td>
      <td>33090.0</td>
      <td>53620.0</td>
      <td>8.233228</td>
      <td>13.341362</td>
    </tr>
    <tr>
      <th>17</th>
      <td>구로구</td>
      <td>443288.0</td>
      <td>412972.0</td>
      <td>30316.0</td>
      <td>58260.0</td>
      <td>6.838895</td>
      <td>13.142697</td>
    </tr>
    <tr>
      <th>18</th>
      <td>금천구</td>
      <td>253646.0</td>
      <td>235608.0</td>
      <td>18038.0</td>
      <td>33818.0</td>
      <td>7.111486</td>
      <td>13.332755</td>
    </tr>
    <tr>
      <th>21</th>
      <td>관악구</td>
      <td>522849.0</td>
      <td>505188.0</td>
      <td>17661.0</td>
      <td>69486.0</td>
      <td>3.377839</td>
      <td>13.289879</td>
    </tr>
    <tr>
      <th>6</th>
      <td>동대문구</td>
      <td>367769.0</td>
      <td>352011.0</td>
      <td>15758.0</td>
      <td>55287.0</td>
      <td>4.284755</td>
      <td>15.033078</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 외국인 비율 순으로 정렬
pop_Seoul.sort_values(by='외국인비율', ascending=False).head()
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
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
      <th>외국인비율</th>
      <th>고령자비율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>19</th>
      <td>영등포구</td>
      <td>401908.0</td>
      <td>368818.0</td>
      <td>33090.0</td>
      <td>53620.0</td>
      <td>8.233228</td>
      <td>13.341362</td>
    </tr>
    <tr>
      <th>18</th>
      <td>금천구</td>
      <td>253646.0</td>
      <td>235608.0</td>
      <td>18038.0</td>
      <td>33818.0</td>
      <td>7.111486</td>
      <td>13.332755</td>
    </tr>
    <tr>
      <th>17</th>
      <td>구로구</td>
      <td>443288.0</td>
      <td>412972.0</td>
      <td>30316.0</td>
      <td>58260.0</td>
      <td>6.838895</td>
      <td>13.142697</td>
    </tr>
    <tr>
      <th>2</th>
      <td>중구</td>
      <td>134174.0</td>
      <td>125332.0</td>
      <td>8842.0</td>
      <td>21249.0</td>
      <td>6.589950</td>
      <td>15.836898</td>
    </tr>
    <tr>
      <th>3</th>
      <td>용산구</td>
      <td>243922.0</td>
      <td>228960.0</td>
      <td>14962.0</td>
      <td>36727.0</td>
      <td>6.133928</td>
      <td>15.056862</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 고령자 숫자 순으로 정렬
pop_Seoul.sort_values(by='고령자', ascending=False).head()
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
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
      <th>외국인비율</th>
      <th>고령자비율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>24</th>
      <td>송파구</td>
      <td>668366.0</td>
      <td>661750.0</td>
      <td>6616.0</td>
      <td>75301.0</td>
      <td>0.989877</td>
      <td>11.266432</td>
    </tr>
    <tr>
      <th>16</th>
      <td>강서구</td>
      <td>607877.0</td>
      <td>601391.0</td>
      <td>6486.0</td>
      <td>75046.0</td>
      <td>1.066992</td>
      <td>12.345590</td>
    </tr>
    <tr>
      <th>12</th>
      <td>은평구</td>
      <td>491899.0</td>
      <td>487507.0</td>
      <td>4392.0</td>
      <td>73850.0</td>
      <td>0.892866</td>
      <td>15.013245</td>
    </tr>
    <tr>
      <th>11</th>
      <td>노원구</td>
      <td>562278.0</td>
      <td>558432.0</td>
      <td>3846.0</td>
      <td>73588.0</td>
      <td>0.684003</td>
      <td>13.087476</td>
    </tr>
    <tr>
      <th>21</th>
      <td>관악구</td>
      <td>522849.0</td>
      <td>505188.0</td>
      <td>17661.0</td>
      <td>69486.0</td>
      <td>3.377839</td>
      <td>13.289879</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 고령자 비율 순으로 정렬
pop_Seoul.sort_values(by='고령자비율', ascending=False).head()
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
      <th>구별</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
      <th>외국인비율</th>
      <th>고령자비율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>9</th>
      <td>강북구</td>
      <td>329042.0</td>
      <td>325552.0</td>
      <td>3490.0</td>
      <td>56078.0</td>
      <td>1.060655</td>
      <td>17.042809</td>
    </tr>
    <tr>
      <th>2</th>
      <td>중구</td>
      <td>134174.0</td>
      <td>125332.0</td>
      <td>8842.0</td>
      <td>21249.0</td>
      <td>6.589950</td>
      <td>15.836898</td>
    </tr>
    <tr>
      <th>1</th>
      <td>종로구</td>
      <td>164640.0</td>
      <td>155109.0</td>
      <td>9531.0</td>
      <td>26034.0</td>
      <td>5.788994</td>
      <td>15.812682</td>
    </tr>
    <tr>
      <th>10</th>
      <td>도봉구</td>
      <td>347338.0</td>
      <td>345293.0</td>
      <td>2045.0</td>
      <td>52909.0</td>
      <td>0.588764</td>
      <td>15.232713</td>
    </tr>
    <tr>
      <th>3</th>
      <td>용산구</td>
      <td>243922.0</td>
      <td>228960.0</td>
      <td>14962.0</td>
      <td>36727.0</td>
      <td>6.133928</td>
      <td>15.056862</td>
    </tr>
  </tbody>
</table>
</div>



---

# 3. 인구별 CCTV 비율을 확인하기 
## 1) CCTV 데이터와 인구현황 데이터를 병합하기 
* 2017년 CCTV 개수만 살펴보기로 함 
* 그래프를 대비함 


```python
data_result = pd.merge(CCTV_Seoul, pop_Seoul, on='구별')
data_result.head()
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
      <th>구별</th>
      <th>소계</th>
      <th>2013년도 이전</th>
      <th>2014년</th>
      <th>2015년</th>
      <th>2016년</th>
      <th>최근증가율</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
      <th>외국인비율</th>
      <th>고령자비율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>강남구</td>
      <td>3238</td>
      <td>1292</td>
      <td>430</td>
      <td>584</td>
      <td>932</td>
      <td>150.619195</td>
      <td>565731.0</td>
      <td>560827.0</td>
      <td>4904.0</td>
      <td>64579.0</td>
      <td>0.866843</td>
      <td>11.415143</td>
    </tr>
    <tr>
      <th>1</th>
      <td>강동구</td>
      <td>1010</td>
      <td>379</td>
      <td>99</td>
      <td>155</td>
      <td>377</td>
      <td>166.490765</td>
      <td>446760.0</td>
      <td>442654.0</td>
      <td>4106.0</td>
      <td>55902.0</td>
      <td>0.919062</td>
      <td>12.512759</td>
    </tr>
    <tr>
      <th>2</th>
      <td>강북구</td>
      <td>831</td>
      <td>369</td>
      <td>120</td>
      <td>138</td>
      <td>204</td>
      <td>125.203252</td>
      <td>329042.0</td>
      <td>325552.0</td>
      <td>3490.0</td>
      <td>56078.0</td>
      <td>1.060655</td>
      <td>17.042809</td>
    </tr>
    <tr>
      <th>3</th>
      <td>강서구</td>
      <td>911</td>
      <td>388</td>
      <td>258</td>
      <td>184</td>
      <td>81</td>
      <td>134.793814</td>
      <td>607877.0</td>
      <td>601391.0</td>
      <td>6486.0</td>
      <td>75046.0</td>
      <td>1.066992</td>
      <td>12.345590</td>
    </tr>
    <tr>
      <th>4</th>
      <td>관악구</td>
      <td>2109</td>
      <td>846</td>
      <td>260</td>
      <td>390</td>
      <td>613</td>
      <td>149.290780</td>
      <td>522849.0</td>
      <td>505188.0</td>
      <td>17661.0</td>
      <td>69486.0</td>
      <td>3.377839</td>
      <td>13.289879</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 2017년 CCTV 개수만 살펴보기로 함 
del data_result['2013년도 이전'] 
del data_result['2014년']
del data_result['2015년']
del data_result['2016년']
data_result.head()
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
      <th>구별</th>
      <th>소계</th>
      <th>최근증가율</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
      <th>외국인비율</th>
      <th>고령자비율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>강남구</td>
      <td>3238</td>
      <td>150.619195</td>
      <td>565731.0</td>
      <td>560827.0</td>
      <td>4904.0</td>
      <td>64579.0</td>
      <td>0.866843</td>
      <td>11.415143</td>
    </tr>
    <tr>
      <th>1</th>
      <td>강동구</td>
      <td>1010</td>
      <td>166.490765</td>
      <td>446760.0</td>
      <td>442654.0</td>
      <td>4106.0</td>
      <td>55902.0</td>
      <td>0.919062</td>
      <td>12.512759</td>
    </tr>
    <tr>
      <th>2</th>
      <td>강북구</td>
      <td>831</td>
      <td>125.203252</td>
      <td>329042.0</td>
      <td>325552.0</td>
      <td>3490.0</td>
      <td>56078.0</td>
      <td>1.060655</td>
      <td>17.042809</td>
    </tr>
    <tr>
      <th>3</th>
      <td>강서구</td>
      <td>911</td>
      <td>134.793814</td>
      <td>607877.0</td>
      <td>601391.0</td>
      <td>6486.0</td>
      <td>75046.0</td>
      <td>1.066992</td>
      <td>12.345590</td>
    </tr>
    <tr>
      <th>4</th>
      <td>관악구</td>
      <td>2109</td>
      <td>149.290780</td>
      <td>522849.0</td>
      <td>505188.0</td>
      <td>17661.0</td>
      <td>69486.0</td>
      <td>3.377839</td>
      <td>13.289879</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 그래프 대비 
data_result.set_index('구별', inplace=True)
data_result.head()
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
      <th>소계</th>
      <th>최근증가율</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
      <th>외국인비율</th>
      <th>고령자비율</th>
    </tr>
    <tr>
      <th>구별</th>
      <th></th>
      <th></th>
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
      <th>강남구</th>
      <td>3238</td>
      <td>150.619195</td>
      <td>565731.0</td>
      <td>560827.0</td>
      <td>4904.0</td>
      <td>64579.0</td>
      <td>0.866843</td>
      <td>11.415143</td>
    </tr>
    <tr>
      <th>강동구</th>
      <td>1010</td>
      <td>166.490765</td>
      <td>446760.0</td>
      <td>442654.0</td>
      <td>4106.0</td>
      <td>55902.0</td>
      <td>0.919062</td>
      <td>12.512759</td>
    </tr>
    <tr>
      <th>강북구</th>
      <td>831</td>
      <td>125.203252</td>
      <td>329042.0</td>
      <td>325552.0</td>
      <td>3490.0</td>
      <td>56078.0</td>
      <td>1.060655</td>
      <td>17.042809</td>
    </tr>
    <tr>
      <th>강서구</th>
      <td>911</td>
      <td>134.793814</td>
      <td>607877.0</td>
      <td>601391.0</td>
      <td>6486.0</td>
      <td>75046.0</td>
      <td>1.066992</td>
      <td>12.345590</td>
    </tr>
    <tr>
      <th>관악구</th>
      <td>2109</td>
      <td>149.290780</td>
      <td>522849.0</td>
      <td>505188.0</td>
      <td>17661.0</td>
      <td>69486.0</td>
      <td>3.377839</td>
      <td>13.289879</td>
    </tr>
  </tbody>
</table>
</div>



---

# 4. 인구대비 각 구별 CCTV 현황에 대한 경향 파악 
## 1)  CCTV 개수 대비 상관관계  파악
* numpy.corrcoef 사용


```python
np.corrcoef(data_result['고령자비율'], data_result['소계'])
```




    array([[ 1.        , -0.26753359],
           [-0.26753359,  1.        ]])




```python
np.corrcoef(data_result['외국인비율'], data_result['소계'])
```




    array([[ 1.        , -0.04728859],
           [-0.04728859,  1.        ]])




```python
np.corrcoef(data_result['인구수'], data_result['소계'])
```




    array([[ 1.        ,  0.23603622],
           [ 0.23603622,  1.        ]])



## 2) 결론:  CCTV와 인구수가  가장 큰 상관관계가 있다. 

---

# 5. 시각화 
## 1) matplotlib
> [https://matplotlib.org/](https://matplotlib.org/) 

> [http://stackoverflow.com/a/20497374](http://stackoverflow.com/a/20497374)

> [http://stackoverflow.com/a/9843560](http://stackoverflow.com/a/9843560)

> [취미로 하는 프로그래밍 !!!](http://freeprog.tistory.com/63) 
 
* 설치 

```bash
➜  sudo apt-get update
➜  sudo apt-get install python-matplotlib
➜  pip install matplotlib

Collecting matplotlib
  Downloading matplotlib-2.1.1-cp36-cp36m-manylinux1_x86_64.whl (15.0MB)
    100% |████████████████████████████████| 15.0MB 62kB/s 
Collecting pyparsing!=2.0.4,!=2.1.2,!=2.1.6,>=2.0.1 (from matplotlib)
  Using cached pyparsing-2.2.0-py2.py3-none-any.whl
Collecting cycler>=0.10 (from matplotlib)
  Using cached cycler-0.10.0-py2.py3-none-any.whl
Installing collected packages: pyparsing, cycler, matplotlib
Successfully installed cycler-0.10.0 matplotlib-2.1.1 pyparsing-2.2.0
```

* 삭제

```bash
# 1. Uninstall python-matplotlib
## To remove just python-matplotlib package itself from Ubuntu 16.04 (Xenial Xerus) execute on terminal:
➜  sudo apt-get remove python-matplotlib

# 2. Uninstall python-matplotlib and it's dependent packages
## To remove the python-matplotlib package and any other dependant package which are no longer needed from Ubuntu Xenial.
➜  sudo apt-get remove --auto-remove python-matplotlib

# 3. Purging python-matplotlib
## If you also want to delete configuration and/or data files of python-matplotlib from Ubuntu Xenial then this will work:
➜  sudo apt-get purge python-matplotlib

# 4. To delete configuration and/or data files of python-matplotlib and it's dependencies from Ubuntu Xenial then execute:
➜  sudo apt-get purge --auto-remove python-matplotlib
```

## 2) matplotlib 한글문제 
* [matplotlib.org](http://matplotlib.org/users/customizing.html)
* [python matplotlib 에서 한글 텍스트 표시하기](https://ansuchan.com/matplotlib-with-korean/)


```python
# 그래프의 결과를 Jypyter Notebook의 Out-Section에 나타나게 하기 
import matplotlib.pyplot as plt
%matplotlib inline
```


```python
# 한글 문제 
import matplotlib.font_manager as fm
# 현재 사용할 수 있는 폰트 목록
fm.get_fontconfig_fonts()
```

    [
     '/usr/share/fonts/truetype/nanum/NanumGothicBold.ttf',
     '/usr/share/fonts/truetype/msttcorefonts/Verdana_Bold_Italic.ttf',
     '/usr/share/fonts/opentype/stix/STIXSizeFourSym-Regular.otf',
     '/usr/share/fonts/truetype/kacst/KacstTitleL.ttf',
     '/usr/share/fonts/truetype/freefont/FreeSerif.ttf',
     '/usr/share/fonts/opentype/stix-word/STIXMath-Regular.otf',
     '/usr/share/fonts/truetype/tlwg/Norasi-BoldItalic.ttf',
     '/usr/share/fonts/truetype/msttcorefonts/Comic_Sans_MS_Bold.ttf',
     '/usr/share/fonts/truetype/dejavu/DejaVuSansCondensed-BoldOblique.ttf',
     '/usr/share/fonts/truetype/tlwg/Loma-Bold.ttf',
     '/usr/share/fonts/truetype/lyx/stmary10.ttf',
     '/usr/share/fonts/truetype/liberation/LiberationSansNarrow-Regular.ttf',
     '/usr/share/fonts/truetype/unfonts-core/UnPilgiBold.ttf',
    ]


```python
# 폰트 적용
font_location = '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf'
font_name = fm.FontProperties(fname=font_location).get_name()

from matplotlib import rc
rc('font', family=font_name)
```

## 3) 인구대비 CCTV비율 시각화 
* 구별 CCTV 개수 - 수평 막대 그래프(barh)
* 인구대비 CCTV비율 시각화 - 수평 막대 그래프 (barh)
* 인구수-CCTV수  그래프
* linear regression
* 경향에서 벗어난 데이터 찾기


```python
# 구별 CCTV 개수 - 수평 막대 그래프 
data_result['소계'].sort_values().plot(kind='barh', grid=True, figsize=(10, 10))
plt.show()
```

![png]({{ site.url }}/data/dataScience/cctv-in-seoul/output_40_0.png)


```python
# 인구대비 CCTV비율 시각화 - 수평 막대 그래프 
data_result['CCTV비율'] = data_result['소계'] / data_result['인구수'] * 100

data_result['CCTV비율'].sort_values().plot(kind='barh', grid=True, figsize=(10, 10))
plt.show()
```

![png]({{ site.url }}/data/dataScience/cctv-in-seoul/output_41_0.png)


```python
plt.figure(figsize=(6,6))
plt.scatter(data_result['인구수'], data_result['소계'], s=50)
plt.xlabel('인구수')
plt.ylabel('CCTV')
plt.grid()
plt.show()
```

![png]({{ site.url }}/data/dataScience/cctv-in-seoul/output_42_0.png)


```python
# linear regression
## 기울기, y절편 구하기 
fp1 = np.polyfit( data_result['인구수'], data_result['소계'], 1)
# 직선의 식
f1 = np.poly1d(fp1)
# x축의 범위와 간격 설정
fx = np.linspace(100000, 700000, 100)

# 그래프
plt.figure(figsize=(6,6))
plt.scatter(data_result['인구수'], data_result['소계'], s=50)
# 그래프에 추쇄선 삽입
## ls: line-style,  lw: line-width 
plt.plot(fx, f1(fx), ls='dashed', lw=3, color='g')
plt.xlabel('인구수')
plt.ylabel('CCTV')
plt.grid()
plt.show()
```

![png]({{ site.url }}/data/dataScience/cctv-in-seoul/output_43_0.png)


```python
# 경향성에서 벗어난 데이터 찾기
## 기울기, y절편 구하기 
fp1 = np.polyfit( data_result['인구수'], data_result['소계'], 1)
## 직선의 식
f1 = np.poly1d(fp1)
## x축의 범위와 간격 설정
fx = np.linspace(100000, 700000, 100)
## 직선과 점 사이의 y축 거리 구하기 
data_result['오차'] = np.abs( data_result['소계'] - f1(data_result['인구수']) ) 
## 오차 순으로 정렬
df_sort = data_result.sort_values(by='오차', ascending=False)
df_sort.head()
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
      <th>소계</th>
      <th>최근증가율</th>
      <th>인구수</th>
      <th>한국인</th>
      <th>외국인</th>
      <th>고령자</th>
      <th>외국인비율</th>
      <th>고령자비율</th>
      <th>CCTV비율</th>
      <th>오차</th>
    </tr>
    <tr>
      <th>구별</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
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
      <th>강남구</th>
      <td>3238</td>
      <td>150.619195</td>
      <td>565731.0</td>
      <td>560827.0</td>
      <td>4904.0</td>
      <td>64579.0</td>
      <td>0.866843</td>
      <td>11.415143</td>
      <td>0.572357</td>
      <td>1543.390613</td>
    </tr>
    <tr>
      <th>양천구</th>
      <td>2482</td>
      <td>34.671731</td>
      <td>476627.0</td>
      <td>472730.0</td>
      <td>3897.0</td>
      <td>54598.0</td>
      <td>0.817620</td>
      <td>11.455079</td>
      <td>0.520743</td>
      <td>887.616126</td>
    </tr>
    <tr>
      <th>강서구</th>
      <td>911</td>
      <td>134.793814</td>
      <td>607877.0</td>
      <td>601391.0</td>
      <td>6486.0</td>
      <td>75046.0</td>
      <td>1.066992</td>
      <td>12.345590</td>
      <td>0.149866</td>
      <td>831.015839</td>
    </tr>
    <tr>
      <th>용산구</th>
      <td>2096</td>
      <td>53.216374</td>
      <td>243922.0</td>
      <td>228960.0</td>
      <td>14962.0</td>
      <td>36727.0</td>
      <td>6.133928</td>
      <td>15.056862</td>
      <td>0.859291</td>
      <td>763.366194</td>
    </tr>
    <tr>
      <th>서초구</th>
      <td>2297</td>
      <td>63.371266</td>
      <td>447177.0</td>
      <td>442833.0</td>
      <td>4344.0</td>
      <td>52738.0</td>
      <td>0.971427</td>
      <td>11.793540</td>
      <td>0.513667</td>
      <td>735.741927</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 그래프
plt.figure(figsize=(14, 10))
plt.scatter(data_result['인구수'], data_result['소계'], s=50, c=data_result['오차'])
# 그래프에 추쇄선 삽입
## ls: line-style,  lw: line-width 
plt.plot(fx, f1(fx), ls='dashed', lw=3, color='g')

for n in range(10):
    plt.text( df_sort['인구수'][n]*1.01, df_sort['소계'][n]*0.99, df_sort.index[n], fontsize=10  )

plt.xlabel('인구수')
plt.ylabel('인구당비율')
plt.colorbar()
plt.grid()
plt.show()
```

![png]({{ site.url }}/data/dataScience/cctv-in-seoul/output_45_0.png)
