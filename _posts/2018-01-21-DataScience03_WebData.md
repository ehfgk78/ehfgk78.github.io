---
layout: post
title: "데이터 사이언스 03 웹자료 가져오기(1)-BeautifulSoup"
date: 2018-01-21 01:10 +0900
categories: [Data-Science]
tags: [Data-Science, BeautifulSoup, requests, 크롬개발자도구]
---

* Kramdown table of contents
{:toc .toc}


# 1. 참조

>  [Beautiful Soup 공식문서](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)



---


# 2. 설치

## 1) BeautifulSoup + lxml

* install BeautifulSoup:  `➯ pip install beautifulsoup4`
* install HTML parser :  `➯  pip install lxml`


## 2) requests

* [requests 공식문서](http://docs.python-requests.org/en/master/)
* [나만의 웹크롤러 만들기 with Requests/BeautifulSoup](https://beomi.github.io/2017/01/20/HowToMakeWebCrawler/)
* **urllib.request.urlopen** 모듈 대신 requests.get이 직관적으로 사용하기 쉬움
* requests.get(URL)
* requests.compat.urljoin( BASE_address, relative_URL )



---


# 3. 구글 크롬 개발자 도구

## 1) XPATH

* [WiKi - XPath](https://ko.wikipedia.org/wiki/XPath)
* [W3Scholl - XPath Tutorials](https://www.w3schools.com/xml/xpath_intro.asp)

XPath(XML Path Language)는 W3C의 표준으로 확장 생성 언어 문서의 구조를 통해 경로 위에 지정한 구문을 사용하여 항목을 배치하고 처리하는 방법을 기술하는 언어이다. XML 표현보다 더 쉽고 약어로 되어 있으며, XSL 변환(XSLT)과 XML 지시자 언어(XPointer)에 쓰이는 언어이다. XPath는 XML 문서의 노드를 정의하기 위하여 경로식을 사용하며, 수학 함수와 기타 확장 가능한 표현들이 있다.



---

# 4. 시카고 샌드위치 맛집 소개 페이지 만들기

## 1) Web page 분석

* 구글링 :  **50 best sandwiches in chicago**
![png]({{ site.url }}/data/dataScience/03WebData/4-1-googling.png)

* 최종 목표

|<center>rank</center> | <center>Cafe</center> | <center>Menu</center> | <center>Price</center> | <center>Address</center> | <center>URL</center>|
|:--------:|:---------:|:-----------:|:---------:|:----------------:|:--------:|
|1 | Old Oak Tap | BLT | 10. | 2109 W. Chicago Ave. | http://www.chicagomag.com/Chicago-Magazine/|

* [마크다운 표 정렬 문법](https://steemit.com/kr/@antares007/-201787t14245290z)


## 2) BASE 페이지

![png]({{ site.url }}/data/dataScience/03WebData/4-2-page.png)


```python
# parser.py
import requests
from bs4 import BeautifulSoup
```


```python
# HTTP GET Request
response = requests.get('http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-Chicago/')
# HTML 소스 가져오기
html = response.text
```


```python
# BeautifulSoup으로 html소스를 python객체로 변환하기
# 첫 인자는 html소스코드, 두 번째 인자는 어떤 parser를 이용할지 명시.
# 여기에는  lxml(Python 내장 html.parser를 쓸 수 있다)를 이용했다.
soup = BeautifulSoup(html, 'lxml')
```

* **각각의 소개 페이지** :  50개의 샌드위치 가게를 소개하는  base 페이지에 대한 태그 분석


```python
len( soup.find_all('div', 'sammy') )
```




    50




```python
soup.find_all('div', 'sammy')[0]
```




    <div class="sammy" style="position: relative;">
    <div class="sammyRank">1</div>
    <div class="sammyListing"><a href="/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Old-Oak-Tap-BLT/"><b>BLT</b><br/>
    Old Oak Tap<br/>
    <em>Read more</em> </a></div>
    </div>




```python
tmp1 = soup.find_all('div', 'sammy')[0]
# 가게 순위
tmp1.findChildren()[0]
```




    <div class="sammyRank">1</div>




```python
tmp1.findChildren()[0].get_text()
```




    '1'




```python
# 메뉴와 가게 이름
tmp1.findChildren()[1]
```




    <div class="sammyListing"><a href="/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Old-Oak-Tap-BLT/"><b>BLT</b><br/>
    Old Oak Tap<br/>
    <em>Read more</em> </a></div>




```python
tmp1.findChildren()[1].get_text().replace('\r', '').split('\n')[:-1]
```




    ['BLT', 'Old Oak Tap']




```python
# 잡지내 상대 URL
tmp1.findChildren()[1].a.get('href')
```




    '/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Old-Oak-Tap-BLT/'




```python
# 절대 주소
base_address = 'http://www.chicagomag.com'
abs_address = requests.compat.urljoin(  base_address,  tmp1.findChildren()[1].a.get('href')  )
abs_address
```




    'http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Old-Oak-Tap-BLT/'



* **50개 가게 리스트 정리 **


```python
rank = []
main_menu = []
cafe_name = []
url_add = []

list_soup = soup.find_all('div', 'sammy')

for item in list_soup:
    child = item.findChildren()
    #순위
    rank.append(child[0].get_text())
    #메뉴와 가게이름
    tmp = child[1].get_text().replace('\r', '').split('\n')[:-1]    
    #메인 메뉴    
    main_menu.append( tmp[0] )
    #가게 이름
    cafe_name.append( tmp[1] )
    # URL
    base_address = 'http://www.chicagomag.com'
    abs_address = requests.compat.urljoin( base_address, child[1].a.get('href') )
    url_add.append( abs_address )    
```


```python
# for-Loop 결과 확인
rank[:5], main_menu[:5], cafe_name[:5], url_add[:10]
```




    (['1', '2', '3', '4', '5'],
     ['BLT', 'Fried Bologna', 'Woodland Mushroom', 'Roast Beef', 'PB&L'],
     ['Old Oak Tap', 'Au Cheval', 'Xoco', 'Al’s Deli', 'Publican Quality Meats'],
     ['http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Old-Oak-Tap-BLT/',
      'http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Au-Cheval-Fried-Bologna/',
      'http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Xoco-Woodland-Mushroom/',
      'http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Als-Deli-Roast-Beef/',
      'http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Publican-Quality-Meats-PB-L/',
      'http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Hendrickx-Belgian-Bread-Crafter-Belgian-Chicken-Curry-Salad/',
      'http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Acadia-Lobster-Roll/',
      'http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Birchwood-Kitchen-Smoked-Salmon-Salad/',
      'http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Cemitas-Puebla-Atomica-Cemitas/',
      'http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Nana-Grilled-Laughing-Bird-Shrimp-and-Fried-Oyster-Po-Boy/'])




```python
# 무결성 체크
len(rank), len(main_menu), len(cafe_name), len(url_add)
```




    (50, 50, 50, 50)



* **pandas.DataFrame**에 저장


```python
import pandas as pd
```


```python
data = {'Rank':rank, "Menu":main_menu, "Cafe": cafe_name, "URL":url_add}
df = pd.DataFrame(
    data,
    columns=['Rank', 'Cafe', 'Menu', 'URL']
)
df.head()
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
      <th>Rank</th>
      <th>Cafe</th>
      <th>Menu</th>
      <th>URL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Old Oak Tap</td>
      <td>BLT</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Au Cheval</td>
      <td>Fried Bologna</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Xoco</td>
      <td>Woodland Mushroom</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Al’s Deli</td>
      <td>Roast Beef</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Publican Quality Meats</td>
      <td>PB&amp;L</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.to_csv('./data_ouput/03.best_sandwiches_list_chicago.csv', sep=',', encoding='utf-8')
```


## 3) 하위 페이지 접근

* 샘플링:  첫번째 가게 : BLT	http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Old-Oak-Tap-BLT/
![png]({{ site.url }}/data/dataScience/03WebData/4-3-lower-page.png)


* 가격, 주소, 전화, 홈페이지 주소
![png]({{ site.url }}/data/dataScience/03WebData/4-3price_address.png)


```python
df['URL'][0]
```




    'http://www.chicagomag.com/Chicago-Magazine/November-2012/Best-Sandwiches-in-Chicago-Old-Oak-Tap-BLT/'




```python
response = requests.get( df['URL'][0] )
html = response.text
soup_tmp = BeautifulSoup( html, 'lxml' )
```


```python
tmp = soup_tmp.find('p', 'addy').get_text().strip()
tmp
```




    '$10. 2109 W. Chicago Ave., 773-772-0406, theoldoaktap.com'



* 가격과 주소 부분 - <pre>$으로 시작해서  첫번째 콤마(,)까지</pre>

* 가격과 주소 구분 -  <pre>$으로 시작해서 숫자가 나오다가  점(.) 다음에 숫자가 있거나  혹은 공백까지 </pre>


```python
tmp_price = tmp[  tmp.find('$') : tmp.find(' ')  ]
tmp_price
```




    '$10.'




```python
tmp_address = tmp[ tmp.find(' ') : tmp.find(',') ]
tmp_address
```




    ' 2109 W. Chicago Ave.'



* 5개 샘플링


```python
price = []
address = []

for n in range(5):
    response = requests.get( df['URL'][n] )
    html = response.text
    soup_tmp = BeautifulSoup( html, 'lxml' )

    tmp = soup_tmp.find('p', 'addy').get_text().strip()
    tmp_price = tmp[  tmp.find('$') : tmp.find(' ')-1  ]
    tmp_address = tmp[ tmp.find(' ') : tmp.find(',') ]

    print( tmp_price, "+", tmp_address )
```

    $10 +  2109 W. Chicago Ave.
    $9 +  800 W. Randolph St.
    $9.50 +  445 N. Clark St.
    $9.40 +  914 Noyes St.
    $10 +  825 W. Fulton Mkt.


* 전체 for-Loop


```python
df.index
```




    RangeIndex(start=0, stop=50, step=1)




```python
price = []
address = []

for n in df.index:
    response = requests.get( df['URL'][n] )
    html = response.text
    soup = BeautifulSoup( html, 'lxml' )

    item = soup.find('p', 'addy').get_text().strip()
    item_price = item[  item.find('$') : item.find(' ') - 1  ]
    item_address = item[ item.find(' ') : item.find(',') ]

    price.append(item_price)
    address.append(item_address)
```


```python
len(price), len(address), len(df)
```




    (50, 50, 50)




```python
df['Price'] = price
df['Address'] = address
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
      <th>Rank</th>
      <th>Cafe</th>
      <th>Menu</th>
      <th>URL</th>
      <th>Price</th>
      <th>Address</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>45</th>
      <td>46</td>
      <td>Chickpea</td>
      <td>Kufta</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
      <td>$8</td>
      <td>2018 W. Chicago Ave.</td>
    </tr>
    <tr>
      <th>46</th>
      <td>47</td>
      <td>The Goddess and Grocer</td>
      <td>Debbie’s Egg Salad</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
      <td>$6.50</td>
      <td>25 E. Delaware Pl.</td>
    </tr>
    <tr>
      <th>47</th>
      <td>48</td>
      <td>Zenwich</td>
      <td>Beef Curry</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
      <td>$7.50</td>
      <td>416 N. York St.</td>
    </tr>
    <tr>
      <th>48</th>
      <td>49</td>
      <td>Toni Patisserie</td>
      <td>Le Végétarien</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
      <td>$8.75</td>
      <td>65 E. Washington St.</td>
    </tr>
    <tr>
      <th>49</th>
      <td>50</td>
      <td>Phoebe’s Bakery</td>
      <td>The Gatsby</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
      <td>$6.85</td>
      <td>3351 N. Broadway</td>
    </tr>
  </tbody>
</table>
</div>




```python
df = df.loc[:, ['Rank', 'Cafe', 'Menu', 'Price', 'Address', 'URL'  ] ]
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
      <th>Rank</th>
      <th>Cafe</th>
      <th>Menu</th>
      <th>Price</th>
      <th>Address</th>
      <th>URL</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>45</th>
      <td>46</td>
      <td>Chickpea</td>
      <td>Kufta</td>
      <td>$8</td>
      <td>2018 W. Chicago Ave.</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
    </tr>
    <tr>
      <th>46</th>
      <td>47</td>
      <td>The Goddess and Grocer</td>
      <td>Debbie’s Egg Salad</td>
      <td>$6.50</td>
      <td>25 E. Delaware Pl.</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
    </tr>
    <tr>
      <th>47</th>
      <td>48</td>
      <td>Zenwich</td>
      <td>Beef Curry</td>
      <td>$7.50</td>
      <td>416 N. York St.</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
    </tr>
    <tr>
      <th>48</th>
      <td>49</td>
      <td>Toni Patisserie</td>
      <td>Le Végétarien</td>
      <td>$8.75</td>
      <td>65 E. Washington St.</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
    </tr>
    <tr>
      <th>49</th>
      <td>50</td>
      <td>Phoebe’s Bakery</td>
      <td>The Gatsby</td>
      <td>$6.85</td>
      <td>3351 N. Broadway</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.to_csv('./data_ouput/03.best_sandwiches_list_chicago2.csv', sep=',', encoding='UTF-8' )
```



## 4) 지도 시각화


```python
import folium
import pandas as pd
import googlemaps
import numpy as np
```


```python
gmaps = googlemaps.Client(key="AIzaSyC58AWrye6Z-2vAdoEVv_Xsf6sk6Vsg_90")
```


```python
tmp = df['Address'][1] + ', ' + 'Chicago'
tmp
```




    ' 800 W. Randolph St., Chicago'




```python
tmpMap = gmaps.geocode(tmp)
tmpMap
```




    [{'address_components': [{'long_name': '800',
        'short_name': '800',
        'types': ['street_number']},
       {'long_name': 'West Randolph Street',
        'short_name': 'W Randolph St',
        'types': ['route']},
       {'long_name': 'West Loop',
        'short_name': 'West Loop',
        'types': ['neighborhood', 'political']},
       {'long_name': 'Chicago',
        'short_name': 'Chicago',
        'types': ['locality', 'political']},
       {'long_name': 'Cook County',
        'short_name': 'Cook County',
        'types': ['administrative_area_level_2', 'political']},
       {'long_name': 'Illinois',
        'short_name': 'IL',
        'types': ['administrative_area_level_1', 'political']},
       {'long_name': 'United States',
        'short_name': 'US',
        'types': ['country', 'political']},
       {'long_name': '60607', 'short_name': '60607', 'types': ['postal_code']},
       {'long_name': '2308',
        'short_name': '2308',
        'types': ['postal_code_suffix']}],
      'formatted_address': '800 W Randolph St, Chicago, IL 60607, USA',
      'geometry': {'location': {'lat': 41.8846582, 'lng': -87.6476668},
       'location_type': 'ROOFTOP',
       'viewport': {'northeast': {'lat': 41.88600718029149,
         'lng': -87.64631781970849},
        'southwest': {'lat': 41.88330921970849, 'lng': -87.6490157802915}}},
      'place_id': 'ChIJNQXlSMUsDogRPCtvEAVtglU',
      'types': ['street_address']}]




```python
tmp_loc = tmpMap[0].get('geometry')
tmp_loc
```




    {'location': {'lat': 41.8846582, 'lng': -87.6476668},
     'location_type': 'ROOFTOP',
     'viewport': {'northeast': {'lat': 41.88600718029149,
       'lng': -87.64631781970849},
      'southwest': {'lat': 41.88330921970849, 'lng': -87.6490157802915}}}




```python
tmp_loc['location']['lat'], tmp_loc['location']['lng']
```




    (41.8846582, -87.6476668)




```python
lat = []
lng = []

for n in df.index:
    if df['Address'][n] == 'Multiple locations':
        lat.append(np.nan)
        lng.append(np.nan)

    elif df['Address'][n] != 'Multiple locations':
        tmp = df['Address'][n] + ', ' + 'Chicago'
        tmp_map = gmaps.geocode(tmp)
        tmp_loc = tmp_map[0].get('geometry')
        lat.append(tmp_loc['location']['lat'])
        lng.append(tmp_loc['location']['lng'])        
```


```python
len(lat), len(lng)
```




    (50, 50)




```python
df['lat'] = lat
df['lng'] = lng
df.head()
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
      <th>Rank</th>
      <th>Cafe</th>
      <th>Menu</th>
      <th>Price</th>
      <th>Address</th>
      <th>URL</th>
      <th>lat</th>
      <th>lng</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Old Oak Tap</td>
      <td>BLT</td>
      <td>$10</td>
      <td>2109 W. Chicago Ave.</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
      <td>41.895605</td>
      <td>-87.679961</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Au Cheval</td>
      <td>Fried Bologna</td>
      <td>$9</td>
      <td>800 W. Randolph St.</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
      <td>41.884658</td>
      <td>-87.647667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Xoco</td>
      <td>Woodland Mushroom</td>
      <td>$9.50</td>
      <td>445 N. Clark St.</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
      <td>41.890570</td>
      <td>-87.630795</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Al’s Deli</td>
      <td>Roast Beef</td>
      <td>$9.40</td>
      <td>914 Noyes St.</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
      <td>42.058322</td>
      <td>-87.683748</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Publican Quality Meats</td>
      <td>PB&amp;L</td>
      <td>$10</td>
      <td>825 W. Fulton Mkt.</td>
      <td>http://www.chicagomag.com/Chicago-Magazine/Nov...</td>
      <td>41.886596</td>
      <td>-87.648557</td>
    </tr>
  </tbody>
</table>
</div>




```python
mapping = folium.Map(
    location=[ df['lat'].mean(), df['lng'].mean() ],
    zoom_start=11
)

for n in df.index:
    if df['Address'][n] != 'Multiple locations':
        folium.Marker(
            [ df['lat'][n], df['lng'][n] ],
            popup=df['Cafe'][n]
        ).add_to(mapping)

mapping        
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><iframe src="data:text/html;charset=utf-8;base64,PCFET0NUWVBFIGh0bWw+CjxoZWFkPiAgICAKICAgIDxtZXRhIGh0dHAtZXF1aXY9ImNvbnRlbnQtdHlwZSIgY29udGVudD0idGV4dC9odG1sOyBjaGFyc2V0PVVURi04IiAvPgogICAgPHNjcmlwdD5MX1BSRUZFUl9DQU5WQVMgPSBmYWxzZTsgTF9OT19UT1VDSCA9IGZhbHNlOyBMX0RJU0FCTEVfM0QgPSBmYWxzZTs8L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmpzIj48L3NjcmlwdD4KICAgIDxzY3JpcHQgc3JjPSJodHRwczovL2FqYXguZ29vZ2xlYXBpcy5jb20vYWpheC9saWJzL2pxdWVyeS8xLjExLjEvanF1ZXJ5Lm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvanMvYm9vdHN0cmFwLm1pbi5qcyI+PC9zY3JpcHQ+CiAgICA8c2NyaXB0IHNyYz0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuanMiPjwvc2NyaXB0PgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL2Nkbi5qc2RlbGl2ci5uZXQvbnBtL2xlYWZsZXRAMS4yLjAvZGlzdC9sZWFmbGV0LmNzcyIgLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9tYXhjZG4uYm9vdHN0cmFwY2RuLmNvbS9ib290c3RyYXAvMy4yLjAvY3NzL2Jvb3RzdHJhcC5taW4uY3NzIiAvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL21heGNkbi5ib290c3RyYXBjZG4uY29tL2Jvb3RzdHJhcC8zLjIuMC9jc3MvYm9vdHN0cmFwLXRoZW1lLm1pbi5jc3MiIC8+CiAgICA8bGluayByZWw9InN0eWxlc2hlZXQiIGhyZWY9Imh0dHBzOi8vbWF4Y2RuLmJvb3RzdHJhcGNkbi5jb20vZm9udC1hd2Vzb21lLzQuNi4zL2Nzcy9mb250LWF3ZXNvbWUubWluLmNzcyIgLz4KICAgIDxsaW5rIHJlbD0ic3R5bGVzaGVldCIgaHJlZj0iaHR0cHM6Ly9jZG5qcy5jbG91ZGZsYXJlLmNvbS9hamF4L2xpYnMvTGVhZmxldC5hd2Vzb21lLW1hcmtlcnMvMi4wLjIvbGVhZmxldC5hd2Vzb21lLW1hcmtlcnMuY3NzIiAvPgogICAgPGxpbmsgcmVsPSJzdHlsZXNoZWV0IiBocmVmPSJodHRwczovL3Jhd2dpdC5jb20vcHl0aG9uLXZpc3VhbGl6YXRpb24vZm9saXVtL21hc3Rlci9mb2xpdW0vdGVtcGxhdGVzL2xlYWZsZXQuYXdlc29tZS5yb3RhdGUuY3NzIiAvPgogICAgPHN0eWxlPmh0bWwsIGJvZHkge3dpZHRoOiAxMDAlO2hlaWdodDogMTAwJTttYXJnaW46IDA7cGFkZGluZzogMDt9PC9zdHlsZT4KICAgIDxzdHlsZT4jbWFwIHtwb3NpdGlvbjphYnNvbHV0ZTt0b3A6MDtib3R0b206MDtyaWdodDowO2xlZnQ6MDt9PC9zdHlsZT4KICAgIAogICAgICAgICAgICA8c3R5bGU+ICNtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkgewogICAgICAgICAgICAgICAgcG9zaXRpb24gOiByZWxhdGl2ZTsKICAgICAgICAgICAgICAgIHdpZHRoIDogMTAwLjAlOwogICAgICAgICAgICAgICAgaGVpZ2h0OiAxMDAuMCU7CiAgICAgICAgICAgICAgICBsZWZ0OiAwLjAlOwogICAgICAgICAgICAgICAgdG9wOiAwLjAlOwogICAgICAgICAgICAgICAgfQogICAgICAgICAgICA8L3N0eWxlPgogICAgICAgIAo8L2hlYWQ+Cjxib2R5PiAgICAKICAgIAogICAgICAgICAgICA8ZGl2IGNsYXNzPSJmb2xpdW0tbWFwIiBpZD0ibWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5IiA+PC9kaXY+CiAgICAgICAgCjwvYm9keT4KPHNjcmlwdD4gICAgCiAgICAKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGJvdW5kcyA9IG51bGw7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgdmFyIG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSA9IEwubWFwKAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgJ21hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOScsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB7Y2VudGVyOiBbNDEuOTMzODAxNTI0LC04Ny41MDUyODcxMDZdLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgem9vbTogMTEsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBtYXhCb3VuZHM6IGJvdW5kcywKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGxheWVyczogW10sCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICB3b3JsZENvcHlKdW1wOiBmYWxzZSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGNyczogTC5DUlMuRVBTRzM4NTcKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgfSk7CiAgICAgICAgICAgIAogICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciB0aWxlX2xheWVyXzZiNWFiMTFkMTJmYzQzZDVhZGUxNDcxMjU3MDQ1MTAwID0gTC50aWxlTGF5ZXIoCiAgICAgICAgICAgICAgICAnaHR0cHM6Ly97c30udGlsZS5vcGVuc3RyZWV0bWFwLm9yZy97en0ve3h9L3t5fS5wbmcnLAogICAgICAgICAgICAgICAgewogICJhdHRyaWJ1dGlvbiI6IG51bGwsCiAgImRldGVjdFJldGluYSI6IGZhbHNlLAogICJtYXhab29tIjogMTgsCiAgIm1pblpvb20iOiAxLAogICJub1dyYXAiOiBmYWxzZSwKICAic3ViZG9tYWlucyI6ICJhYmMiCn0KICAgICAgICAgICAgICAgICkuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl8wOTg3ZWY5OTc4Zjg0YjI3ODUzMWUzMTE1MzZkNjk1MyA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjg5NTYwNDksLTg3LjY3OTk2MTVdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83MGU0M2RlNjIzZDg0MmU0YTRlODdlODBkY2I2ZDFlOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mMDQzYjI5ZDY2ZTg0MmZiOTFhMWYxYjhjMWMwZjM1ZCA9ICQoJzxkaXYgaWQ9Imh0bWxfZjA0M2IyOWQ2NmU4NDJmYjkxYTFmMWI4YzFjMGYzNWQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk9sZCBPYWsgVGFwPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83MGU0M2RlNjIzZDg0MmU0YTRlODdlODBkY2I2ZDFlOS5zZXRDb250ZW50KGh0bWxfZjA0M2IyOWQ2NmU4NDJmYjkxYTFmMWI4YzFjMGYzNWQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl8wOTg3ZWY5OTc4Zjg0YjI3ODUzMWUzMTE1MzZkNjk1My5iaW5kUG9wdXAocG9wdXBfNzBlNDNkZTYyM2Q4NDJlNGE0ZTg3ZTgwZGNiNmQxZTkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfNjkzY2ZmYzAzNjUwNDQ2M2I4N2ZmODBlNDcyZjUxMTYgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS44ODQ2NTgyLC04Ny42NDc2NjY4XSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNTQ3MmViNGU1OTc5NGYyNWIwYzE1MTc1MDFkMjM4NDQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZGI3OTJmNTNiMTVlNDRiM2E1NjAwNTg0ZWIwZDYyMjggPSAkKCc8ZGl2IGlkPSJodG1sX2RiNzkyZjUzYjE1ZTQ0YjNhNTYwMDU4NGViMGQ2MjI4IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5BdSBDaGV2YWw8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzU0NzJlYjRlNTk3OTRmMjViMGMxNTE3NTAxZDIzODQ0LnNldENvbnRlbnQoaHRtbF9kYjc5MmY1M2IxNWU0NGIzYTU2MDA1ODRlYjBkNjIyOCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzY5M2NmZmMwMzY1MDQ0NjNiODdmZjgwZTQ3MmY1MTE2LmJpbmRQb3B1cChwb3B1cF81NDcyZWI0ZTU5Nzk0ZjI1YjBjMTUxNzUwMWQyMzg0NCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl9mZGFlMGQ2NDM4YmM0Njc0OTIwMjQ3YjRkODY0ODkxMyA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjg5MDU2OTksLTg3LjYzMDc5NV0sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2YyYTg0MmQ2NjhkZDQyNzA4Yjg1YmQxMDdjNDFmODc1ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzJjZWI3Yjg5NDViMjQ2ZTY5NTQ4MmU3YzI2OGQ5Y2I0ID0gJCgnPGRpdiBpZD0iaHRtbF8yY2ViN2I4OTQ1YjI0NmU2OTU0ODJlN2MyNjhkOWNiNCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+WG9jbzwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZjJhODQyZDY2OGRkNDI3MDhiODViZDEwN2M0MWY4NzUuc2V0Q29udGVudChodG1sXzJjZWI3Yjg5NDViMjQ2ZTY5NTQ4MmU3YzI2OGQ5Y2I0KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfZmRhZTBkNjQzOGJjNDY3NDkyMDI0N2I0ZDg2NDg5MTMuYmluZFBvcHVwKHBvcHVwX2YyYTg0MmQ2NjhkZDQyNzA4Yjg1YmQxMDdjNDFmODc1KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyX2U4MTdkZjExODVmZDQzZTA4ZjMyMGEyNzM4ZjJmMzA1ID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDIuMDU4MzIxNywtODcuNjgzNzQ3OV0sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2Y5NTMzNDJmZDMxZTRlZmFiZTg4NDBiZDliYmMyY2M3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzQ0NDM3ZTA1MjA2MTQwNzViZmNjZGU2NGIxNjIzN2E3ID0gJCgnPGRpdiBpZD0iaHRtbF80NDQzN2UwNTIwNjE0MDc1YmZjY2RlNjRiMTYyMzdhNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QWzigJlzIERlbGk8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Y5NTMzNDJmZDMxZTRlZmFiZTg4NDBiZDliYmMyY2M3LnNldENvbnRlbnQoaHRtbF80NDQzN2UwNTIwNjE0MDc1YmZjY2RlNjRiMTYyMzdhNyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyX2U4MTdkZjExODVmZDQzZTA4ZjMyMGEyNzM4ZjJmMzA1LmJpbmRQb3B1cChwb3B1cF9mOTUzMzQyZmQzMWU0ZWZhYmU4ODQwYmQ5YmJjMmNjNyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl81YTc3MzRlMDVjZDY0NGMwYmNjOTZlMDM2YmVmMmZiMSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjg4NjU5NTYsLTg3LjY0ODU1NzFdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lMzAxZmI2MGEzODQ0YjdlOTVmYmU2N2NhM2Q4MTA2NiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF84MzQ0ZWYyYzVkZmU0MTI3OTJhMjA5MjA0YTY1ZGYzYSA9ICQoJzxkaXYgaWQ9Imh0bWxfODM0NGVmMmM1ZGZlNDEyNzkyYTIwOTIwNGE2NWRmM2EiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlB1YmxpY2FuIFF1YWxpdHkgTWVhdHM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2UzMDFmYjYwYTM4NDRiN2U5NWZiZTY3Y2EzZDgxMDY2LnNldENvbnRlbnQoaHRtbF84MzQ0ZWYyYzVkZmU0MTI3OTJhMjA5MjA0YTY1ZGYzYSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzVhNzczNGUwNWNkNjQ0YzBiY2M5NmUwMzZiZWYyZmIxLmJpbmRQb3B1cChwb3B1cF9lMzAxZmI2MGEzODQ0YjdlOTVmYmU2N2NhM2Q4MTA2Nik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl9kMWVkMjQ0YzMyOTc0ZTUzOTQ3MTVjN2Q0ZTNjZGMzNSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjkwMDIyMTMsLTg3LjYyNTE3MDNdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kM2NiMTNlZDc1MjM0MzU2OWYzY2Q2ZTc5Zjc1YmViYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mM2ExNTYyYjgxNWU0M2Y0YTVkZTZhNDg1NDliNGUyMCA9ICQoJzxkaXYgaWQ9Imh0bWxfZjNhMTU2MmI4MTVlNDNmNGE1ZGU2YTQ4NTQ5YjRlMjAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkhlbmRyaWNreCBCZWxnaWFuIEJyZWFkIENyYWZ0ZXI8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2QzY2IxM2VkNzUyMzQzNTY5ZjNjZDZlNzlmNzViZWJjLnNldENvbnRlbnQoaHRtbF9mM2ExNTYyYjgxNWU0M2Y0YTVkZTZhNDg1NDliNGUyMCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyX2QxZWQyNDRjMzI5NzRlNTM5NDcxNWM3ZDRlM2NkYzM1LmJpbmRQb3B1cChwb3B1cF9kM2NiMTNlZDc1MjM0MzU2OWYzY2Q2ZTc5Zjc1YmViYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl85NDMyZGU5YmFhOTQ0ZDE0OGU5MmQ5MGNkYmY2MGZmMyA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjg1OTA0MTgsLTg3LjYyNTIxNThdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zYjE3ODQ4OWYwZmI0YTU2ODkxYTdkMmNmNTc5MTg0MSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82MDVlYmZjOTRlYTk0ODllODYyYmM4OTVlNmJmNDY0OSA9ICQoJzxkaXYgaWQ9Imh0bWxfNjA1ZWJmYzk0ZWE5NDg5ZTg2MmJjODk1ZTZiZjQ2NDkiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkFjYWRpYTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfM2IxNzg0ODlmMGZiNGE1Njg5MWE3ZDJjZjU3OTE4NDEuc2V0Q29udGVudChodG1sXzYwNWViZmM5NGVhOTQ4OWU4NjJiYzg5NWU2YmY0NjQ5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfOTQzMmRlOWJhYTk0NGQxNDhlOTJkOTBjZGJmNjBmZjMuYmluZFBvcHVwKHBvcHVwXzNiMTc4NDg5ZjBmYjRhNTY4OTFhN2QyY2Y1NzkxODQxKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyXzExYmU4YzE0Mzk0NDQ5Y2ZhYWI0ZmU1MDk1YmRhOWY2ID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuOTEwMjIxNiwtODcuNjgyODY0OF0sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzkxNTA3MjQzYjFkZTQwY2ZiZjlkYmJmY2JjM2RlY2I0ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2RjNzFlZTlhZmY4ZjRmODY4MDMxYmQ3ODE2NDU1NWRlID0gJCgnPGRpdiBpZD0iaHRtbF9kYzcxZWU5YWZmOGY0Zjg2ODAzMWJkNzgxNjQ1NTVkZSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QmlyY2h3b29kIEtpdGNoZW48L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzkxNTA3MjQzYjFkZTQwY2ZiZjlkYmJmY2JjM2RlY2I0LnNldENvbnRlbnQoaHRtbF9kYzcxZWU5YWZmOGY0Zjg2ODAzMWJkNzgxNjQ1NTVkZSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzExYmU4YzE0Mzk0NDQ5Y2ZhYWI0ZmU1MDk1YmRhOWY2LmJpbmRQb3B1cChwb3B1cF85MTUwNzI0M2IxZGU0MGNmYmY5ZGJiZmNiYzNkZWNiNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl9hNzJkZDAwZDI1ZTU0NTJkODdlMzk4NmNjY2UyZmJhMCA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjkwOTc2MzYsLTg3LjcxNzY2NzRdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF81MjRjNzk5MTdjNDg0YWM2YjBlNzZmZGM1MGQ5NGI1OSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80ZGI3MTEzZWM0NDg0NmFkYWFiN2FhZGMzM2MzM2ZiZSA9ICQoJzxkaXYgaWQ9Imh0bWxfNGRiNzExM2VjNDQ4NDZhZGFhYjdhYWRjMzNjMzNmYmUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNlbWl0YXMgUHVlYmxhPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF81MjRjNzk5MTdjNDg0YWM2YjBlNzZmZGM1MGQ5NGI1OS5zZXRDb250ZW50KGh0bWxfNGRiNzExM2VjNDQ4NDZhZGFhYjdhYWRjMzNjMzNmYmUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl9hNzJkZDAwZDI1ZTU0NTJkODdlMzk4NmNjY2UyZmJhMC5iaW5kUG9wdXAocG9wdXBfNTI0Yzc5OTE3YzQ4NGFjNmIwZTc2ZmRjNTBkOTRiNTkpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfMDljYzA2MzA4N2IzNDJiZWJhNmU3MmViYWIzM2ZmMzUgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS44MzQ1NDUxLC04Ny42NDU4NDI3XSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZWM3ZjY1Y2Q4MTgyNDIyNWEwMWUyNGI2OTJkZDFmMzEgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjIwY2JjNmMzMTFkNGNhZGIyYmRkYjE3NWY1MmJhZDQgPSAkKCc8ZGl2IGlkPSJodG1sXzYyMGNiYzZjMzExZDRjYWRiMmJkZGIxNzVmNTJiYWQ0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5OYW5hPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lYzdmNjVjZDgxODI0MjI1YTAxZTI0YjY5MmRkMWYzMS5zZXRDb250ZW50KGh0bWxfNjIwY2JjNmMzMTFkNGNhZGIyYmRkYjE3NWY1MmJhZDQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl8wOWNjMDYzMDg3YjM0MmJlYmE2ZTcyZWJhYjMzZmYzNS5iaW5kUG9wdXAocG9wdXBfZWM3ZjY1Y2Q4MTgyNDIyNWEwMWUyNGI2OTJkZDFmMzEpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfYjQ2ZWYzYzJkNTk4NGViYWEyZmIyNDVkMWE2M2RiZTIgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS45Mjc2MzY5LC04Ny43MDY5NzU2XSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjIyM2Q5Yjc3YWI2NGI1Mzk3MTI2MDQzMTg2ZjJlZWUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMzI1MGJlM2JmNDAyNDY2OTkyYmUzYjg0NGI3ZjliOTMgPSAkKCc8ZGl2IGlkPSJodG1sXzMyNTBiZTNiZjQwMjQ2Njk5MmJlM2I4NDRiN2Y5YjkzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MdWxhIENhZmU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2IyMjNkOWI3N2FiNjRiNTM5NzEyNjA0MzE4NmYyZWVlLnNldENvbnRlbnQoaHRtbF8zMjUwYmUzYmY0MDI0NjY5OTJiZTNiODQ0YjdmOWI5Myk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyX2I0NmVmM2MyZDU5ODRlYmFhMmZiMjQ1ZDFhNjNkYmUyLmJpbmRQb3B1cChwb3B1cF9iMjIzZDliNzdhYjY0YjUzOTcxMjYwNDMxODZmMmVlZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl9jMTA2NWJhNjFiY2E0NDdlOTAwNDYyZjAyZDdjMDUwZSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjkwNTgxLC04Ny42NzI0ODNdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mYmJkOTMzZjJlYjc0YmQ0YTdlMDVkMTFjNDZiNmE1NyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8wYjI5MzEyZTlmZTg0ODZlOTcxZmMzNDcxYTk3YmRkNyA9ICQoJzxkaXYgaWQ9Imh0bWxfMGIyOTMxMmU5ZmU4NDg2ZTk3MWZjMzQ3MWE5N2JkZDciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJpY29iZW5l4oCZczwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZmJiZDkzM2YyZWI3NGJkNGE3ZTA1ZDExYzQ2YjZhNTcuc2V0Q29udGVudChodG1sXzBiMjkzMTJlOWZlODQ4NmU5NzFmYzM0NzFhOTdiZGQ3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfYzEwNjViYTYxYmNhNDQ3ZTkwMDQ2MmYwMmQ3YzA1MGUuYmluZFBvcHVwKHBvcHVwX2ZiYmQ5MzNmMmViNzRiZDRhN2UwNWQxMWM0NmI2YTU3KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyX2JmYTQ1ZmNjMDExNzQ5M2Y5NGMxMDYzZWVjMWVmZTg1ID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuOTM4NDQxNSwtODcuNjQ0MzU3N10sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzRiMDQ1ZTQ0M2QxYTQ5NDBiM2IzOTgxZjkzMjIzYjE3ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzBjNDM3ZmJmNGE2ZjQzNmM4ZGI4NDA5NmRkNzZiYmRmID0gJCgnPGRpdiBpZD0iaHRtbF8wYzQzN2ZiZjRhNmY0MzZjOGRiODQwOTZkZDc2YmJkZiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RnJvZyBuIFNuYWlsPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF80YjA0NWU0NDNkMWE0OTQwYjNiMzk4MWY5MzIyM2IxNy5zZXRDb250ZW50KGh0bWxfMGM0MzdmYmY0YTZmNDM2YzhkYjg0MDk2ZGQ3NmJiZGYpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl9iZmE0NWZjYzAxMTc0OTNmOTRjMTA2M2VlYzFlZmU4NS5iaW5kUG9wdXAocG9wdXBfNGIwNDVlNDQzZDFhNDk0MGIzYjM5ODFmOTMyMjNiMTcpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfZTk2ODQxNmQ5ZmUxNDFiNmE1Nzk1Y2Y3NTFhZmI0MzEgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS45NDUwODkxLC04Ny42NjM2OTA0XSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDZiNjQwOTc2MTVjNDZkN2FmNjE0Nzg1YWRmYzNjYTQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMDU0ZjVjMjcxNmJkNGIxZWFiMmVlOGJlOWIzNTkxYzMgPSAkKCc8ZGl2IGlkPSJodG1sXzA1NGY1YzI3MTZiZDRiMWVhYjJlZThiZTliMzU5MWMzIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Dcm9zYnnigJlzIEtpdGNoZW48L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQ2YjY0MDk3NjE1YzQ2ZDdhZjYxNDc4NWFkZmMzY2E0LnNldENvbnRlbnQoaHRtbF8wNTRmNWMyNzE2YmQ0YjFlYWIyZWU4YmU5YjM1OTFjMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyX2U5Njg0MTZkOWZlMTQxYjZhNTc5NWNmNzUxYWZiNDMxLmJpbmRQb3B1cChwb3B1cF80NmI2NDA5NzYxNWM0NmQ3YWY2MTQ3ODVhZGZjM2NhNCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl8wYWQ4Y2U2NDM5NTA0ZjIwOTQzNDYyZmEwNGYwYWI5NyA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjkzMDA3NDMsLTg3LjcwNzAyODJdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8xZjU3NTk5YzdhZWI0NjcxODA4YjI3ZDRjMzA1ZGQ1ZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9kODY5ZDE5ZjhiMzk0NzhjODRjMTI3ODczMzA5YTNhYiA9ICQoJzxkaXYgaWQ9Imh0bWxfZDg2OWQxOWY4YjM5NDc4Yzg0YzEyNzg3MzMwOWEzYWIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxvbmdtYW4gJiBFYWdsZTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMWY1NzU5OWM3YWViNDY3MTgwOGIyN2Q0YzMwNWRkNWUuc2V0Q29udGVudChodG1sX2Q4NjlkMTlmOGIzOTQ3OGM4NGMxMjc4NzMzMDlhM2FiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfMGFkOGNlNjQzOTUwNGYyMDk0MzQ2MmZhMDRmMGFiOTcuYmluZFBvcHVwKHBvcHVwXzFmNTc1OTljN2FlYjQ2NzE4MDhiMjdkNGMzMDVkZDVlKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyX2M5MDA5ZGFhNzliYjQ1Njc5NzdmNDI2YmFkOWFkNTU1ID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuODkxMzAxNCwtODcuNjU1NjE5MV0sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzRlZmUxOWJiZDQ1ZTQ3ODc5NTUxMzgxMzUyZmVmY2QzID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2E1ZDc5YmQyYWNiMzQwNjY5ZTEwMTc1MDBlMmRlODMyID0gJCgnPGRpdiBpZD0iaHRtbF9hNWQ3OWJkMmFjYjM0MDY2OWUxMDE3NTAwZTJkZTgzMiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QmFyaTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNGVmZTE5YmJkNDVlNDc4Nzk1NTEzODEzNTJmZWZjZDMuc2V0Q29udGVudChodG1sX2E1ZDc5YmQyYWNiMzQwNjY5ZTEwMTc1MDBlMmRlODMyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfYzkwMDlkYWE3OWJiNDU2Nzk3N2Y0MjZiYWQ5YWQ1NTUuYmluZFBvcHVwKHBvcHVwXzRlZmUxOWJiZDQ1ZTQ3ODc5NTUxMzgxMzUyZmVmY2QzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyX2IyOTBmYjM3OGFkYTRkOGE5MDM0YThiMGEyMjE2ZThkID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuODY3Nzg5LC04Ny42NDE5MjczXSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYjlmNjUzZjRjMWMzNDgxYmE3MGQ5YTdjZTQ4NWNjYjUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNjZmMjEzYjQ1NDFiNDIwYjk4YTFlZTYzOGEwYmRmNzIgPSAkKCc8ZGl2IGlkPSJodG1sXzY2ZjIxM2I0NTQxYjQyMGI5OGExZWU2MzhhMGJkZjcyIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5NYW5ueeKAmXM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2I5ZjY1M2Y0YzFjMzQ4MWJhNzBkOWE3Y2U0ODVjY2I1LnNldENvbnRlbnQoaHRtbF82NmYyMTNiNDU0MWI0MjBiOThhMWVlNjM4YTBiZGY3Mik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyX2IyOTBmYjM3OGFkYTRkOGE5MDM0YThiMGEyMjE2ZThkLmJpbmRQb3B1cChwb3B1cF9iOWY2NTNmNGMxYzM0ODFiYTcwZDlhN2NlNDg1Y2NiNSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl9iNDI0ZDk4MjY5MWI0MWNjOWMzNzg4Yjk0YmQyYTI5MSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjg4NTE5MzMsLTg3LjYxODQ2NTJdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9jODMyY2JiNTk4YzU0ODBhOGVhYjFkNjYyYjBmNjBmZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83Y2UzNGE0OWM1YmU0YTU2YWNkZTg4YjkxOGZiOWMzZSA9ICQoJzxkaXYgaWQ9Imh0bWxfN2NlMzRhNDljNWJlNGE1NmFjZGU4OGI5MThmYjljM2UiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkVnZ3nigJlzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jODMyY2JiNTk4YzU0ODBhOGVhYjFkNjYyYjBmNjBmZS5zZXRDb250ZW50KGh0bWxfN2NlMzRhNDljNWJlNGE1NmFjZGU4OGI5MThmYjljM2UpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl9iNDI0ZDk4MjY5MWI0MWNjOWMzNzg4Yjk0YmQyYTI5MS5iaW5kUG9wdXAocG9wdXBfYzgzMmNiYjU5OGM1NDgwYThlYWIxZDY2MmIwZjYwZmUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfNDc2NWUxOGE5MjJlNDY4OWE0NjM1M2IyMzU3YjUyMTcgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS45MDgwNTUsLTg3LjYzNDMxMTddLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mZTdjZjExZjRjNzA0NmYzOTU4NjRhZTE4NDE3N2IyYyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9mYjVhMWE5ZmQ3YjQ0NTRkYjVkYWU3MjI2MjIwYWNkZCA9ICQoJzxkaXYgaWQ9Imh0bWxfZmI1YTFhOWZkN2I0NDU0ZGI1ZGFlNzIyNjIyMGFjZGQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk9sZCBKZXJ1c2FsZW08L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2ZlN2NmMTFmNGM3MDQ2ZjM5NTg2NGFlMTg0MTc3YjJjLnNldENvbnRlbnQoaHRtbF9mYjVhMWE5ZmQ3YjQ0NTRkYjVkYWU3MjI2MjIwYWNkZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzQ3NjVlMThhOTIyZTQ2ODlhNDYzNTNiMjM1N2I1MjE3LmJpbmRQb3B1cChwb3B1cF9mZTdjZjExZjRjNzA0NmYzOTU4NjRhZTE4NDE3N2IyYyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl8zZmUyMDQyYzdhYzA0NDNiYTU2YjQ2ZGJkNGJlMDA1ZSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjkxMzY3NjksLTg3LjY3NzE4OTRdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9mNmE1NzNmOWU4MmE0NDExYTYxZDgxYjJjNTVkMDUwOSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zYTFmOGFiNmU2NjI0NzczYmMyNTA3ZWY0YWE2NDkxZiA9ICQoJzxkaXYgaWQ9Imh0bWxfM2ExZjhhYjZlNjYyNDc3M2JjMjUwN2VmNGFhNjQ5MWYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk1pbmR54oCZcyBIb3RDaG9jb2xhdGU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2Y2YTU3M2Y5ZTgyYTQ0MTFhNjFkODFiMmM1NWQwNTA5LnNldENvbnRlbnQoaHRtbF8zYTFmOGFiNmU2NjI0NzczYmMyNTA3ZWY0YWE2NDkxZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzNmZTIwNDJjN2FjMDQ0M2JhNTZiNDZkYmQ0YmUwMDVlLmJpbmRQb3B1cChwb3B1cF9mNmE1NzNmOWU4MmE0NDExYTYxZDgxYjJjNTVkMDUwOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl8yNmFjODNiYWZhZTU0ODVhOTI2Y2Y5YWQzODJlMmEwMCA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjk1MzcwNTQsLTg3LjcwODQ1MDJdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lMDRmZGEwNjBjMzQ0MWZhODE2YTY1ZTZhNGM4NjYzMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8yZDc2YTcyZmE3YTU0ZmQzOTBmYzUzNzU2ZDAwZTEzMiA9ICQoJzxkaXYgaWQ9Imh0bWxfMmQ3NmE3MmZhN2E1NGZkMzkwZmM1Mzc1NmQwMGUxMzIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPk9sZ2HigJlzIERlbGljYXRlc3NlbjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTA0ZmRhMDYwYzM0NDFmYTgxNmE2NWU2YTRjODY2MzMuc2V0Q29udGVudChodG1sXzJkNzZhNzJmYTdhNTRmZDM5MGZjNTM3NTZkMDBlMTMyKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfMjZhYzgzYmFmYWU1NDg1YTkyNmNmOWFkMzgyZTJhMDAuYmluZFBvcHVwKHBvcHVwX2UwNGZkYTA2MGMzNDQxZmE4MTZhNjVlNmE0Yzg2NjMzKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyXzY2NmJlZjhlMzBjODQ2NjViOTQ4MjdkNTc2Njc2NzdjID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuOTA1ODEsLTg3LjY3MjQ4M10sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzA2YmQ3YTFjMWQ0OTQ4Y2RiNGEwNGJlZDlkMTY0OWFkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2VkNjEyYjVhODE2YTQxNWZiMDIzMmI5OGUxNTk4ZjU5ID0gJCgnPGRpdiBpZD0iaHRtbF9lZDYxMmI1YTgxNmE0MTVmYjAyMzJiOThlMTU5OGY1OSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+RGF3YWxpIE1lZGl0ZXJyYW5lYW4gS2l0Y2hlbjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfMDZiZDdhMWMxZDQ5NDhjZGI0YTA0YmVkOWQxNjQ5YWQuc2V0Q29udGVudChodG1sX2VkNjEyYjVhODE2YTQxNWZiMDIzMmI5OGUxNTk4ZjU5KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfNjY2YmVmOGUzMGM4NDY2NWI5NDgyN2Q1NzY2NzY3N2MuYmluZFBvcHVwKHBvcHVwXzA2YmQ3YTFjMWQ0OTQ4Y2RiNGEwNGJlZDlkMTY0OWFkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyX2RlZWM3NjU0ZmIxNTRlYzY4MTY2ZGEzMzk2ZGRmYzUyID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuOTc5NDQ5NSwtODcuNjY3OTc0N10sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzEyNmRkM2U2NjlkNTQ3OGViNDVmNzE3MzczOTRkYzRkID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2I4OTgyNmViNzhjOTRiOWRiNTQyZDVkZTMwYWJhMTk4ID0gJCgnPGRpdiBpZD0iaHRtbF9iODk4MjZlYjc4Yzk0YjlkYjU0MmQ1ZGUzMGFiYTE5OCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+QmlnIEpvbmVzPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8xMjZkZDNlNjY5ZDU0NzhlYjQ1ZjcxNzM3Mzk0ZGM0ZC5zZXRDb250ZW50KGh0bWxfYjg5ODI2ZWI3OGM5NGI5ZGI1NDJkNWRlMzBhYmExOTgpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl9kZWVjNzY1NGZiMTU0ZWM2ODE2NmRhMzM5NmRkZmM1Mi5iaW5kUG9wdXAocG9wdXBfMTI2ZGQzZTY2OWQ1NDc4ZWI0NWY3MTczNzM5NGRjNGQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfNWY4ZTRmM2NhYjMyNDgwYzhiYzNhNDllNzFjY2U4YzcgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS45NTQxNjIsLTg3LjcwMjcxMDhdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83MzNmMzc4MWU4ZmI0YzQ5OWU4NDRhYjQwZjRmYjNhZSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF81MDc4OGZlZDVlMDA0ZTk3YWM4YmNlMmVkYjE0ODFiYyA9ICQoJzxkaXYgaWQ9Imh0bWxfNTA3ODhmZWQ1ZTAwNGU5N2FjOGJjZTJlZGIxNDgxYmMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkxhIFBhbmU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzczM2YzNzgxZThmYjRjNDk5ZTg0NGFiNDBmNGZiM2FlLnNldENvbnRlbnQoaHRtbF81MDc4OGZlZDVlMDA0ZTk3YWM4YmNlMmVkYjE0ODFiYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzVmOGU0ZjNjYWIzMjQ4MGM4YmMzYTQ5ZTcxY2NlOGM3LmJpbmRQb3B1cChwb3B1cF83MzNmMzc4MWU4ZmI0YzQ5OWU4NDRhYjQwZjRmYjNhZSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl9lMWY4NWQ4MWY3ZjI0ZDc3OGIxYTc1ZTY5YzVmY2MyMyA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjkwNTgxLC04Ny42NzI0ODNdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9hOWE4MzI0MjM2ZDA0NjViOWNjOWE2NWE3NzlmMzllNiA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF83YzI2MjYzODBiNjg0NWI1YjlhYzhjOGY5N2EzYjRmNCA9ICQoJzxkaXYgaWQ9Imh0bWxfN2MyNjI2MzgwYjY4NDViNWI5YWM4YzhmOTdhM2I0ZjQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBhc3RvcmFsPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9hOWE4MzI0MjM2ZDA0NjViOWNjOWE2NWE3NzlmMzllNi5zZXRDb250ZW50KGh0bWxfN2MyNjI2MzgwYjY4NDViNWI5YWM4YzhmOTdhM2I0ZjQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl9lMWY4NWQ4MWY3ZjI0ZDc3OGIxYTc1ZTY5YzVmY2MyMy5iaW5kUG9wdXAocG9wdXBfYTlhODMyNDIzNmQwNDY1YjljYzlhNjVhNzc5ZjM5ZTYpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfZTg3OTFiZDA4MGRhNGU4ZjljYmQ1YzQ1MDExNTEwYWYgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0Mi4xNTY3MDczLC04Ny44MDM2MzQ1XSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfN2M3YjA2NmYzNGM0NGI5MWFmMzBiYTA4MjBjYjZjOGIgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYjdiM2RjMDhiYjM0NGZjYTgxNzRlZWFlNjlmZDQ5OGYgPSAkKCc8ZGl2IGlkPSJodG1sX2I3YjNkYzA4YmIzNDRmY2E4MTc0ZWVhZTY5ZmQ0OThmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5NYXjigJlzIERlbGk8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzdjN2IwNjZmMzRjNDRiOTFhZjMwYmEwODIwY2I2YzhiLnNldENvbnRlbnQoaHRtbF9iN2IzZGMwOGJiMzQ0ZmNhODE3NGVlYWU2OWZkNDk4Zik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyX2U4NzkxYmQwODBkYTRlOGY5Y2JkNWM0NTAxMTUxMGFmLmJpbmRQb3B1cChwb3B1cF83YzdiMDY2ZjM0YzQ0YjkxYWYzMGJhMDgyMGNiNmM4Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl82NWUxZmVhYzg4NmQ0NTI2OWZkMjZhZDVhMzUwYzJhOCA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjkwNTgxLC04Ny42NzI0ODNdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lY2RkNjAxNDA2MDA0MjZlYjUyZTE4YTBhMWNjMjRmYSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF80ZmViZmViODc5ZDk0YmFmOThmMTJmZjg4Yzg3YzNiZiA9ICQoJzxkaXYgaWQ9Imh0bWxfNGZlYmZlYjg3OWQ5NGJhZjk4ZjEyZmY4OGM4N2MzYmYiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkx1Y2t54oCZcyBTYW5kd2ljaCBDby48L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2VjZGQ2MDE0MDYwMDQyNmViNTJlMThhMGExY2MyNGZhLnNldENvbnRlbnQoaHRtbF80ZmViZmViODc5ZDk0YmFmOThmMTJmZjg4Yzg3YzNiZik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzY1ZTFmZWFjODg2ZDQ1MjY5ZmQyNmFkNWEzNTBjMmE4LmJpbmRQb3B1cChwb3B1cF9lY2RkNjAxNDA2MDA0MjZlYjUyZTE4YTBhMWNjMjRmYSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl9kMGUzNWIyMTgyOGQ0ODNmYjgxOGI1YjE2MmRkMmE0ZCA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjk2NTI5ODcsLTg3LjY3NTU3OTZdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83Zjg3ZjlkNWE4OTQ0OWJhYjk4Y2M5NDY3OTYwNTcwZCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82NzllNWI3ZGUwOTg0OTkxODM5NTMzYzBmYWZmMTZkYiA9ICQoJzxkaXYgaWQ9Imh0bWxfNjc5ZTViN2RlMDk4NDk5MTgzOTUzM2MwZmFmZjE2ZGIiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNpdHkgUHJvdmlzaW9uczwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfN2Y4N2Y5ZDVhODk0NDliYWI5OGNjOTQ2Nzk2MDU3MGQuc2V0Q29udGVudChodG1sXzY3OWU1YjdkZTA5ODQ5OTE4Mzk1MzNjMGZhZmYxNmRiKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfZDBlMzViMjE4MjhkNDgzZmI4MThiNWIxNjJkZDJhNGQuYmluZFBvcHVwKHBvcHVwXzdmODdmOWQ1YTg5NDQ5YmFiOThjYzk0Njc5NjA1NzBkKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyXzBkMmY5NDk2ZDFhMjQ3NzQ5NmMzYmU4ZGMwNTY5NmQ0ID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuOTAyNzIyLC04Ny42OTAyMDg2XSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfYzA3ZWIyMDk0Mzg0NGIyYjg4MWYyZGIxOTU3M2U4ZWMgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfYjZlMjUxNzA3ZWU1NDdiZWI5M2E3ZTkwYjIzN2ZhMTkgPSAkKCc8ZGl2IGlkPSJodG1sX2I2ZTI1MTcwN2VlNTQ3YmViOTNhN2U5MGIyMzdmYTE5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5QYXBh4oCZcyBDYWNoZSBTYWJyb3NvPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9jMDdlYjIwOTQzODQ0YjJiODgxZjJkYjE5NTczZThlYy5zZXRDb250ZW50KGh0bWxfYjZlMjUxNzA3ZWU1NDdiZWI5M2E3ZTkwYjIzN2ZhMTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl8wZDJmOTQ5NmQxYTI0Nzc0OTZjM2JlOGRjMDU2OTZkNC5iaW5kUG9wdXAocG9wdXBfYzA3ZWIyMDk0Mzg0NGIyYjg4MWYyZGIxOTU3M2U4ZWMpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfYzllZTcwYmM1NzFiNGZlZGFlMDkwOGViYTA5NjE4MWUgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS44ODkzOTgyLC04Ny42MzQ5MTE5XSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfODEzOTgyNTgyMWE3NGQzY2JhZTc1Njg0MzZlNzk4MjYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNGU0MDgyYTVhMzVmNDc4YmFjMTZiNjgxYmExZWNmNTEgPSAkKCc8ZGl2IGlkPSJodG1sXzRlNDA4MmE1YTM1ZjQ3OGJhYzE2YjY4MWJhMWVjZjUxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5CYXZldHRl4oCZcyBCYXIgJiBCb2V1ZjwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfODEzOTgyNTgyMWE3NGQzY2JhZTc1Njg0MzZlNzk4MjYuc2V0Q29udGVudChodG1sXzRlNDA4MmE1YTM1ZjQ3OGJhYzE2YjY4MWJhMWVjZjUxKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfYzllZTcwYmM1NzFiNGZlZGFlMDkwOGViYTA5NjE4MWUuYmluZFBvcHVwKHBvcHVwXzgxMzk4MjU4MjFhNzRkM2NiYWU3NTY4NDM2ZTc5ODI2KTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyXzU1YWE0ODQ3ZGY2ZjQxZWFiY2IyZWM3ZWY4NGFiZmVmID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuOTA1ODEsLTg3LjY3MjQ4M10sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzk4Y2RkMDk5MjU5NTQxODViZjFkNWU3OTdiNzgxOWNhID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzY4MGNmNjJmZmQ0MDRmOWViNGE4NWI1ZDEwOGMxMGI3ID0gJCgnPGRpdiBpZD0iaHRtbF82ODBjZjYyZmZkNDA0ZjllYjRhODViNWQxMDhjMTBiNyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+SGFubmFo4oCZcyBCcmV0emVsPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF85OGNkZDA5OTI1OTU0MTg1YmYxZDVlNzk3Yjc4MTljYS5zZXRDb250ZW50KGh0bWxfNjgwY2Y2MmZmZDQwNGY5ZWI0YTg1YjVkMTA4YzEwYjcpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl81NWFhNDg0N2RmNmY0MWVhYmNiMmVjN2VmODRhYmZlZi5iaW5kUG9wdXAocG9wdXBfOThjZGQwOTkyNTk1NDE4NWJmMWQ1ZTc5N2I3ODE5Y2EpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfNzk4MjExYTI1ZDYzNDUxNGJjMmZiZTY3MTYyNDUwMzMgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS45MTA1MTk2LC04Ny42MzQzODA4XSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfMjA2MDhjNzM3ODcyNDU0Zjk3NmRjNTU3ZGRkYWFkYzUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTE3MjE3ZmUwNTQwNDhhMThiYzQxY2QxMTA4MjllOGYgPSAkKCc8ZGl2IGlkPSJodG1sX2UxNzIxN2ZlMDU0MDQ4YTE4YmM0MWNkMTEwODI5ZThmIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5MYSBGb3VybmV0dGU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzIwNjA4YzczNzg3MjQ1NGY5NzZkYzU1N2RkZGFhZGM1LnNldENvbnRlbnQoaHRtbF9lMTcyMTdmZTA1NDA0OGExOGJjNDFjZDExMDgyOWU4Zik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzc5ODIxMWEyNWQ2MzQ1MTRiYzJmYmU2NzE2MjQ1MDMzLmJpbmRQb3B1cChwb3B1cF8yMDYwOGM3Mzc4NzI0NTRmOTc2ZGM1NTdkZGRhYWRjNSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl9iZTcxMzkxMmU0ZTE0NmM2OGU3ZjI3NWY2YWYzN2E2MSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjg4OTYyMzEsLTg3LjY0NDg1NTJdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iYjg2Mjc4MzBlYjg0NTU4YWM3Y2UxMzQxMDU2ZWVjNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zMDRkYTcyZTUwYjI0NzFkOGJhNDk0NTM3ZGM3OWNiZSA9ICQoJzxkaXYgaWQ9Imh0bWxfMzA0ZGE3MmU1MGIyNDcxZDhiYTQ5NDUzN2RjNzljYmUiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBhcmFtb3VudCBSb29tPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9iYjg2Mjc4MzBlYjg0NTU4YWM3Y2UxMzQxMDU2ZWVjNS5zZXRDb250ZW50KGh0bWxfMzA0ZGE3MmU1MGIyNDcxZDhiYTQ5NDUzN2RjNzljYmUpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl9iZTcxMzkxMmU0ZTE0NmM2OGU3ZjI3NWY2YWYzN2E2MS5iaW5kUG9wdXAocG9wdXBfYmI4NjI3ODMwZWI4NDU1OGFjN2NlMTM0MTA1NmVlYzUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfMmYwMDQxNDJiNjQ1NGNiMGEwMjUyMDUyMjIxYThhMDkgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS45MTUwNSwtODcuNjc3NzgxNF0sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzViMTM4MjY0MmNiNzQ5M2U4MDNlNzM5YzRiMzRlYjM5ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzNjNWU3MGI2NjNhNTQ1MTRhN2I1M2Y0ZGQ1NGYwY2M5ID0gJCgnPGRpdiBpZD0iaHRtbF8zYzVlNzBiNjYzYTU0NTE0YTdiNTNmNGRkNTRmMGNjOSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TWVsdCBTYW5kd2ljaCBTaG9wcGU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzViMTM4MjY0MmNiNzQ5M2U4MDNlNzM5YzRiMzRlYjM5LnNldENvbnRlbnQoaHRtbF8zYzVlNzBiNjYzYTU0NTE0YTdiNTNmNGRkNTRmMGNjOSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzJmMDA0MTQyYjY0NTRjYjBhMDI1MjA1MjIyMWE4YTA5LmJpbmRQb3B1cChwb3B1cF81YjEzODI2NDJjYjc0OTNlODAzZTczOWM0YjM0ZWIzOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl81ZWNjNDUwM2RmZTE0ZDY0OTVlNDM1NDE2NWRmYTBlNCA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjkyMTgzMzcsLTg3LjY1OTIwMTVdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9kZTI1YmQ0MzJkODk0OTA4OWM5MTBlZGVhZWEzNzcwMyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85YjlkNDBiZGM1ZDU0ZTY1OGY3MmEwNmI0MTVkNzk1MyA9ICQoJzxkaXYgaWQ9Imh0bWxfOWI5ZDQwYmRjNWQ1NGU2NThmNzJhMDZiNDE1ZDc5NTMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZsb3Jpb2xlIENhZmUgJiBCYWtlcnk8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2RlMjViZDQzMmQ4OTQ5MDg5YzkxMGVkZWFlYTM3NzAzLnNldENvbnRlbnQoaHRtbF85YjlkNDBiZGM1ZDU0ZTY1OGY3MmEwNmI0MTVkNzk1Myk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzVlY2M0NTAzZGZlMTRkNjQ5NWU0MzU0MTY1ZGZhMGU0LmJpbmRQb3B1cChwb3B1cF9kZTI1YmQ0MzJkODk0OTA4OWM5MTBlZGVhZWEzNzcwMyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl81YmMzMWZjNjA5Nzc0M2Q5YjI5MzEyNDc4Mzg2Y2FjOSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjk3OTcwMzMsLTg3LjY2OTM1MTNdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lMDg1OGI5M2VmMWI0NWJhYWQ3NDlmNDRiNjc2NzY3YSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9hMzI0YzNhNTM0MDM0OGVlYmY5NDlmOWJhZTA1OTAxNyA9ICQoJzxkaXYgaWQ9Imh0bWxfYTMyNGMzYTUzNDAzNDhlZWJmOTQ5ZjliYWUwNTkwMTciIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkZpcnN0IFNsaWNlIFBpZSBDYWbDqTwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZTA4NThiOTNlZjFiNDViYWFkNzQ5ZjQ0YjY3Njc2N2Euc2V0Q29udGVudChodG1sX2EzMjRjM2E1MzQwMzQ4ZWViZjk0OWY5YmFlMDU5MDE3KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfNWJjMzFmYzYwOTc3NDNkOWIyOTMxMjQ3ODM4NmNhYzkuYmluZFBvcHVwKHBvcHVwX2UwODU4YjkzZWYxYjQ1YmFhZDc0OWY0NGI2NzY3NjdhKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyXzdkZjM2MDZkYjUyMTQxMDE5YTI4YTM5ZGVhN2NlMzhkID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuOTYxNzEyMiwtODcuNjc1ODE2Ml0sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwX2YzOTZhYWZlZmRmOTRiNTg5MTk2NmIyNDFmOWQ1OWIwID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2JmNDVkNWZhM2VkMzQ5ZDJhMDYzODYyOGUwN2E3OGFjID0gJCgnPGRpdiBpZD0iaHRtbF9iZjQ1ZDVmYTNlZDM0OWQyYTA2Mzg2MjhlMDdhNzhhYyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+VHJvcXVldDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfZjM5NmFhZmVmZGY5NGI1ODkxOTY2YjI0MWY5ZDU5YjAuc2V0Q29udGVudChodG1sX2JmNDVkNWZhM2VkMzQ5ZDJhMDYzODYyOGUwN2E3OGFjKTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfN2RmMzYwNmRiNTIxNDEwMTlhMjhhMzlkZWE3Y2UzOGQuYmluZFBvcHVwKHBvcHVwX2YzOTZhYWZlZmRmOTRiNTg5MTk2NmIyNDFmOWQ1OWIwKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyX2YwMzcxNjcwZWJkMzRiMjY4YzJkYzlmOWU1ZjE3NDhkID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuODkyOTYxMiwtODcuNjI3ODIxNF0sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzU0YzI4YzBmYTVhYTQ1MGU4Y2U3NmZkY2M3NjQ4YzJjID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzMwNzRlYjQ2MDFjMTQ0MTg5NjhjOTNjYTgwOTliZjU4ID0gJCgnPGRpdiBpZD0iaHRtbF8zMDc0ZWI0NjAxYzE0NDE4OTY4YzkzY2E4MDk5YmY1OCIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+R3JhaGFtd2ljaDwvZGl2PicpWzBdOwogICAgICAgICAgICAgICAgcG9wdXBfNTRjMjhjMGZhNWFhNDUwZThjZTc2ZmRjYzc2NDhjMmMuc2V0Q29udGVudChodG1sXzMwNzRlYjQ2MDFjMTQ0MTg5NjhjOTNjYTgwOTliZjU4KTsKICAgICAgICAgICAgCgogICAgICAgICAgICBtYXJrZXJfZjAzNzE2NzBlYmQzNGIyNjhjMmRjOWY5ZTVmMTc0OGQuYmluZFBvcHVwKHBvcHVwXzU0YzI4YzBmYTVhYTQ1MGU4Y2U3NmZkY2M3NjQ4YzJjKTsKCiAgICAgICAgICAgIAogICAgICAgIAogICAgCgogICAgICAgICAgICB2YXIgbWFya2VyXzZlNGQ5ZDViY2M5YTRjZjFiOWQ0NThlYWFiZTcxY2M5ID0gTC5tYXJrZXIoCiAgICAgICAgICAgICAgICBbNDEuOTA1ODEsLTg3LjY3MjQ4M10sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzE3YmJkMTg3YzI4YTQ2ODZiMDA4NDdkN2UxYzQxMDZmID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sXzlhMmNmZWRiNDFiYzRhZTE4YzQ0M2EzMTNjMDYxOGU2ID0gJCgnPGRpdiBpZD0iaHRtbF85YTJjZmVkYjQxYmM0YWUxOGM0NDNhMzEzYzA2MThlNiIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+U2FpZ29uIFNpc3RlcnM8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzE3YmJkMTg3YzI4YTQ2ODZiMDA4NDdkN2UxYzQxMDZmLnNldENvbnRlbnQoaHRtbF85YTJjZmVkYjQxYmM0YWUxOGM0NDNhMzEzYzA2MThlNik7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzZlNGQ5ZDViY2M5YTRjZjFiOWQ0NThlYWFiZTcxY2M5LmJpbmRQb3B1cChwb3B1cF8xN2JiZDE4N2MyOGE0Njg2YjAwODQ3ZDdlMWM0MTA2Zik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl8xNjE5ZDZmZGM5ZDM0ZTMwYThkMmZjNWE4ZmU3NmU2YSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjkwNDc4MTcsLTg3LjkzOTY3NzVdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9iMmZhMTU4YmJiMTU0ZGRkOGRjMTExNzlhZjVmMTdhMCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82N2I1ZGEyNDE5Njg0Y2IyOWRmZjM3M2IwMTM2ZTNmYyA9ICQoJzxkaXYgaWQ9Imh0bWxfNjdiNWRhMjQxOTY4NGNiMjlkZmYzNzNiMDEzNmUzZmMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlJvc2FsaWHigJlzIERlbGk8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwX2IyZmExNThiYmIxNTRkZGQ4ZGMxMTE3OWFmNWYxN2EwLnNldENvbnRlbnQoaHRtbF82N2I1ZGEyNDE5Njg0Y2IyOWRmZjM3M2IwMTM2ZTNmYyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzE2MTlkNmZkYzlkMzRlMzBhOGQyZmM1YThmZTc2ZTZhLmJpbmRQb3B1cChwb3B1cF9iMmZhMTU4YmJiMTU0ZGRkOGRjMTExNzlhZjVmMTdhMCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl80ZDcxMmEzNGJkOGM0MWRlYTY3ODE1Mzk2Y2RmMjYyNyA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjc5MTI2NzUsLTg3LjU5Mzg4MzVdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF83ZjRhYTVlYjdmZGY0OTYyOGI5ZmExZjdmMzU3YWUwNCA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF82NDUwYWQ4MTJkNDI0NTMyOTRlZmJjNjRkNDRhODE0ZCA9ICQoJzxkaXYgaWQ9Imh0bWxfNjQ1MGFkODEyZDQyNDUzMjk0ZWZiYzY0ZDQ0YTgxNGQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlomSCBNYXJrZXRDYWZlPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83ZjRhYTVlYjdmZGY0OTYyOGI5ZmExZjdmMzU3YWUwNC5zZXRDb250ZW50KGh0bWxfNjQ1MGFkODEyZDQyNDUzMjk0ZWZiYzY0ZDQ0YTgxNGQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl80ZDcxMmEzNGJkOGM0MWRlYTY3ODE1Mzk2Y2RmMjYyNy5iaW5kUG9wdXAocG9wdXBfN2Y0YWE1ZWI3ZmRmNDk2MjhiOWZhMWY3ZjM1N2FlMDQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfNTE3YTI5NGQ5MjViNGM2MTk3YjAxNzhlOWY1ZTI2MzUgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS44NzEzNDcsLTg4LjE4MTg2N10sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzM5MjNhYjgwM2ZmNTRjZmFiM2I1NTkzMDQwZDJjNzc4ID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Y0MmNlYjU4ZDY5MDRhNWM4MjA1NDAxYTY2OGYzM2E1ID0gJCgnPGRpdiBpZD0iaHRtbF9mNDJjZWI1OGQ2OTA0YTVjODIwNTQwMWE2NjhmMzNhNSIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+TWFya2V0IEhvdXNlIG9uIHRoZSBTcXVhcmU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzM5MjNhYjgwM2ZmNTRjZmFiM2I1NTkzMDQwZDJjNzc4LnNldENvbnRlbnQoaHRtbF9mNDJjZWI1OGQ2OTA0YTVjODIwNTQwMWE2NjhmMzNhNSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzUxN2EyOTRkOTI1YjRjNjE5N2IwMTc4ZTlmNWUyNjM1LmJpbmRQb3B1cChwb3B1cF8zOTIzYWI4MDNmZjU0Y2ZhYjNiNTU5MzA0MGQyYzc3OCk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl84N2M1NTVkMTk1M2I0YWQwOWI1OWJmZDdkNDAxYzhhZCA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjkxNTMzNjgsLTg3LjYzNDYxNjFdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8zNzA0NGJlYTQ0OWI0MTk0YjdkNThkY2YyZDJmMDk1YyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF8zNTQ1YWUzMGQ5MmM0ZjJiOTZjMTAyZTU2MjE1ODcwZCA9ICQoJzxkaXYgaWQ9Imh0bWxfMzU0NWFlMzBkOTJjNGYyYjk2YzEwMmU1NjIxNTg3MGQiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkVsYWluZeKAmXMgQ29mZmVlIENhbGw8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzM3MDQ0YmVhNDQ5YjQxOTRiN2Q1OGRjZjJkMmYwOTVjLnNldENvbnRlbnQoaHRtbF8zNTQ1YWUzMGQ5MmM0ZjJiOTZjMTAyZTU2MjE1ODcwZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzg3YzU1NWQxOTUzYjRhZDA5YjU5YmZkN2Q0MDFjOGFkLmJpbmRQb3B1cChwb3B1cF8zNzA0NGJlYTQ0OWI0MTk0YjdkNThkY2YyZDJmMDk1Yyk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl80Yzg3NGZlYzMxZDI0ZTg5OTFlNWZmZjE0MWY0ZDRlZSA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjg4NjQzMywtODcuODAyMjE4XSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfNDA4ZGJkN2U2ZTEyNDg2OThkZjU5NDcwNDRkZGJjMjkgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfMDA0OGZmMmRhZmE2NDdiNGFmM2E3ZDhmODJlYmIzYzEgPSAkKCc8ZGl2IGlkPSJodG1sXzAwNDhmZjJkYWZhNjQ3YjRhZjNhN2Q4ZjgyZWJiM2MxIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5NYXJpb24gU3RyZWV0IENoZWVzZSBNYXJrZXQ8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzQwOGRiZDdlNmUxMjQ4Njk4ZGY1OTQ3MDQ0ZGRiYzI5LnNldENvbnRlbnQoaHRtbF8wMDQ4ZmYyZGFmYTY0N2I0YWYzYTdkOGY4MmViYjNjMSk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzRjODc0ZmVjMzFkMjRlODk5MWU1ZmZmMTQxZjRkNGVlLmJpbmRQb3B1cChwb3B1cF80MDhkYmQ3ZTZlMTI0ODY5OGRmNTk0NzA0NGRkYmMyOSk7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl84ZTFjYzVhYzI4ZjI0ODZkYTM2ZDFlMGFjY2RhMThiMCA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjg3NTgwMzcsLTg3LjYyNjM2Nl0sCiAgICAgICAgICAgICAgICB7CiAgICAgICAgICAgICAgICAgICAgaWNvbjogbmV3IEwuSWNvbi5EZWZhdWx0KCkKICAgICAgICAgICAgICAgICAgICB9CiAgICAgICAgICAgICAgICApCiAgICAgICAgICAgICAgICAuYWRkVG8obWFwXzdhNDNhNzY4YTIxMjQyZTFhNDUzMmY3MDUxNWEwZmM5KTsKICAgICAgICAgICAgCiAgICAKICAgICAgICAgICAgdmFyIHBvcHVwXzllZTRhZDc1NzJkNTQzODRhMGM5Mjk5ZDk1NWZkNTdiID0gTC5wb3B1cCh7bWF4V2lkdGg6ICczMDAnfSk7CgogICAgICAgICAgICAKICAgICAgICAgICAgICAgIHZhciBodG1sX2Y4YzBiOTUxNTNiNzQyNjlhYjM0YWYzNGY4MTUyMGMzID0gJCgnPGRpdiBpZD0iaHRtbF9mOGMwYjk1MTUzYjc0MjY5YWIzNGFmMzRmODE1MjBjMyIgc3R5bGU9IndpZHRoOiAxMDAuMCU7IGhlaWdodDogMTAwLjAlOyI+Q2FmZWNpdG88L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzllZTRhZDc1NzJkNTQzODRhMGM5Mjk5ZDk1NWZkNTdiLnNldENvbnRlbnQoaHRtbF9mOGMwYjk1MTUzYjc0MjY5YWIzNGFmMzRmODE1MjBjMyk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyXzhlMWNjNWFjMjhmMjQ4NmRhMzZkMWUwYWNjZGExOGIwLmJpbmRQb3B1cChwb3B1cF85ZWU0YWQ3NTcyZDU0Mzg0YTBjOTI5OWQ5NTVmZDU3Yik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl8zNTllYTVkMGVmYTE0MWE3ODk5NWM3ZmUzYTNlZDdhMCA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjg5NjA4MzgsLTg3LjY3NzgyODddLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF9lZDhjM2FmZjZjNGM0MTE5YjFkMWMyOWYwODExNjAzNSA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF9jMjdkNmZlMWMxODQ0OTNkOTg5MDFiOGIzMjg5MWRhMCA9ICQoJzxkaXYgaWQ9Imh0bWxfYzI3ZDZmZTFjMTg0NDkzZDk4OTAxYjhiMzI4OTFkYTAiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPkNoaWNrcGVhPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9lZDhjM2FmZjZjNGM0MTE5YjFkMWMyOWYwODExNjAzNS5zZXRDb250ZW50KGh0bWxfYzI3ZDZmZTFjMTg0NDkzZDk4OTAxYjhiMzI4OTFkYTApOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl8zNTllYTVkMGVmYTE0MWE3ODk5NWM3ZmUzYTNlZDdhMC5iaW5kUG9wdXAocG9wdXBfZWQ4YzNhZmY2YzRjNDExOWIxZDFjMjlmMDgxMTYwMzUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfMTc4YjJiMmRjZjc1NGRkYjk4MTliNzIzNTcxYWQyNWIgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS44OTg5NzQ5LC04Ny42MjczMDI0XSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfZDA4YjhiZWJmMDc5NGQ0MThjNDg5YThhMDdjMTg3OWUgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfZTI2YWU4NDZlM2I3NDUwYWJkNmQ3ZDc0ZDQxYzI5NTkgPSAkKCc8ZGl2IGlkPSJodG1sX2UyNmFlODQ2ZTNiNzQ1MGFiZDZkN2Q3NGQ0MWMyOTU5IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5UaGUgR29kZGVzcyBhbmQgR3JvY2VyPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF9kMDhiOGJlYmYwNzk0ZDQxOGM0ODlhOGEwN2MxODc5ZS5zZXRDb250ZW50KGh0bWxfZTI2YWU4NDZlM2I3NDUwYWJkNmQ3ZDc0ZDQxYzI5NTkpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl8xNzhiMmIyZGNmNzU0ZGRiOTgxOWI3MjM1NzFhZDI1Yi5iaW5kUG9wdXAocG9wdXBfZDA4YjhiZWJmMDc5NGQ0MThjNDg5YThhMDdjMTg3OWUpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfYTI2M2RkYmVjNTU4NDU1NzliNDU5MWZlNzY4NTVhZTAgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0Mi44NzkzNjQyLC03OC44NjU3NTg3XSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfN2Y4YmY0YzkyYmJhNGY1N2IwNWUxNTgzZTNlYzc0NDQgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfN2RlMDAxZDNjYzQ3NGVlNTkzMmM1ODBjNWMzMWZjNTQgPSAkKCc8ZGl2IGlkPSJodG1sXzdkZTAwMWQzY2M0NzRlZTU5MzJjNTgwYzVjMzFmYzU0IiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5aZW53aWNoPC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF83ZjhiZjRjOTJiYmE0ZjU3YjA1ZTE1ODNlM2VjNzQ0NC5zZXRDb250ZW50KGh0bWxfN2RlMDAxZDNjYzQ3NGVlNTkzMmM1ODBjNWMzMWZjNTQpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl9hMjYzZGRiZWM1NTg0NTU3OWI0NTkxZmU3Njg1NWFlMC5iaW5kUG9wdXAocG9wdXBfN2Y4YmY0YzkyYmJhNGY1N2IwNWUxNTgzZTNlYzc0NDQpOwoKICAgICAgICAgICAgCiAgICAgICAgCiAgICAKCiAgICAgICAgICAgIHZhciBtYXJrZXJfZjVhZDIwMDQ2NTIyNDM3ODk2N2I2N2Q0OGY3YjQ4NTUgPSBMLm1hcmtlcigKICAgICAgICAgICAgICAgIFs0MS44ODMwNDc5LC04Ny42MjU1MDkyXSwKICAgICAgICAgICAgICAgIHsKICAgICAgICAgICAgICAgICAgICBpY29uOiBuZXcgTC5JY29uLkRlZmF1bHQoKQogICAgICAgICAgICAgICAgICAgIH0KICAgICAgICAgICAgICAgICkKICAgICAgICAgICAgICAgIC5hZGRUbyhtYXBfN2E0M2E3NjhhMjEyNDJlMWE0NTMyZjcwNTE1YTBmYzkpOwogICAgICAgICAgICAKICAgIAogICAgICAgICAgICB2YXIgcG9wdXBfOWI2Y2RhMDdhYWJjNDQwYjk1ZTVlYTYwOWIwZGI1YTYgPSBMLnBvcHVwKHttYXhXaWR0aDogJzMwMCd9KTsKCiAgICAgICAgICAgIAogICAgICAgICAgICAgICAgdmFyIGh0bWxfNGZjNGVhYTdmZDA1NGZjMDhhNDE2OWY3NGE1MDQ1YmQgPSAkKCc8ZGl2IGlkPSJodG1sXzRmYzRlYWE3ZmQwNTRmYzA4YTQxNjlmNzRhNTA0NWJkIiBzdHlsZT0id2lkdGg6IDEwMC4wJTsgaGVpZ2h0OiAxMDAuMCU7Ij5Ub25pIFBhdGlzc2VyaWU8L2Rpdj4nKVswXTsKICAgICAgICAgICAgICAgIHBvcHVwXzliNmNkYTA3YWFiYzQ0MGI5NWU1ZWE2MDliMGRiNWE2LnNldENvbnRlbnQoaHRtbF80ZmM0ZWFhN2ZkMDU0ZmMwOGE0MTY5Zjc0YTUwNDViZCk7CiAgICAgICAgICAgIAoKICAgICAgICAgICAgbWFya2VyX2Y1YWQyMDA0NjUyMjQzNzg5NjdiNjdkNDhmN2I0ODU1LmJpbmRQb3B1cChwb3B1cF85YjZjZGEwN2FhYmM0NDBiOTVlNWVhNjA5YjBkYjVhNik7CgogICAgICAgICAgICAKICAgICAgICAKICAgIAoKICAgICAgICAgICAgdmFyIG1hcmtlcl8wM2I4YWM3ZTAzNWI0OTY5ODRmNzc4YThhYWViMzZlNiA9IEwubWFya2VyKAogICAgICAgICAgICAgICAgWzQxLjk0MzEyODQsLTg3LjY0NDY5ODJdLAogICAgICAgICAgICAgICAgewogICAgICAgICAgICAgICAgICAgIGljb246IG5ldyBMLkljb24uRGVmYXVsdCgpCiAgICAgICAgICAgICAgICAgICAgfQogICAgICAgICAgICAgICAgKQogICAgICAgICAgICAgICAgLmFkZFRvKG1hcF83YTQzYTc2OGEyMTI0MmUxYTQ1MzJmNzA1MTVhMGZjOSk7CiAgICAgICAgICAgIAogICAgCiAgICAgICAgICAgIHZhciBwb3B1cF8wZDg0MDBlY2U4Yzg0ZTQwYjY2OGFiMjNhY2Q2YzU5YyA9IEwucG9wdXAoe21heFdpZHRoOiAnMzAwJ30pOwoKICAgICAgICAgICAgCiAgICAgICAgICAgICAgICB2YXIgaHRtbF85NzBkZTk0NThhYTE0NTA3YmExMzZjNzFhMzdmZTU4MyA9ICQoJzxkaXYgaWQ9Imh0bWxfOTcwZGU5NDU4YWExNDUwN2JhMTM2YzcxYTM3ZmU1ODMiIHN0eWxlPSJ3aWR0aDogMTAwLjAlOyBoZWlnaHQ6IDEwMC4wJTsiPlBob2ViZeKAmXMgQmFrZXJ5PC9kaXY+JylbMF07CiAgICAgICAgICAgICAgICBwb3B1cF8wZDg0MDBlY2U4Yzg0ZTQwYjY2OGFiMjNhY2Q2YzU5Yy5zZXRDb250ZW50KGh0bWxfOTcwZGU5NDU4YWExNDUwN2JhMTM2YzcxYTM3ZmU1ODMpOwogICAgICAgICAgICAKCiAgICAgICAgICAgIG1hcmtlcl8wM2I4YWM3ZTAzNWI0OTY5ODRmNzc4YThhYWViMzZlNi5iaW5kUG9wdXAocG9wdXBfMGQ4NDAwZWNlOGM4NGU0MGI2NjhhYjIzYWNkNmM1OWMpOwoKICAgICAgICAgICAgCiAgICAgICAgCjwvc2NyaXB0Pg==" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



---

# 5. 서울시 구별 맥도널드 매장 수

## 1) URL 창

* 매장찾기 :  http://www.mcdonalds.co.kr/www/kor/findus/district.do
* `서울특별시 강남구` 검색

```ini
http://www.mcdonalds.co.kr/www/kor/findus/district.do?pageIndex=2&sSearch_yn=Y&skey=2&skey1=&skey2=&skeyword=서울특별시%20강남구&skey4=&skey5=&skeyword2=&sflag1=&sflag2=&sflag3=&sflag4=&sflag5=&sflag6=&sflag=N
➞
http://www.mcdonalds.co.kr/www/kor/findus/district.do?pageIndex=2&sSearch_yn=Y&skey=2&skey1=&skey2=&skeyword=%EC%84%9C%EC%9A%B8%ED%8A%B9%EB%B3%84%EC%8B%9C%20%EA%B0%95%EB%82%A8%EA%B5%AC&skey4=&skey5=&skeyword2=&sflag1=&sflag2=&sflag3=&sflag4=&sflag5=&sflag6=&sflag=N
```
* http://www.mcdonalds.co.kr/www/kor/findus/district.do? pageIndex={ page }&sSearch_yn=Y&skey=2&skey1=&skey2=&skeyword={ location }


```python
import requests
from bs4 import BeautifulSoup
import pandas as pd
import numpy as np
```


```python
URI = 'http://www.mcdonalds.co.kr/www/kor/findus/district.do?pageIndex={page}&sSearch_yn=Y&skey=2&skey1=&skey2=&skeyword={location}'
URL = URI.format(
    page=2,
    location='서울특별시 종로구'
)
response = requests.get(URL)
response.url
```




    'http://www.mcdonalds.co.kr/www/kor/findus/district.do?pageIndex=2&sSearch_yn=Y&skey=2&skey1=&skey2=&skeyword=%EC%84%9C%EC%9A%B8%ED%8A%B9%EB%B3%84%EC%8B%9C%20%EC%A2%85%EB%A1%9C%EA%B5%AC'




```python
soup = BeautifulSoup(response.text, 'lxml')
```

## 2) 매장 목록 부분 태그 + 빈 페이지 리스트
![png]({{ site.url }}/data/dataScience/03WebData/5-2-tag.png)

* 자치구 리스트 읽기


```python
# 자치구 리스트
df = pd.read_csv('./data_ouput/02.crime_in_Seoul_final.csv')
McDonald_store = pd.DataFrame( {'구별' : df['구별'] } )
```


```python
McDonald_store.head()
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
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>강남구</td>
    </tr>
    <tr>
      <th>1</th>
      <td>강동구</td>
    </tr>
    <tr>
      <th>2</th>
      <td>강북구</td>
    </tr>
    <tr>
      <th>3</th>
      <td>관악구</td>
    </tr>
    <tr>
      <th>4</th>
      <td>광진구</td>
    </tr>
  </tbody>
</table>
</div>



* 매장 목록 부분 태그


```python
tmp = soup.find_all( 'dl', 'clearFix' )
tmp[0].a.get_text()
```




    '  정동점'



* 빈 페이지 리스트


```python
URI = 'http://www.mcdonalds.co.kr/www/kor/findus/district.do?pageIndex={page}&sSearch_yn=Y&skey=2&skey1=&skey2=&skeyword={location}'
URL = URI.format(
    page=3,
    location='서울특별시 종로구'
)
response = requests.get(URL)
soup = BeautifulSoup(response.text, 'lxml')

tmp =  soup.find_all( 'dl', 'clearFix' )
tmp
```




    []




```python
df.index
```




    RangeIndex(start=0, stop=23, step=1)




```python
import time
from itertools import count

URI = 'http://www.mcdonalds.co.kr/www/kor/findus/district.do?pageIndex={page}&sSearch_yn=Y&skey=2&skey1=&skey2=&skeyword={location}'

Mc_store_num = []
Mc_count = 0

for n in df.index:
    time.sleep(0.5)
    loc = '서울특별시' + ' ' + McDonald_store['구별'][n]
    print('------------------------------------------------------------------------------------------')
    print(loc)
    print('------------------------------------------------------------------------------------------')

    for pg in count():
        time.sleep(0.5)
        URL = URI.format(
            page=pg+1,
            location=loc
        )
        response = requests.get(URL)
        soup = BeautifulSoup(response.text, 'lxml')
        tmp =  soup.find_all( 'dl', 'clearFix' )
        print(pg, ' ➡ ', len(tmp))

        Mc_count  += len(tmp)

        if len(tmp)==0:
            Mc_store_num.append( Mc_count )
            Mc_count = 0
            break
```

    ------------------------------------------------------------------------------------------
    서울특별시 강남구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  5
    2  ➡  2
    3  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 강동구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  2
    2  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 강북구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 관악구
    ------------------------------------------------------------------------------------------
    0  ➡  3
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 광진구
    ------------------------------------------------------------------------------------------
    0  ➡  2
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 구로구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 노원구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 도봉구
    ------------------------------------------------------------------------------------------
    0  ➡  2
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 동대문구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 동작구
    ------------------------------------------------------------------------------------------
    0  ➡  4
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 마포구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 서대문구
    ------------------------------------------------------------------------------------------
    0  ➡  4
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 서초구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  1
    2  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 성동구
    ------------------------------------------------------------------------------------------
    0  ➡  3
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 성북구
    ------------------------------------------------------------------------------------------
    0  ➡  4
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 송파구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 양천구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 영등포구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 용산구
    ------------------------------------------------------------------------------------------
    0  ➡  2
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 은평구
    ------------------------------------------------------------------------------------------
    0  ➡  4
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 종로구
    ------------------------------------------------------------------------------------------
    0  ➡  5
    1  ➡  2
    2  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 중구
    ------------------------------------------------------------------------------------------
    0  ➡  3
    1  ➡  0
    ------------------------------------------------------------------------------------------
    서울특별시 중랑구
    ------------------------------------------------------------------------------------------
    0  ➡  4
    1  ➡  0



```python
McDonald_store['맥도널드매장수'] = Mc_store_num
McDonald_store.head()
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
      <th>맥도널드매장수</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>강남구</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>강동구</td>
      <td>7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>강북구</td>
      <td>5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>관악구</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>광진구</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>




```python
McDonald_store.set_index('구별', inplace=True)

McDonald_store.tail()
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
      <th>맥도널드매장수</th>
    </tr>
    <tr>
      <th>구별</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>용산구</th>
      <td>2</td>
    </tr>
    <tr>
      <th>은평구</th>
      <td>4</td>
    </tr>
    <tr>
      <th>종로구</th>
      <td>7</td>
    </tr>
    <tr>
      <th>중구</th>
      <td>3</td>
    </tr>
    <tr>
      <th>중랑구</th>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>



---


# 6. 네이버 영화 평점 사이트

![png]({{ site.url }}/data/dataScience/03WebData/6-movie-naver.png)


## 1) 날짜별 네이버 영화 평점 추이

* 날짜 -  `pandas.date_range( 시작일, periods=기간, freq='D)`
* 기간 - `2017-5-1` ~ `2017-6-29`


```python
import requests
from bs4 import  BeautifulSoup
import pandas as pd
```


```python
url = 'http://movie.naver.com/movie/sdb/rank/rmovie.nhn?sel=cur&date=20170704'
res = requests.get(url)
page = res.text

soup = BeautifulSoup(page, 'lxml')
```


```python
# 영화 제목
soup.find_all('div', 'tit5')[0].a.text
```




    '사운드 오브 뮤직'




```python
# 영화 평점
soup.find_all('td', 'point')[0].text
```




    '9.36'




```python
movie_name = [ soup.find_all('div', 'tit5')[n].a.text for n in range(0, 49) ]
movie_name[:5]
```




    ['사운드 오브 뮤직', '킹 오브 프리즘 프라이드 더 히어로', '서서평, 천천히 평온하게', '첫 키스만 50번째', '우리들']




```python
movie_point = [ soup.find_all('td', 'point')[n].text  for n in range(0, 49) ]
movie_point[:5]
```




    ['9.36', '9.34', '9.23', '9.22', '9.19']




```python
# 기간
date = pd.date_range('2017-5-1', periods=60, freq='D')
date
```




    DatetimeIndex(['2017-05-01', '2017-05-02', '2017-05-03', '2017-05-04',
                   '2017-05-05', '2017-05-06', '2017-05-07', '2017-05-08',
                   '2017-05-09', '2017-05-10', '2017-05-11', '2017-05-12',
                   '2017-05-13', '2017-05-14', '2017-05-15', '2017-05-16',
                   '2017-05-17', '2017-05-18', '2017-05-19', '2017-05-20',
                   '2017-05-21', '2017-05-22', '2017-05-23', '2017-05-24',
                   '2017-05-25', '2017-05-26', '2017-05-27', '2017-05-28',
                   '2017-05-29', '2017-05-30', '2017-05-31', '2017-06-01',
                   '2017-06-02', '2017-06-03', '2017-06-04', '2017-06-05',
                   '2017-06-06', '2017-06-07', '2017-06-08', '2017-06-09',
                   '2017-06-10', '2017-06-11', '2017-06-12', '2017-06-13',
                   '2017-06-14', '2017-06-15', '2017-06-16', '2017-06-17',
                   '2017-06-18', '2017-06-19', '2017-06-20', '2017-06-21',
                   '2017-06-22', '2017-06-23', '2017-06-24', '2017-06-25',
                   '2017-06-26', '2017-06-27', '2017-06-28', '2017-06-29'],
                  dtype='datetime64[ns]', freq='D')




```python
date[0].strftime('%Y%m%d')
```




    '20170501'




```python
url_date =  'http://movie.naver.com/movie/sdb/rank/rmovie.nhn?sel=cur&date={u_date}'
url = url_date.format( u_date = date[0].strftime('%Y%m%d') )
url
```




    'http://movie.naver.com/movie/sdb/rank/rmovie.nhn?sel=cur&date=20170501'




```python
movie_date = []
movie_name = []
movie_point = []
url_date =  'http://movie.naver.com/movie/sdb/rank/rmovie.nhn?sel=cur&date={date}'

for day in date:   
    u_date = day.strftime('%Y%m%d')
    url = url_date.format(date=u_date)

    res = requests.get(url)
    html = res.text
    soup = BeautifulSoup(html, 'lxml')

    end = len( soup.find_all('td', 'point') )

    movie_date.extend( [day for n in range(0, end)] )
    movie_name.extend( [ soup.find_all('div', 'tit5')[n].a.text  for n in range(0, end)  ] )
    movie_point.extend( [ soup.find_all('td', 'point')[n].text  for n in range(0, end) ] )
```


```python
len(movie_date), len(movie_name), len(movie_point)
```




    (2793, 2793, 2793)




```python
movie = pd.DataFrame(
    {
    'date': movie_date,
     'name': movie_name,
     'point': movie_point
    }
)
movie.head()
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
      <th>date</th>
      <th>name</th>
      <th>point</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2017-05-01</td>
      <td>히든 피겨스</td>
      <td>9.38</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2017-05-01</td>
      <td>사운드 오브 뮤직</td>
      <td>9.36</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2017-05-01</td>
      <td>시네마 천국</td>
      <td>9.29</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2017-05-01</td>
      <td>미스 슬로운</td>
      <td>9.26</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2017-05-01</td>
      <td>잉여들의 히치하이킹</td>
      <td>9.25</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 데이터 유형 확인
movie.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 2793 entries, 0 to 2792
    Data columns (total 3 columns):
    date     2793 non-null datetime64[ns]
    name     2793 non-null object
    point    2793 non-null object
    dtypes: datetime64[ns](1), object(2)
    memory usage: 65.5+ KB



```python
# point를 float 형으로 변경
movie['point'] = movie['point'].astype(float)
movie.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 2793 entries, 0 to 2792
    Data columns (total 3 columns):
    date     2793 non-null datetime64[ns]
    name     2793 non-null object
    point    2793 non-null float64
    dtypes: datetime64[ns](1), float64(1), object(1)
    memory usage: 65.5+ KB



```python
import numpy as np

movie_unique = pd.pivot_table(
    movie,
    index=['name'],
    aggfunc=np.sum
)

movie_best = movie_unique.sort_values( by='point', ascending=False )
movie_best.head()
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
      <th>point</th>
    </tr>
    <tr>
      <th>name</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>댄서</th>
      <td>548.91</td>
    </tr>
    <tr>
      <th>서서평, 천천히 평온하게</th>
      <td>520.33</td>
    </tr>
    <tr>
      <th>오두막</th>
      <td>520.29</td>
    </tr>
    <tr>
      <th>라라랜드</th>
      <td>515.29</td>
    </tr>
    <tr>
      <th>보스 베이비</th>
      <td>505.36</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 보스 베이비 평점 변화
tmp = movie.query('name == ["보스 베이비"]')

import matplotlib.pyplot as plt
%matplotlib inline

plt.figure()
plt.plot( tmp['date'], tmp['point'] )
plt.legend(loc='best')
plt.show()
```


![png]({{ site.url }}/data/dataScience/03WebData/output_79_0.png)



```python
movie_pivot = pd.pivot_table(
    movie,
    index=["date"],
    columns=["name"],
    values=['point']
)

movie_pivot.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead tr th {
        text-align: left;
    }

    .dataframe thead tr:last-of-type th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr>
      <th></th>
      <th colspan="21" halign="left">point</th>
    </tr>
    <tr>
      <th>name</th>
      <th>10분</th>
      <th>7번째 내가 죽던 날</th>
      <th>8 마일</th>
      <th>가디언즈 오브 갤럭시</th>
      <th>가디언즈 오브 갤럭시 VOL. 2</th>
      <th>겟 아웃</th>
      <th>공각기동대 : 고스트 인 더 쉘</th>
      <th>그녀</th>
      <th>그랑블루</th>
      <th>그물</th>
      <th>...</th>
      <th>피와 뼈</th>
      <th>하루</th>
      <th>하이큐!! 끝과 시작</th>
      <th>한공주</th>
      <th>해리가 샐리를 만났을 때</th>
      <th>핵소 고지</th>
      <th>행복 목욕탕</th>
      <th>헤드윅</th>
      <th>흑집사 : 북 오브 더 아틀란틱</th>
      <th>히든 피겨스</th>
    </tr>
    <tr>
      <th>date</th>
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
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-05-01</th>
      <td>8.89</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.56</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>8.89</td>
      <td>NaN</td>
      <td>8.70</td>
      <td>NaN</td>
      <td>9.20</td>
      <td>9.38</td>
    </tr>
    <tr>
      <th>2017-05-02</th>
      <td>8.89</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.56</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>8.89</td>
      <td>NaN</td>
      <td>8.68</td>
      <td>NaN</td>
      <td>9.21</td>
      <td>9.37</td>
    </tr>
    <tr>
      <th>2017-05-03</th>
      <td>8.89</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9.22</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>8.89</td>
      <td>NaN</td>
      <td>8.70</td>
      <td>NaN</td>
      <td>9.22</td>
      <td>9.38</td>
    </tr>
    <tr>
      <th>2017-05-04</th>
      <td>8.89</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9.15</td>
      <td>NaN</td>
      <td>7.18</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.67</td>
      <td>NaN</td>
      <td>9.23</td>
      <td>9.38</td>
    </tr>
    <tr>
      <th>2017-05-05</th>
      <td>8.89</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9.08</td>
      <td>NaN</td>
      <td>7.16</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.69</td>
      <td>NaN</td>
      <td>9.24</td>
      <td>9.37</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 150 columns</p>
</div>




```python
movie_pivot.columns = movie_pivot.columns.droplevel()
movie_pivot.head()
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
      <th>name</th>
      <th>10분</th>
      <th>7번째 내가 죽던 날</th>
      <th>8 마일</th>
      <th>가디언즈 오브 갤럭시</th>
      <th>가디언즈 오브 갤럭시 VOL. 2</th>
      <th>겟 아웃</th>
      <th>공각기동대 : 고스트 인 더 쉘</th>
      <th>그녀</th>
      <th>그랑블루</th>
      <th>그물</th>
      <th>...</th>
      <th>피와 뼈</th>
      <th>하루</th>
      <th>하이큐!! 끝과 시작</th>
      <th>한공주</th>
      <th>해리가 샐리를 만났을 때</th>
      <th>핵소 고지</th>
      <th>행복 목욕탕</th>
      <th>헤드윅</th>
      <th>흑집사 : 북 오브 더 아틀란틱</th>
      <th>히든 피겨스</th>
    </tr>
    <tr>
      <th>date</th>
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
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2017-05-01</th>
      <td>8.89</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.56</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>8.89</td>
      <td>NaN</td>
      <td>8.70</td>
      <td>NaN</td>
      <td>9.20</td>
      <td>9.38</td>
    </tr>
    <tr>
      <th>2017-05-02</th>
      <td>8.89</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.56</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>8.89</td>
      <td>NaN</td>
      <td>8.68</td>
      <td>NaN</td>
      <td>9.21</td>
      <td>9.37</td>
    </tr>
    <tr>
      <th>2017-05-03</th>
      <td>8.89</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9.22</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>8.89</td>
      <td>NaN</td>
      <td>8.70</td>
      <td>NaN</td>
      <td>9.22</td>
      <td>9.38</td>
    </tr>
    <tr>
      <th>2017-05-04</th>
      <td>8.89</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9.15</td>
      <td>NaN</td>
      <td>7.18</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.67</td>
      <td>NaN</td>
      <td>9.23</td>
      <td>9.38</td>
    </tr>
    <tr>
      <th>2017-05-05</th>
      <td>8.89</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9.08</td>
      <td>NaN</td>
      <td>7.16</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.78</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8.69</td>
      <td>NaN</td>
      <td>9.24</td>
      <td>9.37</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 150 columns</p>
</div>




```python
# 한글 문제
import matplotlib.font_manager as fm
## 폰트 적용
font_location = '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf'
font_name = fm.FontProperties(fname=font_location).get_name()

from matplotlib import rc
rc('font', family=font_name)

movie_pivot.plot(
    y=["라라랜드", "노무현입니다", "사운드 오브 뮤직", '리얼', '악녀'],
    figsize=(12, 6)
)
plt.legend(loc="best")
plt.grid()
plt.show()
```

    /home/learn/.pyenv/versions/3.6.2/envs/Jupyter/lib/python3.6/site-packages/pandas/plotting/_core.py:1716: UserWarning: Pandas doesn't allow columns to be created via a new attribute name - see https://pandas.pydata.org/pandas-docs/stable/indexing.html#attribute-access
      series.name = label



![png]({{ site.url }}/data/dataScience/03WebData/output_82_1.png)
