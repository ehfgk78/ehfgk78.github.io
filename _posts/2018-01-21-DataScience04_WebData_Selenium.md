---
layout: post
title: "데이터 사이언스 04 웹자료 가져오기(2)- Selenium "
date: 2018-01-21 01:20 +0900
categories: [Data-Science]
tags: [Data-Science, Selenium]
---

* Kramdown table of contents
{:toc .toc}



# 1. Selenium

* BeautifulSoup는 requests 모듈 등으로 접근한 웹주소(URL)에서 웹자료를 쉽게 가져올 수 있지만,


## 1) Beautiful Soup의 한계 및 Selenium 소개

* 접근할 웹주소를 알 수 없거나
* 자바스크립트를 사용하는 경우
* 웹 브라우저로만 접근해야하는 경우(예를들어, 로그인 등이 필요한 경우)

위의 경우에는 **원격 웹브라우저인 selenium**이 필요하다.  selenium은,
* 웹 브라우저를 원격 조작하는 도구로서, 자동으로 URL을 열고, 클릭/스크롤/문자입력(로그인 시도)/화면캡쳐 등이 가능하다.


## 2) Selenium 설치

* [selenium](http://www.seleniumhq.org/)

```sh
➡ pip install selenium

  Downloading selenium-3.8.1-py2.py3-none-any.whl (942kB)
    100% |████████████████████████████████| 952kB 851kB/s
Installing collected packages: selenium
Successfully installed selenium-3.8.1
```

## 3) [크롬 드라이버 설치](https://chromedriver.storage.googleapis.com/index.html?path=2.30/)
* 최신 버전은 2.35이나 안정된 버전인 2.30설치함



---

# 2. 예제 1 - 네이버 메일 보낸 사람 리스트

* 네에버 로그인

![png]({{ site.url }}/data/dataScience/04Selenium/4-2-naver.png)



```python
from selenium import webdriver
```


```python
# 크롬 창 열기
driver = webdriver.Chrome('../crawler/driver/chromedriver')
```

![png]({{ site.url }}/data/dataScience/04Selenium/4-3-1-self_oil_station.png)



```python
# 네이버 :  이 창은 건들지 않는다.
## 별도의 네이버 창을 연다.
driver.get('http://naver.com')
```


```python
# 화면 캡쳐
driver.save_screenshot('./data_science/04img/1-3-naver.png')
```




    True




```python
# 네이버 로그인
## id
naver_login = driver.find_element_by_id("id")
naver_login.clear()
naver_login.send_keys("ehfgk")
## pw
naver_login = driver.find_element_by_id("pw")
naver_login.clear()
naver_login.send_keys("@naver2559")
## 로그인버튼 클릭
### Copy Xpath : //*[@id="frmNIDLogin"]/fieldset/span/input
driver.find_element_by_xpath("""//*[@id="frmNIDLogin"]/fieldset/span/input""").click()
```


```python
# naver 메일
driver.get("http://mail.naver.com")
```


```python
from bs4 import BeautifulSoup

#현재 페이지 주소
mail_page = driver.page_source
# Soup
soup = BeautifulSoup(mail_page, 'lxml')
```


```python
# 메일을 보낸 사람 태그
raw_list = soup.find_all('div', 'name _ccr(lst.from) ')
raw_list
```




    [<div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1931) _stopDefault" href="#" title='"Google" &lt;no-reply@accounts.google.com&gt;'>Google</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1928) _stopDefault" href="#" title='"Google" &lt;no-reply@accounts.google.com&gt;'>Google</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1836) _stopDefault" href="#" title='"네이버웹툰 주식회사" &lt;nv_webtoon_noreply@naver.com&gt;'>네이버웹툰 주식회사</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1717) _stopDefault" href="#" title='"!한국IT전문학교 입시정보!" &lt;navercafe@naver.com&gt;'>!한국IT전문학교 입..</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1662) _stopDefault" href="#" title='"2030산악회『저알콜서울경기/20대30대/하늘마루2030등산동호회』" &lt;navercafe@naver.com&gt;'>2030산악회『저알..</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1627) _stopDefault" href="#" title='"워드프레스앤(홈페이지,wordpress,테마,설치,쇼핑몰,강의,강좌)" &lt;navercafe@naver.com&gt;'>워드프레스앤(홈페..</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1534) _stopDefault" href="#" title='"한국사내변호사회" &lt;navercafe@naver.com&gt;'>한국사내변호사회</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1472) _stopDefault" href="#" title='"hwang sungyeon" &lt;latarte7@hanmail.net&gt;'>hwang sungyeon</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1404) _stopDefault" href="#" title='"achilles7" &lt;achilles7@naver.com&gt;'>achilles7</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1393) _stopDefault" href="#" title='"안나현" &lt;anh2002@hanmail.net&gt;'>안나현</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1387) _stopDefault" href="#" title='"노영빈" &lt;lawyb@naver.com&gt;'>노영빈</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1383) _stopDefault" href="#" title='"김경우" &lt;think810525@hotmail.com&gt;'>김경우</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1357) _stopDefault" href="#" title="&lt;sada111@naver.com&gt;">sada111@naver.com</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1354) _stopDefault" href="#" title='"낭만시대" &lt;newflag@naver.com&gt;'>낭만시대</a></div>,
     <div class="name _ccr(lst.from) "><span class="blind">보낸 이:</span><a class="_c1(myContextMenu|showSenderContextLayer|list|1353) _stopDefault" href="#" title='"낭만시대" &lt;newflag@naver.com&gt;'>낭만시대</a></div>]




```python
send_list = [ raw_list[n].find('a').get_text() for n in range(0, len(raw_list)) ]
send_list
```




    ['Google',
     'Google',
     '네이버웹툰 주식회사',
     '!한국IT전문학교 입..',
     '2030산악회『저알..',
     '워드프레스앤(홈페..',
     '한국사내변호사회',
     'hwang sungyeon',
     'achilles7',
     '안나현',
     '노영빈',
     '김경우',
     'sada111@naver.com',
     '낭만시대',
     '낭만시대']




```python
driver.close()
```




---

# 3. **셀프 주유소**가 저렴한가?



## 1) 대상


* 대상 :  [http://www.opinet.co.kr/](http://www.opinet.co.kr/)


```python
# 크롬 창 열기
driver = webdriver.Chrome('../crawler/driver/chromedriver')
```


```python
# opinet/지역별 주유소 찾기
driver.get("http://www.opinet.co.kr/searRgSelect.do")
```


```python
# 주유소 목록 구하기: 서울:  구
## 자바스크립트 부분 -  xpath : //*[@id="SIGUNGU_NM0"]
gu_list_raw = driver.find_element_by_xpath( """//*[@id="SIGUNGU_NM0"]""" )
### option들:  elements
gu_list = gu_list_raw.find_elements_by_tag_name("option")
```


```python
### 확인
gu_names = [ option.get_attribute("value") for option in gu_list ]
gu_names[:5]
```




    ['', '강남구', '강동구', '강북구', '강서구']




```python
gu_names.remove('')
```


```python
gu_names[:5]
```




    ['강남구', '강동구', '강북구', '강서구', '관악구']



## 2) 데이터 얻기

자바스크립트가 작동함.

* 리스트 박스에서 지역을 선택한다.
* '조회' 버튼을 누른다.  ➔ xpath: //*[@id="searRgSelect"]  : seaRch gu Select
import time* '엑셀 저장'버튼을 누른다


```python
import time


# 주유소 - 지역 - 서울 -  id: 자치 구 표시 칸
element = driver.find_element_by_id("SIGUNGU_NM0")
# 강남구 이름 입력
element.send_keys(gu_names[0])
time.sleep(2)

# 조회
element_select_gu = driver.find_element_by_xpath("""//*[@id="searRgSelect"]""")
time.sleep(2)

# 엑셀버튼 누르기
element_get_excel = driver.find_element_by_xpath("""//*[@id="glopopd_excel"]""").click()
```


```python
for gu in gu_names:
    # 주유소 - 지역 - 서울 -  id: 자치 구 표시 칸
    element = driver.find_element_by_id("SIGUNGU_NM0")
    # 구 이름 입력
    element.send_keys(gu)
    time.sleep(2)

    # 조회
    element_select_gu = driver.find_element_by_xpath("""//*[@id="searRgSelect"]""")
    time.sleep(2)

    # 엑셀버튼 누르기
    element_get_excel = driver.find_element_by_xpath("""//*[@id="glopopd_excel"]""").click()
    time.sleep(2)

    print(gu)
```

    강남구
    강동구
    강북구
    강서구
    관악구
    광진구
    구로구
    금천구
    노원구
    도봉구
    동대문구
    동작구
    마포구
    서대문구
    서초구
    성동구
    성북구
    송파구
    양천구
    영등포구
    용산구
    은평구
    종로구
    중구
    중랑구


## 3) 데이터 읽기  :  glob
* 파일 집합 다루기

> [https://docs.python.org/3/library/glob.html](https://docs.python.org/3/library/glob.html)

> [System∑Sec†ion :: 파이썬 – os.glob 모듈 - Tistory](http://devanix.tistory.com/299)

* 데이터 다듬기

데이터 자료구조 파악하기  -  `[data].info()`


```python
from glob import glob
import pandas as pd
```

![png]({{ site.url }}/data/dataScience/04Selenium/4-3-2-results.png)



```python
glob('./data_ouput/04.oil_stations_in_Seoul/지역*.xls')
```




    ['./data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (11).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (20).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (10).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (4).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (19).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (8).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (9).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (21).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (13).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (12).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (3).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (23).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (2).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (22).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (24).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (5).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (1).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (6).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (14).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (17).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (7).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (16).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (18).xls',
     './data_ouput/04.oil_stations_in_Seoul/지역_위치별(주유소) (15).xls']




```python
stations_files = glob('./data_ouput/04.oil_stations_in_Seoul/지역*.xls')
```


```python
tmp_raw = []

for file_name in stations_files:
    tmp = pd.read_excel(file_name, header=2)
    tmp_raw.append(tmp)

station_raw = pd.concat(tmp_raw)
```


```python
station_raw.tail()
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
      <th>지역</th>
      <th>상호</th>
      <th>주소</th>
      <th>상표</th>
      <th>전화번호</th>
      <th>셀프여부</th>
      <th>고급휘발유</th>
      <th>휘발유</th>
      <th>경유</th>
      <th>실내등유</th>
      <th>셀프</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>13</th>
      <td>서울특별시</td>
      <td>SK세원2주유소</td>
      <td>서울 성동구 광나루로 184 (성수동1가)</td>
      <td>SK에너지</td>
      <td>02-462-5665</td>
      <td>N</td>
      <td>1890</td>
      <td>1660</td>
      <td>1465</td>
      <td>-</td>
      <td>False</td>
    </tr>
    <tr>
      <th>14</th>
      <td>서울특별시</td>
      <td>금호주유소</td>
      <td>서울 성동구 금호로 39 (금호동4가)</td>
      <td>GS칼텍스</td>
      <td>02-2297-0066</td>
      <td>N</td>
      <td>-</td>
      <td>1698</td>
      <td>1478</td>
      <td>1260</td>
      <td>False</td>
    </tr>
    <tr>
      <th>15</th>
      <td>서울특별시</td>
      <td>동일주유소</td>
      <td>서울 성동구 광나루로 254 (성수동2가)</td>
      <td>현대오일뱅크</td>
      <td>02-461-7100</td>
      <td>N</td>
      <td>1998</td>
      <td>1745</td>
      <td>1538</td>
      <td>1149</td>
      <td>False</td>
    </tr>
    <tr>
      <th>16</th>
      <td>서울특별시</td>
      <td>청계로주유소</td>
      <td>서울 성동구 청계천로 454 (하왕십리동)</td>
      <td>SK에너지</td>
      <td>02-2294-4225</td>
      <td>N</td>
      <td>-</td>
      <td>1848</td>
      <td>1647</td>
      <td>1095</td>
      <td>False</td>
    </tr>
    <tr>
      <th>17</th>
      <td>서울특별시</td>
      <td>(주)옥수하이웨이스테이션</td>
      <td>서울 성동구 독서당로 168 (옥수동)</td>
      <td>GS칼텍스</td>
      <td>02-2282-5151</td>
      <td>N</td>
      <td>2151</td>
      <td>1856</td>
      <td>1685</td>
      <td>-</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>



* **셀프여부** 데이터 유형 바꾸기 :   string('N'/'Y')   ➔   boolean(True/False)


```python
station_raw['셀프'] =  station_raw['셀프여부'] == 'Y'
station_raw.head()
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
      <th>지역</th>
      <th>상호</th>
      <th>주소</th>
      <th>상표</th>
      <th>전화번호</th>
      <th>셀프여부</th>
      <th>고급휘발유</th>
      <th>휘발유</th>
      <th>경유</th>
      <th>실내등유</th>
      <th>셀프</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>서울특별시</td>
      <td>매일주유소</td>
      <td>서울특별시 동작구  상도로 139 (상도동)</td>
      <td>S-OIL</td>
      <td>02-817-4085</td>
      <td>N</td>
      <td>-</td>
      <td>1529</td>
      <td>1369</td>
      <td>1000</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>서울특별시</td>
      <td>서경주유소</td>
      <td>서울 동작구 대림로 46 (신대방동)</td>
      <td>현대오일뱅크</td>
      <td>02-843-5130</td>
      <td>N</td>
      <td>-</td>
      <td>1529</td>
      <td>1329</td>
      <td>-</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>서울특별시</td>
      <td>대방주유소</td>
      <td>서울특별시 동작구 여의대방로214 (대방동)</td>
      <td>GS칼텍스</td>
      <td>02-826-5145</td>
      <td>N</td>
      <td>1755</td>
      <td>1538</td>
      <td>1338</td>
      <td>950</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>서울특별시</td>
      <td>한원케미칼(주)</td>
      <td>서울특별시 동작구  동작대로 135 (사당동)</td>
      <td>GS칼텍스</td>
      <td>02-2183-3824</td>
      <td>Y</td>
      <td>-</td>
      <td>1539</td>
      <td>1329</td>
      <td>-</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>서울특별시</td>
      <td>금성주유소</td>
      <td>서울특별시 동작구  사당로 59 (상도동)</td>
      <td>현대오일뱅크</td>
      <td>02-825-5151</td>
      <td>N</td>
      <td>-</td>
      <td>1539</td>
      <td>1329</td>
      <td>-</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>




```python
stations = pd.DataFrame(
    {
        'Oil_store' : station_raw['상호'].values,
        '주소' : station_raw['주소'].values,
        '가격' : station_raw['휘발유'].values,
        '셀프' : station_raw['셀프'].values,
        '상표' : station_raw['상표'].values
    }
)

stations.head()
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
      <th>Oil_store</th>
      <th>가격</th>
      <th>상표</th>
      <th>셀프</th>
      <th>주소</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>매일주유소</td>
      <td>1529</td>
      <td>S-OIL</td>
      <td>False</td>
      <td>서울특별시 동작구  상도로 139 (상도동)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>서경주유소</td>
      <td>1529</td>
      <td>현대오일뱅크</td>
      <td>False</td>
      <td>서울 동작구 대림로 46 (신대방동)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>대방주유소</td>
      <td>1538</td>
      <td>GS칼텍스</td>
      <td>False</td>
      <td>서울특별시 동작구 여의대방로214 (대방동)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>한원케미칼(주)</td>
      <td>1539</td>
      <td>GS칼텍스</td>
      <td>True</td>
      <td>서울특별시 동작구  동작대로 135 (사당동)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>금성주유소</td>
      <td>1539</td>
      <td>현대오일뱅크</td>
      <td>False</td>
      <td>서울특별시 동작구  사당로 59 (상도동)</td>
    </tr>
  </tbody>
</table>
</div>




```python
stations['구'] = [ eachAddress.split()[1] for eachAddress in stations['주소'] ]
stations.head()
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
      <th>Oil_store</th>
      <th>가격</th>
      <th>상표</th>
      <th>셀프</th>
      <th>주소</th>
      <th>구</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>매일주유소</td>
      <td>1529</td>
      <td>S-OIL</td>
      <td>False</td>
      <td>서울특별시 동작구  상도로 139 (상도동)</td>
      <td>동작구</td>
    </tr>
    <tr>
      <th>1</th>
      <td>서경주유소</td>
      <td>1529</td>
      <td>현대오일뱅크</td>
      <td>False</td>
      <td>서울 동작구 대림로 46 (신대방동)</td>
      <td>동작구</td>
    </tr>
    <tr>
      <th>2</th>
      <td>대방주유소</td>
      <td>1538</td>
      <td>GS칼텍스</td>
      <td>False</td>
      <td>서울특별시 동작구 여의대방로214 (대방동)</td>
      <td>동작구</td>
    </tr>
    <tr>
      <th>3</th>
      <td>한원케미칼(주)</td>
      <td>1539</td>
      <td>GS칼텍스</td>
      <td>True</td>
      <td>서울특별시 동작구  동작대로 135 (사당동)</td>
      <td>동작구</td>
    </tr>
    <tr>
      <th>4</th>
      <td>금성주유소</td>
      <td>1539</td>
      <td>현대오일뱅크</td>
      <td>False</td>
      <td>서울특별시 동작구  사당로 59 (상도동)</td>
      <td>동작구</td>
    </tr>
  </tbody>
</table>
</div>




```python
stations.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 536 entries, 0 to 535
    Data columns (total 6 columns):
    Oil_store    536 non-null object
    가격           536 non-null object
    상표           536 non-null object
    셀프           536 non-null bool
    주소           536 non-null object
    구            536 non-null object
    dtypes: bool(1), object(5)
    memory usage: 21.5+ KB



```python
stations['구'].unique()
```




    array(['동작구', '용산구', '동대문구', '강서구', '영등포구', '노원구', '도봉구', '특별시', '은평구',
           '서대문구', '마포구', '강북구', '중구', '강동구', '종로구', '중랑구', '관악구', '강남구',
           '광진구', '구로구', '서초구', '송파구', '금천구', '성북구', '양천구', '성동구', '서울특별시'], dtype=object)




```python
# 서울특별시,  특별시
stations[ stations['구']=='서울특별시' ]
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
      <th>Oil_store</th>
      <th>가격</th>
      <th>상표</th>
      <th>셀프</th>
      <th>주소</th>
      <th>구</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>529</th>
      <td>SK네트웍스(주)효진주유소</td>
      <td>1654</td>
      <td>SK에너지</td>
      <td>False</td>
      <td>1 서울특별시 성동구 동일로 129 (성수동2가)</td>
      <td>서울특별시</td>
    </tr>
  </tbody>
</table>
</div>




```python
stations[ stations['구']=='특별시' ]
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
      <th>Oil_store</th>
      <th>가격</th>
      <th>상표</th>
      <th>셀프</th>
      <th>주소</th>
      <th>구</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>144</th>
      <td>서현주유소</td>
      <td>1524</td>
      <td>S-OIL</td>
      <td>True</td>
      <td>서울 특별시 도봉구 방학로 142 (방학동)</td>
      <td>특별시</td>
    </tr>
  </tbody>
</table>
</div>




```python
#  '서울특별시'가 쓰여진 raw의 '구'부분
stations.loc[ stations['구']=='서울특별시', '구' ] = '성동구'
```


```python
# '특별시'가 쓰여진 raw의 '구' 부분
stations.loc[ stations['구']=='특별시', '구' ] = '도봉구'
```


```python
stations['구'].unique()
```




    array(['동작구', '용산구', '동대문구', '강서구', '영등포구', '노원구', '도봉구', '은평구', '서대문구',
           '마포구', '강북구', '중구', '강동구', '종로구', '중랑구', '관악구', '강남구', '광진구', '구로구',
           '서초구', '송파구', '금천구', '성북구', '양천구', '성동구'], dtype=object)




```python
# 가격 부분 : 실수형으로 정하기
stations['가격'] = [float(values) for values in stations['가격']]
```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-68-f062bc7c80cf> in <module>()
          1 # 가격 부분
    ----> 2 stations['가격'] = [float(values) for values in stations['가격']]


    <ipython-input-68-f062bc7c80cf> in <listcomp>(.0)
          1 # 가격 부분
    ----> 2 stations['가격'] = [float(values) for values in stations['가격']]


    ValueError: could not convert string to float: '-'



```python
stations[ stations['가격'] == '-' ]
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
      <th>Oil_store</th>
      <th>가격</th>
      <th>상표</th>
      <th>셀프</th>
      <th>주소</th>
      <th>구</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>118</th>
      <td>하나주유소</td>
      <td>-</td>
      <td>S-OIL</td>
      <td>False</td>
      <td>서울특별시 영등포구  도림로 236 (신길동)</td>
      <td>영등포구</td>
    </tr>
    <tr>
      <th>217</th>
      <td>(주)에이앤이청담주유소</td>
      <td>-</td>
      <td>SK에너지</td>
      <td>True</td>
      <td>서울특별시 강북구 도봉로 155  (미아동)</td>
      <td>강북구</td>
    </tr>
    <tr>
      <th>248</th>
      <td>명진석유(주)동서울주유소</td>
      <td>-</td>
      <td>GS칼텍스</td>
      <td>True</td>
      <td>서울특별시 강동구  천호대로 1456 (상일동)</td>
      <td>강동구</td>
    </tr>
    <tr>
      <th>339</th>
      <td>SK서광그린주유소</td>
      <td>-</td>
      <td>SK에너지</td>
      <td>False</td>
      <td>서울특별시 강남구 봉은사로 222(역삼동)</td>
      <td>강남구</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 위 부분은 지우기 : 가격 정보가 없으므로,  데이터 분석을 할 수 없음
stations = stations[ stations['가격'] != '-' ]
stations[ stations['가격'] == '-' ]
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
      <th>Oil_store</th>
      <th>가격</th>
      <th>상표</th>
      <th>셀프</th>
      <th>주소</th>
      <th>구</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>




```python
# 가격 부분 : 실수형으로 정하기
stations['가격'] = [float(values) for values in stations['가격']]
```


```python
stations.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 532 entries, 0 to 535
    Data columns (total 6 columns):
    Oil_store    532 non-null object
    가격           532 non-null float64
    상표           532 non-null object
    셀프           532 non-null bool
    주소           532 non-null object
    구            532 non-null object
    dtypes: bool(1), float64(1), object(4)
    memory usage: 25.5+ KB


## 4)  시각화


```python
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline

# 한글 문제
import matplotlib.font_manager as fm
## 폰트 적용
font_location = '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf'
font_name = fm.FontProperties(fname=font_location).get_name()

from matplotlib import rc
rc('font', family=font_name)
```


```python
plt.figure( figsize=(12, 8) )
sns.boxplot(
    x='상표',
    y='가격',
    hue='셀프',
    data=stations,
    palette="Set3"
)
sns.swarmplot(
    x='상표',
    y='가격',
    data=stations,
    color='.6'
)
plt.show()
```

![png]({{ site.url }}/data/dataScience/04Selenium/output_45_0.png)
