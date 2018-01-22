---
layout: post
title: "데이터 사이언스 05 지도데이터 처리2"
date: 2018-01-21 01:40 +0900
categories: [Data-Science]
tags: [Data-Science, Square tile grid map, SHP, GeoJSON]
---

* Kramdown table of contents
{:toc .toc}



# 1. 19대 대선 후보의 지역별 투표율
## 1) 데이터 얻기
* 중앙선거관리위원회 선거통계시스템 : [http://info.nec.go.kr/](http://info.nec.go.kr/)
* 크롬 개발자 도구 > Source > 위 사이트는  3개의 프레임 (= index + left + main)으로 구성되어 있다.  프레임에 맞춰 selenium 작업을 해야한다.

![png]({{ site.url }}/data/dataScience/05GeoData/5(2)-1-1web_source.png)



### (1) selenium.webdriver.common.action_chains.ActionChains
* 마우스 over(hovering 상태)에서  관련 태그를 클릭하는 연속 동작을 구현하기


```python
from selenium import  webdriver
import time
```


```python
# 크롬 창 열기
driver = webdriver.Chrome('../crawler/driver/chromedriver')
```


```python
# 중앙선거관리위원회 선거통계시스템 열기
driver.get('http://info.nec.go.kr/')
```


```python
# 크롬 개발자 도구 > Source
driver.switch_to_default_content()
driver.switch_to_frame('main')
```


```python
# 투/개표 텝 열기  -  JavaScript
## xpath   //*[@id="topmenu"]/ul/li[4]/a
driver.find_element_by_xpath("""//*[@id="topmenu"]/ul/li[4]/a""").click()
```


```python
# 투/개표 텝 >> 역대선거 탭
driver.find_element_by_xpath("""//*[@id="header"]/div[1]/ul[1]/li[2]/a""").click()
```


```python
# 투/개표 >> 역대선거  >>  투개표 : hover 상태
element01 = driver.find_element_by_xpath('''//*[@id="presubmu"]/li[3]/a''')
```


```python
# 투/개표 >> 역대선거  >>  투개표 : hover 상태 >  개표현황 : 클릭
element02 = driver.find_element_by_xpath('''//*[@id="preallmenu"]/div/div/ul/li[3]/dl/dd[4]/a''')
webdriver.common.action_chains.ActionChains(driver).move_to_element(element01).move_to_element(element02).click().perform()
```


```python
# 투/개표 >> 역대선거  >>  투개표 >  역대선거실시상황 >> 대통령선거
driver.find_element_by_xpath('''//*[@id="electionType1"]''').click()
```


```python
# ~ 대통령선거 >> 선택
driver.find_element_by_xpath('''//*[@id="electionName"]''').click()
```


```python
# ~ 대통령선거 >> 선택 >> 제19대
driver.find_element_by_xpath('''//*[@id="electionName"]/option[2]''').click()
```


```python
time.sleep(2)
# ~ 대통령선거 >> 선택 >> 제19대 >> 선거 선택 >> 대통령선거
driver.find_element_by_xpath('''//*[@id="electionCode"]/option[2]''').click()
```


```python
# 시도 선택
time.sleep(2)
sido_list_raw = driver.find_element_by_xpath("""//*[@id="cityCode"]""")
sido_list = sido_list_raw.find_elements_by_tag_name("option")
```


```python
time.sleep(2)
sido_names_values_raw = [ option.text for option in sido_list ]

sido_names_values_raw[:5]
```




    ['▽ 선 택', '▷ 전 체', '서울특별시', '부산광역시', '대구광역시']




```python
sido_names_values = sido_names_values_raw[2:]

sido_names_values
```




    ['서울특별시',
     '부산광역시',
     '대구광역시',
     '인천광역시',
     '광주광역시',
     '대전광역시',
     '울산광역시',
     '세종특별자치시',
     '경기도',
     '강원도',
     '충청북도',
     '충청남도',
     '전라북도',
     '전라남도',
     '경상북도',
     '경상남도',
     '제주특별자치도']




```python
# 서울특별시  >> 검색 버튼 클릭
driver.find_element_by_id("cityCode").send_keys(sido_names_values[0])
driver.find_element_by_xpath('''//*[@id="searchBtn"]''').click()
```


```python
# 서울특별시  >> 검색 버튼 클릭 >>  개표현황 표
from bs4 import BeautifulSoup

html = driver.page_source
soup = BeautifulSoup(html, 'lxml')
```


```python
tmp = soup.find('table').find('tbody').find_all('tr')
tmp[0]
```




    <tr>
    <td class="alignL" width="70">합계</td>
    <td class="alignR">8,382,999<br/>(867,043)</td>
    <td class="alignR">6,590,646<br/>(843,459)</td>
    <td class="alignR">2,781,345<br/>(42.34)</td><td class="alignR">1,365,285<br/>(20.78)</td><td class="alignR">1,492,767<br/>(22.72)</td><td class="alignR">476,973<br/>(7.26)</td><td class="alignR">425,459<br/>(6.47)</td><td class="alignR">9,987<br/>(0.15)</td><td class="alignR">789<br/>(0.01)</td><td class="alignR">3,554<br/>(0.05)</td><td class="alignR">1,938<br/>(0.02)</td><td class="alignR">3,416<br/>(0.05)</td><td class="alignR">1,277<br/>(0.01)</td><td class="alignR">2,177<br/>(0.03)</td><td class="alignR">3,950<br/>(0.06)</td><td class="alignR">6,568,917</td>
    <td class="alignR">21,729</td>
    <td class="alignR">1,792,353</td>
    </tr>




```python
tmp=tmp[1:]
tmp[0]
```




    <tr>
    <td class="alignL">종로구</td>
    <td class="alignR">133,769<br/>(15,511)</td>
    <td class="alignR">102,566<br/>(14,822)</td>
    <td class="alignR">42,512<br/>(41.59)</td><td class="alignR">22,325<br/>(21.84)</td><td class="alignR">22,313<br/>(21.83)</td><td class="alignR">7,412<br/>(7.25)</td><td class="alignR">7,113<br/>(6.95)</td><td class="alignR">228<br/>(0.22)</td><td class="alignR">5<br/>(0.00)</td><td class="alignR">78<br/>(0.07)</td><td class="alignR">31<br/>(0.03)</td><td class="alignR">63<br/>(0.06)</td><td class="alignR">26<br/>(0.02)</td><td class="alignR">47<br/>(0.04)</td><td class="alignR">49<br/>(0.04)</td><td class="alignR">102,202</td>
    <td class="alignR">364</td>
    <td class="alignR">31,203</td>
    </tr>




```python
# 구 이름
tmp_gu_si_gun_name =tmp[0].find_all("td")[0]
tmp_gu_si_gun_name.get_text()
```




    '종로구'




```python
# 투표수
tmp_gu_si_gun_num_of_votes = tmp[1].find_all("td")[2]
t1 = tmp_gu_si_gun_num_of_votes.text
tmp_votes = t1[: t1.find('(')].replace(',', "")
tmp_votes
```




    '82852'




```python
# 문재인 득표수/득표율
tmp_gu_si_gun_Moon = tmp[1].find_all("td")[3]
tm = tmp_gu_si_gun_Moon.text
tmp_Moon_votes = tm[ : tm.find('(')].replace(',', "")
tmp_Moon_rate = tm[ tm.find('(') +1 : -1 ]
tm, tmp_Moon_votes, tmp_Moon_rate
```




    ('34,062(41.23)', '34062', '41.23')




```python
# 홍준표 득표수/득표율
tmp_gu_si_gun_Hong = tmp[1].find_all("td")[4]
tH =  tmp_gu_si_gun_Hong.text
tmp_Hong_votes = tH[ : tH.find('(')].replace(',', "")
tmp_Hong_rate = tH[ tH.find('(') +1 : -1 ]
tH, tmp_Hong_votes, tmp_Hong_rate
```




    ('17,901(21.67)', '17901', '21.67')




```python
# 안철수 득표수/득표율
tmp_gu_si_gun_Ahn = tmp[1].find_all("td")[5]
tA = tmp_gu_si_gun_Ahn.text
tmp_Ahn_votes = tA[ : tA.find('(')].replace(',', "")
tmp_Ahn_rate = tA[ tA.find('(') +1 : -1 ]
tH, tmp_Ahn_votes, tmp_Ahn_rate
```




    ('17,901(21.67)', '19372', '23.45')




```python
from bs4 import BeautifulSoup
import time

name_si_do = []
name_gu_si_gun = []
num_votes = []
num_Moon = []
rate_Moon = []
num_Hong = []
rate_Hong = []
num_Ahn = []
rate_Ahn = []

for sido_name in sido_names_values:
    # 시도  >> 검색 버튼 클릭
    driver.find_element_by_id("cityCode").send_keys(sido_name)
    time.sleep(1)
    driver.find_element_by_xpath('''//*[@id="searchBtn"]''').click()
    # 시도  >> 검색 버튼 클릭 >>  개표현황 표
    html = driver.page_source
    time.sleep(1)
    soup = BeautifulSoup(html, 'lxml')
    table_tr = soup.find('table').find('tbody').find_all('tr')
    table_tr = table_tr[1:]

    for tmp in table_tr:
        # 구 이름
        tmp_gu_si_gun_name =tmp.find_all("td")[0]
        gu_si_gun_name = tmp_gu_si_gun_name.get_text()

        # 투표수
        tmp_gu_si_gun_num_of_votes = tmp.find_all("td")[2]
        t1 = tmp_gu_si_gun_num_of_votes.text
        tmp_votes = t1[: t1.find('(')].replace(',', "")

        # 문재인 득표수/득표율
        tmp_gu_si_gun_Moon = tmp.find_all("td")[3]
        tm = tmp_gu_si_gun_Moon.text
        tmp_Moon_votes = tm[ : tm.find('(')].replace(',', "")
        tmp_Moon_rate = tm[ tm.find('(') +1 : -1 ]

        # 홍준표 득표수/득표율
        tmp_gu_si_gun_Hong = tmp.find_all("td")[4]
        tH =  tmp_gu_si_gun_Hong.text
        tmp_Hong_votes = tH[ : tH.find('(')].replace(',', "")
        tmp_Hong_rate = tH[ tH.find('(') +1 : -1 ]

        # 안철수 득표수/득표율
        tmp_gu_si_gun_Ahn = tmp.find_all("td")[5]
        tA = tmp_gu_si_gun_Ahn.text
        tmp_Ahn_votes = tA[ : tA.find('(')].replace(',', "")
        tmp_Ahn_rate = tA[ tA.find('(') +1 : -1 ]

        time.sleep(1)
        name_si_do.append(sido_name)
        name_gu_si_gun.append(gu_si_gun_name)
        num_votes.append(tmp_votes)
        num_Moon.append(tmp_Moon_votes)
        rate_Moon.append(tmp_Moon_rate)
        num_Hong.append(tmp_Hong_votes)
        rate_Hong.append(tmp_Hong_rate)
        num_Ahn.append(tmp_Ahn_votes)
        rate_Ahn.append(tmp_Ahn_rate)
```


```python
import pandas as pd

election_result = pd.DataFrame(
    {
        '광역시도' : name_si_do,
        '시군' : name_gu_si_gun,
        '투표수' : num_votes,
        '문재인 득표' : num_Moon,
        '문재인 득표율' : rate_Moon,
        '홍준표 득표': num_Hong,
        '홍준표 득표율' : rate_Hong,
        '안철수 득표' : num_Ahn,
        '안철수 득표율' : rate_Ahn
    },
    columns=['광역시도', '시군', '투표수', '문재인 득표', '문재인 득표율', '홍준표 득표', '홍준표 득표율', '안철수 득표', '안철수 득표율' ]
)

election_result.tail()
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>245</th>
      <td>경상남도</td>
      <td>산청군</td>
      <td>24513</td>
      <td>6561</td>
      <td>27.00</td>
      <td>12544</td>
      <td>51.63</td>
      <td>2753</td>
      <td>11.33</td>
    </tr>
    <tr>
      <th>246</th>
      <td>경상남도</td>
      <td>거창군</td>
      <td>41325</td>
      <td>11256</td>
      <td>27.48</td>
      <td>19976</td>
      <td>48.78</td>
      <td>4923</td>
      <td>12.02</td>
    </tr>
    <tr>
      <th>247</th>
      <td>경상남도</td>
      <td>합천군</td>
      <td>33021</td>
      <td>7143</td>
      <td>21.83</td>
      <td>19699</td>
      <td>60.22</td>
      <td>3077</td>
      <td>9.40</td>
    </tr>
    <tr>
      <th>248</th>
      <td>제주특별자치도</td>
      <td>제주시</td>
      <td>273163</td>
      <td>125717</td>
      <td>46.25</td>
      <td>48027</td>
      <td>17.67</td>
      <td>55971</td>
      <td>20.59</td>
    </tr>
    <tr>
      <th>249</th>
      <td>제주특별자치도</td>
      <td>서귀포시</td>
      <td>101296</td>
      <td>43776</td>
      <td>43.50</td>
      <td>20036</td>
      <td>19.91</td>
      <td>21890</td>
      <td>21.75</td>
    </tr>
  </tbody>
</table>
</div>




```python
election_result.to_csv(
    './data_ouput/05.election_result.csv',
    encoding='utf-8',
    sep=','
)
```

---

# 2. 지역별 ID 생성 코드
## 1) draw_Korea의 ID와 일치시키기
* 정규표현식
* 세종시를 제외한 광역시 + 일반 도 + 세종시


```python
import re
```


```python
# 예제
example = '수원시장안구'
re.split('시|구', example)
```




    ['수원', '장안', '']




```python
election_result = pd.read_csv(
    './data_ouput/05.election_result.csv',
    encoding='utf-8',
    index_col=0
)

# 데이터 유형 파악
election_result.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 250 entries, 0 to 249
    Data columns (total 9 columns):
    광역시도       250 non-null object
    시군         250 non-null object
    투표수        250 non-null int64
    문재인 득표     250 non-null int64
    문재인 득표율    250 non-null float64
    홍준표 득표     250 non-null int64
    홍준표 득표율    250 non-null float64
    안철수 득표     250 non-null int64
    안철수 득표율    250 non-null float64
    dtypes: float64(3), int64(4), object(2)
    memory usage: 19.5+ KB



```python
import re


ID = []

for n in election_result.index:

    # '세종특별시'를 제외한 광역시
    if ( election_result['광역시도'][n][-1] == '시'  ) & ( election_result['광역시도'][n] != '세종특별자치시' ):
        #  광역시가 다르지만 이름이 같은 구 이름의 중복을 막기 위한 코드
        ## 구/시/군 이름의 길이가 2자인 경우
        if len(election_result['시군'][n]) == 2:
            ID.append( election_result['광역시도'][n][:2] + ' ' + election_result['시군'][n]  )
        ## 구/시/군 이름의 길이가 2자 이상인  경우
        else:
            ID.append( election_result['광역시도'][n][:2] + ' ' + election_result['시군'][n][:-1] )

   # '도'             
    elif ( election_result['광역시도'][n][-1] == '도' ):
        tmp = election_result['시군'][n]

        # 정규표현식 :  '시', '군' 없애기
        if tmp[0] not in ['시', '군']:
            tmp2 = re.split('시|군', tmp)
        else:
            tmp2 = [ tmp[:-1], '' ]

         # 구/시/군 이름 2자  >>  전남 고흥(군)
        if len( tmp2[1] ) == 2:
            tmp3 = tmp2[0] + ' ' + tmp2[1]
        # 구/시/군 이름 3자 이상  >> 경북 미리내(시)
        elif len(tmp2[1]) >= 3:
            tmp3 = tmp2[0] + ' ' + tmp2[1][:-1]
        else:
            tmp3 = tmp2[0]
        ID.append(tmp3)

    # 세종시
    else:
        ID.append('세종')
```


```python
len(ID), len(election_result)
```




    (250, 250)




```python
election_result['ID'] = ID

election_result.head()
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>서울특별시</td>
      <td>종로구</td>
      <td>102566</td>
      <td>42512</td>
      <td>41.59</td>
      <td>22325</td>
      <td>21.84</td>
      <td>22313</td>
      <td>21.83</td>
      <td>서울 종로</td>
    </tr>
    <tr>
      <th>1</th>
      <td>서울특별시</td>
      <td>중구</td>
      <td>82852</td>
      <td>34062</td>
      <td>41.23</td>
      <td>17901</td>
      <td>21.67</td>
      <td>19372</td>
      <td>23.45</td>
      <td>서울 중구</td>
    </tr>
    <tr>
      <th>2</th>
      <td>서울특별시</td>
      <td>용산구</td>
      <td>148157</td>
      <td>58081</td>
      <td>39.33</td>
      <td>35230</td>
      <td>23.85</td>
      <td>32109</td>
      <td>21.74</td>
      <td>서울 용산</td>
    </tr>
    <tr>
      <th>3</th>
      <td>서울특별시</td>
      <td>성동구</td>
      <td>203175</td>
      <td>86686</td>
      <td>42.80</td>
      <td>40566</td>
      <td>20.03</td>
      <td>45674</td>
      <td>22.55</td>
      <td>서울 성동</td>
    </tr>
    <tr>
      <th>4</th>
      <td>서울특별시</td>
      <td>광진구</td>
      <td>240030</td>
      <td>105512</td>
      <td>44.10</td>
      <td>46368</td>
      <td>19.38</td>
      <td>52824</td>
      <td>22.08</td>
      <td>서울 광진</td>
    </tr>
  </tbody>
</table>
</div>



## 2) 표 데이터 분석
* 문재인 후보에 대한 지지 비율이 높은 지역
* 홍준표 후보에 대한 지지 비율이 높은 지역


```python
# 문재인 후보에 대한 지지 비율이 높은 지역
election_result.sort_values( ['문재인 득표율'], ascending=False ).head()
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>182</th>
      <td>전라남도</td>
      <td>순천시</td>
      <td>181451</td>
      <td>122595</td>
      <td>67.81</td>
      <td>4525</td>
      <td>2.50</td>
      <td>40429</td>
      <td>22.36</td>
      <td>순천</td>
    </tr>
    <tr>
      <th>175</th>
      <td>전라북도</td>
      <td>장수군</td>
      <td>16079</td>
      <td>10714</td>
      <td>67.06</td>
      <td>717</td>
      <td>4.48</td>
      <td>3353</td>
      <td>20.98</td>
      <td>장수</td>
    </tr>
    <tr>
      <th>166</th>
      <td>전라북도</td>
      <td>전주시덕진구</td>
      <td>187921</td>
      <td>125375</td>
      <td>66.89</td>
      <td>5183</td>
      <td>2.76</td>
      <td>40188</td>
      <td>21.44</td>
      <td>전주 덕진</td>
    </tr>
    <tr>
      <th>165</th>
      <td>전라북도</td>
      <td>전주시완산구</td>
      <td>236383</td>
      <td>157637</td>
      <td>66.89</td>
      <td>7003</td>
      <td>2.97</td>
      <td>50506</td>
      <td>21.43</td>
      <td>전주 완산</td>
    </tr>
    <tr>
      <th>173</th>
      <td>전라북도</td>
      <td>진안군</td>
      <td>18107</td>
      <td>11918</td>
      <td>66.17</td>
      <td>819</td>
      <td>4.54</td>
      <td>3904</td>
      <td>21.67</td>
      <td>진안</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 홍준표 후보에 대한 지지 비율이 높은 지역
election_result.sort_values( ['홍준표 득표율'], ascending=False ).head()
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>219</th>
      <td>경상북도</td>
      <td>군위군</td>
      <td>17627</td>
      <td>2251</td>
      <td>12.83</td>
      <td>11651</td>
      <td>66.43</td>
      <td>1939</td>
      <td>11.05</td>
      <td>군위</td>
    </tr>
    <tr>
      <th>220</th>
      <td>경상북도</td>
      <td>의성군</td>
      <td>37855</td>
      <td>5365</td>
      <td>14.27</td>
      <td>23790</td>
      <td>63.30</td>
      <td>4767</td>
      <td>12.68</td>
      <td>의성</td>
    </tr>
    <tr>
      <th>223</th>
      <td>경상북도</td>
      <td>영덕군</td>
      <td>26125</td>
      <td>3786</td>
      <td>14.61</td>
      <td>16314</td>
      <td>62.96</td>
      <td>3231</td>
      <td>12.47</td>
      <td>영덕</td>
    </tr>
    <tr>
      <th>247</th>
      <td>경상남도</td>
      <td>합천군</td>
      <td>33021</td>
      <td>7143</td>
      <td>21.83</td>
      <td>19699</td>
      <td>60.22</td>
      <td>3077</td>
      <td>9.40</td>
      <td>합천</td>
    </tr>
    <tr>
      <th>216</th>
      <td>경상북도</td>
      <td>고령군</td>
      <td>22396</td>
      <td>3754</td>
      <td>16.90</td>
      <td>13248</td>
      <td>59.65</td>
      <td>2600</td>
      <td>11.70</td>
      <td>고령</td>
    </tr>
  </tbody>
</table>
</div>



---

# 3. Square tile grid Map 시각화
## 1) 기존 지도 데이터 가져오기


```python
draw_korea = pd.read_csv(
    './data_ouput/05.draw_korea.csv',
    encoding='utf-8',
    index_col=0
)

draw_korea.head()
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
      <th>y</th>
      <th>x</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>7</td>
      <td>철원</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0</td>
      <td>8</td>
      <td>화천</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>9</td>
      <td>양구</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0</td>
      <td>10</td>
      <td>고성(강원)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>3</td>
      <td>양주</td>
    </tr>
  </tbody>
</table>
</div>



## 2)  draw_korea / election_result 두 데이터의 ID 일치 여부 확인하기



```python
set( draw_korea['ID'].unique() )  -  set( election_result['ID'].unique() )
```




    {'고성(강원)', '고성(경남)', '부천 소사', '부천 오정', '부천 원미', '창원 합포', '창원 회원'}




```python
set( election_result['ID'].unique() ) - set( draw_korea['ID'].unique() )
```




    {'고성', '부천', '창원 마산합포', '창원 마산회원'}




```python
# 강원 고성과  경남 고성  서로 구분하기

election_result[ election_result['ID'] == '고성' ]
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>125</th>
      <td>강원도</td>
      <td>고성군</td>
      <td>18692</td>
      <td>5664</td>
      <td>30.58</td>
      <td>6511</td>
      <td>35.15</td>
      <td>3964</td>
      <td>21.40</td>
      <td>고성</td>
    </tr>
    <tr>
      <th>233</th>
      <td>경상남도</td>
      <td>고성군</td>
      <td>34603</td>
      <td>9848</td>
      <td>28.67</td>
      <td>16797</td>
      <td>48.91</td>
      <td>4104</td>
      <td>11.95</td>
      <td>고성</td>
    </tr>
  </tbody>
</table>
</div>




```python
election_result.loc[125, 'ID'] = '고성(강원)'
election_result.loc[233, 'ID'] = '고성(경남)'

election_result[ election_result['시군'] == '고성군' ]
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>125</th>
      <td>강원도</td>
      <td>고성군</td>
      <td>18692</td>
      <td>5664</td>
      <td>30.58</td>
      <td>6511</td>
      <td>35.15</td>
      <td>3964</td>
      <td>21.40</td>
      <td>고성(강원)</td>
    </tr>
    <tr>
      <th>233</th>
      <td>경상남도</td>
      <td>고성군</td>
      <td>34603</td>
      <td>9848</td>
      <td>28.67</td>
      <td>16797</td>
      <td>48.91</td>
      <td>4104</td>
      <td>11.95</td>
      <td>고성(경남)</td>
    </tr>
  </tbody>
</table>
</div>




```python
election_result.tail()
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>245</th>
      <td>경상남도</td>
      <td>산청군</td>
      <td>24513</td>
      <td>6561</td>
      <td>27.00</td>
      <td>12544</td>
      <td>51.63</td>
      <td>2753</td>
      <td>11.33</td>
      <td>산청</td>
    </tr>
    <tr>
      <th>246</th>
      <td>경상남도</td>
      <td>거창군</td>
      <td>41325</td>
      <td>11256</td>
      <td>27.48</td>
      <td>19976</td>
      <td>48.78</td>
      <td>4923</td>
      <td>12.02</td>
      <td>거창</td>
    </tr>
    <tr>
      <th>247</th>
      <td>경상남도</td>
      <td>합천군</td>
      <td>33021</td>
      <td>7143</td>
      <td>21.83</td>
      <td>19699</td>
      <td>60.22</td>
      <td>3077</td>
      <td>9.40</td>
      <td>합천</td>
    </tr>
    <tr>
      <th>248</th>
      <td>제주특별자치도</td>
      <td>제주시</td>
      <td>273163</td>
      <td>125717</td>
      <td>46.25</td>
      <td>48027</td>
      <td>17.67</td>
      <td>55971</td>
      <td>20.59</td>
      <td>제주</td>
    </tr>
    <tr>
      <th>249</th>
      <td>제주특별자치도</td>
      <td>서귀포시</td>
      <td>101296</td>
      <td>43776</td>
      <td>43.50</td>
      <td>20036</td>
      <td>19.91</td>
      <td>21890</td>
      <td>21.75</td>
      <td>서귀포</td>
    </tr>
  </tbody>
</table>
</div>




```python
#  창원시의 긴 이름의 구  검색하기

election_result[ election_result['광역시도'] == '경상남도' ][:7]
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>226</th>
      <td>경상남도</td>
      <td>창원시의창구</td>
      <td>164047</td>
      <td>60757</td>
      <td>37.22</td>
      <td>56887</td>
      <td>34.85</td>
      <td>22830</td>
      <td>13.98</td>
      <td>창원 의창</td>
    </tr>
    <tr>
      <th>227</th>
      <td>경상남도</td>
      <td>창원시성산구</td>
      <td>153327</td>
      <td>63717</td>
      <td>41.74</td>
      <td>42052</td>
      <td>27.54</td>
      <td>22923</td>
      <td>15.01</td>
      <td>창원 성산</td>
    </tr>
    <tr>
      <th>228</th>
      <td>경상남도</td>
      <td>창원시마산합포구</td>
      <td>119281</td>
      <td>35592</td>
      <td>29.99</td>
      <td>54488</td>
      <td>45.91</td>
      <td>14686</td>
      <td>12.37</td>
      <td>창원 마산합포</td>
    </tr>
    <tr>
      <th>229</th>
      <td>경상남도</td>
      <td>창원시마산회원구</td>
      <td>136757</td>
      <td>45014</td>
      <td>33.07</td>
      <td>56340</td>
      <td>41.39</td>
      <td>17744</td>
      <td>13.03</td>
      <td>창원 마산회원</td>
    </tr>
    <tr>
      <th>230</th>
      <td>경상남도</td>
      <td>창원시진해구</td>
      <td>114779</td>
      <td>41249</td>
      <td>36.11</td>
      <td>40049</td>
      <td>35.06</td>
      <td>17435</td>
      <td>15.26</td>
      <td>창원 진해</td>
    </tr>
    <tr>
      <th>231</th>
      <td>경상남도</td>
      <td>진주시</td>
      <td>222813</td>
      <td>73929</td>
      <td>33.35</td>
      <td>93751</td>
      <td>42.30</td>
      <td>26687</td>
      <td>12.04</td>
      <td>진주</td>
    </tr>
    <tr>
      <th>232</th>
      <td>경상남도</td>
      <td>통영시</td>
      <td>82855</td>
      <td>25477</td>
      <td>30.94</td>
      <td>36128</td>
      <td>43.87</td>
      <td>10738</td>
      <td>13.04</td>
      <td>통영</td>
    </tr>
  </tbody>
</table>
</div>




```python
election_result.loc[228, 'ID'] = '창원 합포'
election_result.loc[229, 'ID'] = '창원 회원'
election_result[ election_result['광역시도'] == '경상남도' ][:5]
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>226</th>
      <td>경상남도</td>
      <td>창원시의창구</td>
      <td>164047</td>
      <td>60757</td>
      <td>37.22</td>
      <td>56887</td>
      <td>34.85</td>
      <td>22830</td>
      <td>13.98</td>
      <td>창원 의창</td>
    </tr>
    <tr>
      <th>227</th>
      <td>경상남도</td>
      <td>창원시성산구</td>
      <td>153327</td>
      <td>63717</td>
      <td>41.74</td>
      <td>42052</td>
      <td>27.54</td>
      <td>22923</td>
      <td>15.01</td>
      <td>창원 성산</td>
    </tr>
    <tr>
      <th>228</th>
      <td>경상남도</td>
      <td>창원시마산합포구</td>
      <td>119281</td>
      <td>35592</td>
      <td>29.99</td>
      <td>54488</td>
      <td>45.91</td>
      <td>14686</td>
      <td>12.37</td>
      <td>창원 합포</td>
    </tr>
    <tr>
      <th>229</th>
      <td>경상남도</td>
      <td>창원시마산회원구</td>
      <td>136757</td>
      <td>45014</td>
      <td>33.07</td>
      <td>56340</td>
      <td>41.39</td>
      <td>17744</td>
      <td>13.03</td>
      <td>창원 회원</td>
    </tr>
    <tr>
      <th>230</th>
      <td>경상남도</td>
      <td>창원시진해구</td>
      <td>114779</td>
      <td>41249</td>
      <td>36.11</td>
      <td>40049</td>
      <td>35.06</td>
      <td>17435</td>
      <td>15.26</td>
      <td>창원 진해</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 부천
# 2016년 7월 4일 부터 구제를 폐지하고 10개의 행정복지센터(책임동)으로 전환 운영한다.

set( draw_korea['ID'].unique() )  -  set( election_result['ID'].unique() )
```




    {'부천 소사', '부천 오정', '부천 원미'}




```python
set( election_result['ID'].unique() ) - set( draw_korea['ID'].unique() )
```




    {'부천'}




```python
# 부천은 1/3씩 나눠서 처리

election_result[ election_result['시군'] == '부천시' ]
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>85</th>
      <td>경기도</td>
      <td>부천시</td>
      <td>543777</td>
      <td>239697</td>
      <td>44.23</td>
      <td>100544</td>
      <td>18.55</td>
      <td>128297</td>
      <td>23.67</td>
      <td>부천</td>
    </tr>
  </tbody>
</table>
</div>




```python
votes_부천 = election_result.loc[ 85, '투표수' ] /3
Moon_votes = election_result.loc[ 85, '문재인 득표' ] /3
Moon_rate = election_result.loc[ 85, '문재인 득표율' ]
Hong_votes = election_result.loc[ 85, '홍준표 득표' ] /3
Hong_rate = election_result.loc[ 85, '홍준표 득표율' ]
Ahn_votes = election_result.loc[ 85, '안철수 득표' ] /3
Ahn_rate = election_result.loc[ 85, '안철수 득표율' ]

election_result.loc[250] = ['경기도', '부천시', votes_부천, Moon_votes, Moon_rate, Hong_votes, Hong_rate, Ahn_votes, Ahn_rate, '부천 소사' ]
election_result.loc[251] = ['경기도', '부천시', votes_부천, Moon_votes, Moon_rate, Hong_votes, Hong_rate, Ahn_votes, Ahn_rate, '부천 오정' ]
election_result.loc[252] = ['경기도', '부천시', votes_부천, Moon_votes, Moon_rate, Hong_votes, Hong_rate, Ahn_votes, Ahn_rate, '부천 원미' ]
```


```python
election_result[ election_result['시군'] == '부천시' ]
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>85</th>
      <td>경기도</td>
      <td>부천시</td>
      <td>543777.0</td>
      <td>239697.0</td>
      <td>44.23</td>
      <td>100544.000000</td>
      <td>18.55</td>
      <td>128297.000000</td>
      <td>23.67</td>
      <td>부천</td>
    </tr>
    <tr>
      <th>250</th>
      <td>경기도</td>
      <td>부천시</td>
      <td>181259.0</td>
      <td>79899.0</td>
      <td>44.23</td>
      <td>33514.666667</td>
      <td>18.55</td>
      <td>42765.666667</td>
      <td>23.67</td>
      <td>부천 소사</td>
    </tr>
    <tr>
      <th>251</th>
      <td>경기도</td>
      <td>부천시</td>
      <td>181259.0</td>
      <td>79899.0</td>
      <td>44.23</td>
      <td>33514.666667</td>
      <td>18.55</td>
      <td>42765.666667</td>
      <td>23.67</td>
      <td>부천 오정</td>
    </tr>
    <tr>
      <th>252</th>
      <td>경기도</td>
      <td>부천시</td>
      <td>181259.0</td>
      <td>79899.0</td>
      <td>44.23</td>
      <td>33514.666667</td>
      <td>18.55</td>
      <td>42765.666667</td>
      <td>23.67</td>
      <td>부천 원미</td>
    </tr>
  </tbody>
</table>
</div>




```python
election_result.drop( [85], inplace=True  )
```


```python
election_result[ election_result['시군'] == '부천시' ]
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>250</th>
      <td>경기도</td>
      <td>부천시</td>
      <td>181259.0</td>
      <td>79899.0</td>
      <td>44.23</td>
      <td>33514.666667</td>
      <td>18.55</td>
      <td>42765.666667</td>
      <td>23.67</td>
      <td>부천 소사</td>
    </tr>
    <tr>
      <th>251</th>
      <td>경기도</td>
      <td>부천시</td>
      <td>181259.0</td>
      <td>79899.0</td>
      <td>44.23</td>
      <td>33514.666667</td>
      <td>18.55</td>
      <td>42765.666667</td>
      <td>23.67</td>
      <td>부천 오정</td>
    </tr>
    <tr>
      <th>252</th>
      <td>경기도</td>
      <td>부천시</td>
      <td>181259.0</td>
      <td>79899.0</td>
      <td>44.23</td>
      <td>33514.666667</td>
      <td>18.55</td>
      <td>42765.666667</td>
      <td>23.67</td>
      <td>부천 원미</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 일치 여부 최종 확인

set( draw_korea['ID'].unique() )  -  set( election_result['ID'].unique() )
```




    set()




```python
set( election_result['ID'].unique() )  -  set( draw_korea['ID'].unique() )
```




    set()



## 3) draw_korea / election_result 두 데이터를 합치기 : merge


```python
draw_korea_election = pd.merge(
    election_result,
    draw_korea,
    how='left',
    on=['ID']
)

draw_korea_election.head()
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
      <th>y</th>
      <th>x</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>서울특별시</td>
      <td>종로구</td>
      <td>102566.0</td>
      <td>42512.0</td>
      <td>41.59</td>
      <td>22325.0</td>
      <td>21.84</td>
      <td>22313.0</td>
      <td>21.83</td>
      <td>서울 종로</td>
      <td>4</td>
      <td>6</td>
    </tr>
    <tr>
      <th>1</th>
      <td>서울특별시</td>
      <td>중구</td>
      <td>82852.0</td>
      <td>34062.0</td>
      <td>41.23</td>
      <td>17901.0</td>
      <td>21.67</td>
      <td>19372.0</td>
      <td>23.45</td>
      <td>서울 중구</td>
      <td>5</td>
      <td>6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>서울특별시</td>
      <td>용산구</td>
      <td>148157.0</td>
      <td>58081.0</td>
      <td>39.33</td>
      <td>35230.0</td>
      <td>23.85</td>
      <td>32109.0</td>
      <td>21.74</td>
      <td>서울 용산</td>
      <td>6</td>
      <td>6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>서울특별시</td>
      <td>성동구</td>
      <td>203175.0</td>
      <td>86686.0</td>
      <td>42.80</td>
      <td>40566.0</td>
      <td>20.03</td>
      <td>45674.0</td>
      <td>22.55</td>
      <td>서울 성동</td>
      <td>5</td>
      <td>7</td>
    </tr>
    <tr>
      <th>4</th>
      <td>서울특별시</td>
      <td>광진구</td>
      <td>240030.0</td>
      <td>105512.0</td>
      <td>44.10</td>
      <td>46368.0</td>
      <td>19.38</td>
      <td>52824.0</td>
      <td>22.08</td>
      <td>서울 광진</td>
      <td>6</td>
      <td>7</td>
    </tr>
  </tbody>
</table>
</div>



## 4) 문재인 vs 홍준표,  문재인 vs 안철수,  홍준표 vs 안철수 득표율 정의하기


```python
draw_korea_election['문vs홍'] = draw_korea_election['문재인 득표율']  -  draw_korea_election['홍준표 득표율']
draw_korea_election['문vs안'] = draw_korea_election['문재인 득표율']  -  draw_korea_election['안철수 득표율']
draw_korea_election['홍vs안'] = draw_korea_election['홍준표 득표율']  -  draw_korea_election['안철수 득표율']
```


```python
draw_korea_election.tail()
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
      <th>광역시도</th>
      <th>시군</th>
      <th>투표수</th>
      <th>문재인 득표</th>
      <th>문재인 득표율</th>
      <th>홍준표 득표</th>
      <th>홍준표 득표율</th>
      <th>안철수 득표</th>
      <th>안철수 득표율</th>
      <th>ID</th>
      <th>y</th>
      <th>x</th>
      <th>문vs홍</th>
      <th>문vs안</th>
      <th>홍vs안</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>247</th>
      <td>제주특별자치도</td>
      <td>제주시</td>
      <td>273163.0</td>
      <td>125717.0</td>
      <td>46.25</td>
      <td>48027.000000</td>
      <td>17.67</td>
      <td>55971.000000</td>
      <td>20.59</td>
      <td>제주</td>
      <td>25</td>
      <td>5</td>
      <td>28.58</td>
      <td>25.66</td>
      <td>-2.92</td>
    </tr>
    <tr>
      <th>248</th>
      <td>제주특별자치도</td>
      <td>서귀포시</td>
      <td>101296.0</td>
      <td>43776.0</td>
      <td>43.50</td>
      <td>20036.000000</td>
      <td>19.91</td>
      <td>21890.000000</td>
      <td>21.75</td>
      <td>서귀포</td>
      <td>26</td>
      <td>5</td>
      <td>23.59</td>
      <td>21.75</td>
      <td>-1.84</td>
    </tr>
    <tr>
      <th>249</th>
      <td>경기도</td>
      <td>부천시</td>
      <td>181259.0</td>
      <td>79899.0</td>
      <td>44.23</td>
      <td>33514.666667</td>
      <td>18.55</td>
      <td>42765.666667</td>
      <td>23.67</td>
      <td>부천 소사</td>
      <td>4</td>
      <td>2</td>
      <td>25.68</td>
      <td>20.56</td>
      <td>-5.12</td>
    </tr>
    <tr>
      <th>250</th>
      <td>경기도</td>
      <td>부천시</td>
      <td>181259.0</td>
      <td>79899.0</td>
      <td>44.23</td>
      <td>33514.666667</td>
      <td>18.55</td>
      <td>42765.666667</td>
      <td>23.67</td>
      <td>부천 오정</td>
      <td>6</td>
      <td>2</td>
      <td>25.68</td>
      <td>20.56</td>
      <td>-5.12</td>
    </tr>
    <tr>
      <th>251</th>
      <td>경기도</td>
      <td>부천시</td>
      <td>181259.0</td>
      <td>79899.0</td>
      <td>44.23</td>
      <td>33514.666667</td>
      <td>18.55</td>
      <td>42765.666667</td>
      <td>23.67</td>
      <td>부천 원미</td>
      <td>5</td>
      <td>2</td>
      <td>25.68</td>
      <td>20.56</td>
      <td>-5.12</td>
    </tr>
  </tbody>
</table>
</div>



## 5)  draw_Korea 함수 가져오기


```python
def draw_Korea(target_data, blocked_map, c_map_name):
    gamma = 0.75
    white_label_min = (
        max(blocked_map[target_data]) - min(blocked_map[target_data]) * 0.25 + min( blocked_map[target_data] )        
    )
    data_label = target_data
    vmin = min( blocked_map[target_data] )
    vmax = max( blocked_map[target_data] )

    BORDER_LINES = [
        # 인천
        [(5, 1), (5,2), (7,2), (7,3), (11,3), (11,0)],
        # 서울
        [(5,4), (5,5), (2,5), (2,7), (4,7), (4,9), (7,9), (7,7), (9,7), (9,5), (10,5), (10,4), (5,4)],
        # 경기도
        [(1,7), (1,8), (3,8), (3,10), (10,10), (10,7), (12,7), (12,6), (11,6), (11,5), (12, 5), (12,4), (11,4), (11,3)],
        # 강원도
        [(8,10), (8,11), (6,11), (6,12)],
        # 충청북도
        [(12,5), (13,5), (13,4), (14,4), (14,5), (15,5), (15,4), (16,4), (16,2)],
        # 전라북도
        [(16,4), (17,4), (17,5), (16,5), (16,6), (19,6), (19,5), (20,5), (20,4), (21,4), (21,3), (19,3), (19,1)],
        # 대전시
        [(13,5), (13,6), (16,6)],
        #세종시
        [(13,5), (14,5)],
        #광주
        [(21,2), (21,3), (22,3), (22,4), (24,4), (24,2), (21,2)],
        #전라남도
        [(20,5), (21,5), (21,6), (23,6)],
        #충청북도
        [(10,8), (12,8), (12,9), (14,9), (14,8), (16,8), (16,6)],
        #경상북도
        [(14,9), (14,11), (14,12), (13,12), (13,13)],
        #대구
        [(15,8), (17,8), (17,10), (16,10), (16,11), (14,11)],
        #부산
        [(17,9), (18,9), (18,8), (19,8), (19,9), (20,9), (20,10), (21,10)],
        #울산
        [(16,11), (16,13)],
        # [(9,14), (9,15)],
        [(27,5), (27,6), (25,6)],
    ]

    map_data = blocked_map.pivot_table(
        index='y',
        columns='x',
        values=target_data
        )
    masked_map_data = np.ma.masked_where(
        np.isnan(map_data),
        map_data
        )
    plt.figure( figsize=(9, 11) )
    plt.pcolor(masked_map_data, vmin=vmin, vmax=vmax, cmap=c_map_name, edgecolor='#aaaaaa', linewidth=0.5 )

    # 지역 이름 표시
    for idx, row in blocked_map.iterrows():
        # 광역시는 구 이름이 겹치는 경우가 많아서 시단위 이름을 같이 표시한다.
        if len( row['ID'].split() ) ==2:
            display_name = '{}\n{}'.format(row['ID'].split()[0], row['ID'].split()[1])
        elif row['ID'][:2] =='고성':
            display_name = '고성'
        else:
            display_name = row['ID']
        # 서대문구, 서귀포시 같이 이름이 3자 이상인 경우에 작은 글자로 표시한다
        if len(display_name.splitlines()[-1]) >= 3:
            fontsize, linespacing = 9.5, 1.5
        else:
            fontsize, linespacing = 11, 1.2
        annocolor = 'white' if row[target_data] > white_label_min else 'black'
        plt.annotate(
            display_name,
            (row['x']+0.5, row['y']+0.5),
            weight='bold',
            fontsize=fontsize,
            ha='center',
            va='center',
            color=annocolor,
            linespacing=linespacing
        )

        # 시도 경계를 그린다.
        for path in BORDER_LINES:
            ys, xs = zip(*path)
            plt.plot(xs, ys, c='black', lw=2)

    plt.gca().invert_yaxis()
    plt.axis('off')
    plt.tight_layout()
    plt.show()
```


```python
import numpy as np

import matplotlib.pyplot as plt
%matplotlib inline

# 한글폰트 적용
import matplotlib.font_manager as fm

font_location = '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf'
font_name = fm.FontProperties(fname=font_location).get_name()

from matplotlib import rc
rc('font', family=font_name)
```


```python
draw_Korea('문vs홍', draw_korea_election, 'Blues' )
```


![png]({{ site.url }}/data/dataScience/05GeoData/output_65_0.png)



```python
#  대비 효과를 높이기 위한 draw_Korea 함수 수정


def draw_Korea(target_data, blocked_map, c_map_name):
    gamma = 0.75
#     white_label_min = (
#         max(blocked_map[target_data]) - min(blocked_map[target_data]) * 0.25 + min( blocked_map[target_data] )        
#     )
    white_label_min = 20.

    data_label = target_data
#     vmin = min( blocked_map[target_data] )
    vmin = -50
#     vmax = max( blocked_map[target_data] )
    vmax = 50

    BORDER_LINES = [
        # 인천
        [(5, 1), (5,2), (7,2), (7,3), (11,3), (11,0)],
        # 서울
        [(5,4), (5,5), (2,5), (2,7), (4,7), (4,9), (7,9), (7,7), (9,7), (9,5), (10,5), (10,4), (5,4)],
        # 경기도
        [(1,7), (1,8), (3,8), (3,10), (10,10), (10,7), (12,7), (12,6), (11,6), (11,5), (12, 5), (12,4), (11,4), (11,3)],
        # 강원도
        [(8,10), (8,11), (6,11), (6,12)],
        # 충청북도
        [(12,5), (13,5), (13,4), (14,4), (14,5), (15,5), (15,4), (16,4), (16,2)],
        # 전라북도
        [(16,4), (17,4), (17,5), (16,5), (16,6), (19,6), (19,5), (20,5), (20,4), (21,4), (21,3), (19,3), (19,1)],
        # 대전시
        [(13,5), (13,6), (16,6)],
        #세종시
        [(13,5), (14,5)],
        #광주
        [(21,2), (21,3), (22,3), (22,4), (24,4), (24,2), (21,2)],
        #전라남도
        [(20,5), (21,5), (21,6), (23,6)],
        #충청북도
        [(10,8), (12,8), (12,9), (14,9), (14,8), (16,8), (16,6)],
        #경상북도
        [(14,9), (14,11), (14,12), (13,12), (13,13)],
        #대구
        [(15,8), (17,8), (17,10), (16,10), (16,11), (14,11)],
        #부산
        [(17,9), (18,9), (18,8), (19,8), (19,9), (20,9), (20,10), (21,10)],
        #울산
        [(16,11), (16,13)],
        # [(9,14), (9,15)],
        [(27,5), (27,6), (25,6)],
    ]

    map_data = blocked_map.pivot_table(
        index='y',
        columns='x',
        values=target_data
        )
    masked_map_data = np.ma.masked_where(
        np.isnan(map_data),
        map_data
        )
    plt.figure( figsize=(9, 11) )
    plt.pcolor(masked_map_data, vmin=vmin, vmax=vmax, cmap=c_map_name, edgecolor='#aaaaaa', linewidth=0.5 )

    # 지역 이름 표시
    for idx, row in blocked_map.iterrows():
        # 광역시는 구 이름이 겹치는 경우가 많아서 시단위 이름을 같이 표시한다.
        if len( row['ID'].split() ) ==2:
            display_name = '{}\n{}'.format(row['ID'].split()[0], row['ID'].split()[1])
        elif row['ID'][:2] =='고성':
            display_name = '고성'
        else:
            display_name = row['ID']
        # 서대문구, 서귀포시 같이 이름이 3자 이상인 경우에 작은 글자로 표시한다
        if len(display_name.splitlines()[-1]) >= 3:
            fontsize, linespacing = 9.5, 1.5
        else:
            fontsize, linespacing = 11, 1.2
#         annocolor = 'white' if row[target_data] > white_label_min else 'black'
        annocolor = 'white' if np.abs( row[target_data] )  > white_label_min else 'black'
        plt.annotate(
            display_name,
            (row['x']+0.5, row['y']+0.5),
            weight='bold',
            fontsize=fontsize,
            ha='center',
            va='center',
            color=annocolor,
            linespacing=linespacing
        )

        # 시도 경계를 그린다.
        for path in BORDER_LINES:
            ys, xs = zip(*path)
            plt.plot(xs, ys, c='black', lw=2)

    plt.gca().invert_yaxis()
    plt.axis('off')
    plt.tight_layout()
    plt.show()
```

## 6) 지도 그리기
### (1)  Sequential ColorMaps
* Greys / Purples / `Blues` / Greens / Oranges / Reds / YlOrBr / YlOrRd / OrRd / RdPu / BuPu / GnBu / YlGnBu / PuBuGn / BuGn / YlGn

### (2)  Diverging ColorMaps
* PiYG / PRGn / BrBG / PuOr / RdGy / `RdBu` / RdYlBu / RdYlGn / Spectral / coolwarm / bwr / seismic


```python
draw_Korea('문vs홍', draw_korea_election, 'RdBu' )
```


![png]({{ site.url }}/data/dataScience/05GeoData/output_68_0.png)



```python
draw_Korea('문vs안', draw_korea_election, 'BrBG' )
```


![png]({{ site.url }}/data/dataScience/05GeoData/output_69_0.png)



```python
draw_Korea('홍vs안', draw_korea_election, 'BrBG' )
```

![png]({{ site.url }}/data/dataScience/05GeoData/output_70_0.png)
