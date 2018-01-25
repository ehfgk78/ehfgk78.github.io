---
layout: post
title:  "데이터 사이언스 08 자연어 처리(1)-KoNLPy "
date:   2018-01-23 20:20 +0900
categories: [Data-Science]
tags: [Data-Science, KoNLPy, NLTK, wordcloud]
---

* Kramdown table of contents
{:toc .toc}


# 1. 한국어 자연어 처리

## 1) 자연어,  처리의 어려움

> [자연어 - 위키백과](https://ko.wikipedia.org/wiki/%EC%9E%90%EC%97%B0%EC%96%B4)

> [자연어 이해](http://www.aistudy.co.kr/linguistics/natural/nlp_kim.htm)

* 자연어 - 자연어란 프로그래밍 언어들, 즉 FORTRAN, COBOL, PASCAL, C, C++, Ada, LISP 및 PROLOG 등과 같은 인공 언어와는 다르게, 어법이 정해진 규칙만을 따르지 않고 일상적으로 사용되는 언어의 구조적인 체계를 말한다. 요약하면 자연어는 어떤 정돈된 완벽한 문법이나 형식적인 의미가 없는 언어를 말한다.

* 인간과 인간이 통신을 하고자 할 때에는 문어 (written language) 및 구어 (spoken language) 에 의한 수단으로 할 수 있다. 문어는 구어에 비해 문장의 애매모호함의 정도가 작은데, 그 이유는 정돈된 문법을 어느 정도 따르기 때문이다. 반면에 구어는 어떤 정돈된 완벽한 문법이나 형식적인 의미에 구애받지 않고 사용되므로 구어를 이해하기 위해서는 모든 잡음과 가청신호의 애매함을 처리할 수 있는 <u>**충분한 지식**</u>이 있어야 하므로 구어를 이해하는 것은 문어를 이해하는 것보다 훨씬 어렵다.

그러므로, 자연어 처리에서는 구어 및 문어를 동시에 이해하는 것이 필요하다. 즉 전체 자연어 이해를 위해서는 다음 두 가지를 동시에 만족해야 한다.

첫째, 자연어의 **어휘분석 (lexical)**, **구문분석 (syntactic)** 및 **의미분석 (semantic)** 지식을 이용하여 문어의 내용을 이해할 수 있어야 한다.

둘째, 담화하는 과정에서 발생하는 불확실한 것들을 처리하기 위해 **충분히 주어진 정보**를 이용하여 구어의 내용을 이해할 수 있어야 한다.


## 2) Google Cloud Platform 자연어 처리 api

> [구글의 자연어 처리 기술](https://brunch.co.kr/@mapthecity/25) 


## 3) Syntaxnet - 구글 오픈 소스

> [Google 자연어 처리 오픈소스 SyntaxNet 공개 - CPUU](https://cpuu.postype.com/post/166917/) 
*  디지털 포렌식 / IT보안 / 머신러닝 등에 관한 좋은 내용의 포스팅이 많다. 

> [구글 블로그]( http://googleresearch.blogspot.kr/2016/05/announcing-syntaxnet-worlds-most.html)

> [arXiv 논문]( http://arxiv.org/abs/1603.06042 )

> [Github - Syntaxnet 오픈소스]( https://github.com/tensorflow/models/tree/master/research/syntaxnet )

> [ Syntaxnet 튜토리얼 ](https://www.tensorflow.org/versions/r0.12/tutorials/syntaxnet/)

> [ Syntaxnet 튜토리얼 - 한글번역본 GitBook ](https://tensorflowkorea.gitbooks.io/tensorflow-kr/content/g3doc/tutorials/syntaxnet/) 


## 4) KoNLPy 

>  [ 공식문서 ](http://konlpy.org/en/v0.4.4/)

> [ GitHub ](https://github.com/konlpy/konlpy)

---

# 2. 설치-KoNLPy 등

## 1) KoNLPy

>  [ 공식문서 ](http://konlpy.org/en/v0.4.4/)

* Ubuntu16.04LTS,  Python 3.6.2 기준으로 작성하였다. 

```sh
➜  sudo apt-get install g++ openjdk-7-jdk python-dev python3-dev

➜  pip install JPype1-py3 

➜  pip install konlpy
```

* 선택사항 MeCab

```sh
➜ sudo apt-get install curl
➜ bash <(curl -s https://raw.githubusercontent.com/konlpy/konlpy/master/scripts/mecab.sh)
```


## 2) JPype1 대신 Py4J 가능함 

> [Py4J 공식문서](https://www.py4j.org/download.html)

* JPype1 문제가 발생하면 Py4J 모듈을  설치해본다. 

---

## 3) WordCloud 설치

* WordCloud

> [WordCloud 공식문서](https://amueller.github.io/word_cloud/)

> [Github 소스 코드](https://github.com/amueller/word_cloud) 

> [워드클라우드 그리기 - KoNLPy ](http://konlpy.org/ko/v0.4.3/examples/wordcloud/)

> [파이썬 형태소 분석으로 워드클라우드 그리기 - 팅크웨이](https://thinkwarelab.wordpress.com/2016/08/30/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%ED%98%95%ED%83%9C%EC%86%8C-%EB%B6%84%EC%84%9D%EC%9C%BC%EB%A1%9C-%EC%9B%8C%EB%93%9C%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EA%B7%B8%EB%A6%AC%EA%B8%B0/) 


* 추천 웹사이트 

> [Wordle](http://www.wordle.net/)

> [Jason Davies’ Word Cloud Generator](http://www.jasondavies.com/wordcloud/)

> [WorditOut](http://www.worditout.com/)

> [VocabGrabber](https://www.visualthesaurus.com/vocabgrabber/)

> [Tagxedo](http://www.tagxedo.com/) - 갤러리도 있다. 

> [TagCrowd](https://tagcrowd.com/)


```sh
➜  pip install wordcloud

Collecting wordcloud
  Using cached wordcloud-1.3.1.tar.gz
Requirement already satisfied: matplotlib in /home/learn/.pyenv/versions/3.6.2/envs/Jupyter/lib/python3.6/site-packages (from wordcloud)
Requirement already satisfied: numpy>=1.6.1 in /home/learn/.pyenv/versions/3.6.2/envs/Jupyter/lib/python3.6/site-packages (from wordcloud)
Collecting pillow (from wordcloud)
  Downloading Pillow-5.0.0-cp36-cp36m-manylinux1_x86_64.whl (5.9MB)
    100% |████████████████████████████████| 5.9MB 161kB/s 
Installing collected packages: pillow, wordcloud
  Running setup.py install for wordcloud ... done
Successfully installed pillow-5.0.0 wordcloud-1.3.1
```

---

## 4) nltk


![png]({{ site.url }}/data/dataScience/08(1)/1-4-nltk.png)


> [Natural Language ToolKit 공식문서](http://www.nltk.org/) 

```sh
➜  pip install --upgrade nltk

Collecting nltk
  Using cached nltk-3.2.5.tar.gz
Requirement already up-to-date: six in /home/learn/.pyenv/versions/3.6.2/envs/Jupyter/lib/python3.6/site-packages (from nltk)
Installing collected packages: nltk
  Running setup.py install for nltk ... done
Successfully installed nltk-3.2.5
```


---

# 3. 예제들 

## 1) 꼬꼬마 - 한국어 분석


```python
import nltk

nltk.download()
```

    showing info https://raw.githubusercontent.com/nltk/nltk_data/gh-pages/index.xml





    True




```python
from konlpy.tag import Kkma

ggoggoma = Kkma()
```


```python
# 문장 구분 

ggoggoma.sentences( '한국어 분석을 시작합니다 재미있어요~~' )
```




    ['한국어 분석을 시작합니다', '재미있어요~~']




```python
# 명사 찾기

ggoggoma.nouns('한국어 분석을 시작합니다 재미있어요~~')
```




    ['한국어', '분석']




```python
# 형태소 분석

ggoggoma.pos('한국어 분석을 시작합니다 재미있어요~~')
```




    [('한국어', 'NNG'),
     ('분석', 'NNG'),
     ('을', 'JKO'),
     ('시작하', 'VV'),
     ('ㅂ니다', 'EFN'),
     ('재미있', 'VA'),
     ('어요', 'EFN'),
     ('~~', 'SW')]



## 2) 한나눔

* **시작**합니다  ➜ 명사로 보이지만 동사에 해당함
* 그러나 넓게 단어를 찾는 경우 쓸모있음
* 사용하는 모듈에 따라 동일 입력에 대한 결과가 다를 수 있다. 


```python
from konlpy.tag import Hannanum

hannanum = Hannanum()
```


```python
hannanum.nouns(  '한국어 분석을 시작합니다 재미있어요~~'  )
```




    ['한국어', '분석', '시작']




```python
hannanum.pos( '한국어 분석을 시작합니다 재미있어요~~' )
```




    [('한국어', 'N'),
     ('분석', 'N'),
     ('을', 'J'),
     ('시작', 'N'),
     ('하', 'X'),
     ('ㅂ니다', 'E'),
     ('재미있', 'P'),
     ('어요', 'E'),
     ('~~', 'S')]



## 3) 한국어: 대한민국 국회 제 1809890호 의안

* 데이터는 `konlpy.corpus.kobill`에 있음
* stop_words, stopword

### (1) stopwords 처리

### (2) 연어( Collocation ) 처리 

* "함께 위치하는 단어들"에서 유래하여, 어휘의 조합 또는 짝을 이루는 말들을 일컬음.  예를들어, 'friend'와 주로 같이 쓰는 형용사는 "best", "good" 등이다. 


```python
from konlpy.corpus import kobill

files_ko = kobill.fileids()
doc_ko = kobill.open("1809890.txt").read()
```


```python
# 내용 확인

doc_ko[:100]
```




    '지방공무원법 일부개정법률안\n\n(정의화의원 대표발의 )\n\n 의 안\n 번 호\n\n9890\n\n발의연월일 : 2010.  11.  12.  \n\n발  의  자 : 정의화․이명수․김을동 \n\n이'




```python
# 문서 tokenize 
from konlpy.tag import Twitter


t = Twitter()
tokens_ko = t.morphs( doc_ko )
tokens_ko[:10]
```




    ['지방공무원법', '일부', '개정', '법률', '안', '(', '정의화', '의원', '대표', '발의']




```python
import nltk


ko = nltk.Text( tokens_ko, name="대한민국 국회 의안 제 1809890호" )
```


```python
len( ko.tokens ), len( set( ko.tokens ) )
```




    (1707, 476)




```python
# 형태소 별 빈도수 일부 
list( ko.vocab().items() )[ : 10 ]
```




    [('지방공무원법', 4),
     ('일부', 4),
     ('개정', 8),
     ('법률', 7),
     ('안', 6),
     ('(', 27),
     ('정의화', 2),
     ('의원', 2),
     ('대표', 1),
     ('발의', 2)]




```python
ko.vocab().most_common(5)
```




    [('.', 61), ('의', 46), ('육아휴직', 38), ('을', 34), ('(', 27)]




```python
stop_words_list = []
for m, n in ko.vocab().most_common(500):
    stop_words_list.append(m)
    
stop_words_list[ 440 : 460 ]
```




    ['이기',
     '보기',
     '힘들',
     '20',
     '라도',
     '600',
     '연감',
     '에서는',
     '여기',
     '주로',
     '대',
     '기자',
     '일용직',
     '활용하는',
     '일반',
     '기업체',
     '차이',
     '논의',
     '기간',
     '세로']




```python
# 시각화 - 최빈 단어 Plot

import matplotlib.pyplot as plt
import seaborn as sns

%matplotlib inline

# 한글 문제 
import matplotlib.font_manager as fm
# 폰트 적용
font_location = '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf'
font_name = fm.FontProperties(fname=font_location).get_name()

from matplotlib import rc
rc('font', family=font_name)
```


```python
plt.figure( figsize=(12, 6) )
ko.plot(50)
plt.show()
```


![png]({{ site.url }}/data/dataScience/08(1)/output_21_0.png)



```python
# STOP Word 적용하기 - 불필요하고 무의미한 형태소 배제하기 
tokens_ko = ko
```


```python
stop_words = [ '.', '(', ')', ',', "'", '%', '-', 'X', 'x', '의', '자', '에', '안', '번', '호', '자',
               '에', '다', '만','를', '이하', '액', '경우', '세', '가', '로', '제', '1',  '한',  '은',
               '8', '여', '5', '과',  '2', '에서',  ':', '는',  'P', '수', '조제', '으로', '따라',
               '호',  '4', '3', '%', 'N',  '7',  '2011', '것', 'p',  '자료',  '인', '할', '하고',  '다음', '표',
               '명', '하여', '｢', '｣',  '고', '급', '이상', '0', '확률', '위', '>', '일부', '및', '항제', '에는', '법',
              '생', '략', '같', '음',  '----------------------------',  '면',  '추계', '와',  '다고', '[', ']', '않', '따라서',
              '정', '조',  '사유', '있다', '서', '미', '첨부', '원', '항', '발생하지', '+', '에도',  '이나',
              '하기',  '위하여','①', '∼','관한', '또는', '---------------------------', '⋅', '③⋅④', '②', '있는', '야','것임',
               '같이', '“', '전', '”', '중인', '말한', '부터', '따르',  '관련',  '이다', '된',  '지급하던',  '더',  '않는',
               '육', '아', '되', '때문', '값',  '보다', '크', '여부', '알아보기',  '지', '따른',  '같다',  'A', 
               '21',  'B',  '바탕',  '그리고', '지급해야',  '도', '하더',  '보여',  '주', '보인',  '팀',  '번', '9890', 
               '연월일', '12', '발', '내용', '되어', '있어', '위해서','부모님',  '이는',  '곧',  '시키는',  '이어질',
               '있',  '하려', ').',  '부', '칙',  '날',  '신', '·',  '문대비', '현',  '행',  '개', '각', '호의', '어', '-------------------------',
               '느', '하나', '해당하는',  '원하면',  '권', '자는', '다만', '-------------.---------------',  '하는',  '특별한',  '사정',
               '없으', '--------------.', '취', '학', '위하', '필요하거',  '나',  '----------',  '하게', '되었', '때', '--------',  '등',
                '요인',   '공', '무',  '예상되는', '거나', '적', '로서',  '해당함',  '신청할',  '동', '세이', '하지', '않은',
              '어느',  '정도', '있으', '며',  '기', '이러한', '여의', '해당한', '순',  '받던',  '차', '인데',  '볼', '정확하게',
              '대한', '모', '델', '만들', '하자', '사', '용', '되는', '까지',  '급할',  '예정',  '므',  '923', '이며', 
              '인원',  '까지의', '정리하면', '~', '단위',  '비',  '율', '/',  '라고', '다시',  '작성하면',  '보자',  '하면',
              '배', '달', '하',  '이고',  '여배', '이기',  '보기', '힘들',  '라도',  '에서는',  '여기', '주로',  '대', '기자',
              '라',   '장', '문', '종', '분석관',  '김', '태', '완', '02', '788', '4649', 'tanzania@assembly.go.kr', '을', '이'
             ]

tokens_ko = [ each_word for each_word in tokens_ko if each_word not in stop_words ]
```


```python
tokens_ko[:10]
```




    ['지방공무원법', '개정', '정의화', '의원', '대표', '발의', '발의', '2010', '11', '정의화']




```python
# 등장 횟수 Count

ko.count('초등학교')
```




    6




```python
plt.figure( figsize=(12, 6) )
ko.dispersion_plot( ['육아휴직', '초등학교', '공무원'] )
```


![png]({{ site.url }}/data/dataScience/08(1)/output_26_0.png)



```python
# 색인 

ko.concordance('초등학교')
```

    Displaying 6 of 6 matches:
     ․ 김정훈 김학송 의원 ( 10 인 ) 제안 이유 및 주요 내용 초등학교 저학년 의 경우 에도 부모 의 따뜻한 사랑 과 보살핌 이 필요 한
     을 할 수 있는 자녀 의 나이 는 만 6 세 이하 로 되어 있어 초등학교 저학년 인 자녀 를 돌보기 위해서 는 해당 부모님 은 일자리 를 
     다 . 제 63 조제 2 항제 4 호 중 “ 만 6 세 이하 의 초등학교 취학 전 자녀 를 ” 을 “ 만 8 세 이하 ( 취학 중인 경우 
     전 자녀 를 ” 을 “ 만 8 세 이하 ( 취학 중인 경우 에는 초등학교 2 학년 이하 를 말한 다 ) 의 자녀 를 ” 로 한 다 . 부 
     . ∼ 3 . ( 현행 과 같 음 ) 4 . 만 6 세 이하 의 초등학교 취 4 . 만 8 세 이하 ( 취학 중인 경우 학 전 자녀 를 양
    세 이하 ( 취학 중인 경우 학 전 자녀 를 양육 하기 위하 에는 초등학교 2 학년 이하 를 여 필요하거 나 여자 공무원 이 말한 다 ) 의



```python
from nltk.downloader import Downloader


dler = Downloader("https://pastebin.com/raw/D3TBY4Mj")
dler.download('punkt')
```

    [nltk_data] Downloading package punkt to /home/learn/nltk_data...
    [nltk_data]   Package punkt is already up-to-date!





    True




```python
dler.download("stopwords")
```

    [nltk_data] Downloading package stopwords to /home/learn/nltk_data...
    [nltk_data]   Unzipping corpora/stopwords.zip.





    True




```python
# 연어 - Collocation

ko.collocations()
```

    초등학교 저학년; 육아휴직 대상자



```python
# stop_word가 적용된 nltk.Text 객체 

ko = nltk.Text( tokens_ko, name='국회 의안 발의' )
```


```python
plt.figure( figsize=(15, 6) )
ko.plot(50)
plt.show()
```

![png]({{ site.url }}/data/dataScience/08(1)/output_32_0.png)


---

# 4. WordCloud 

## 1) 예제1- Alice.txt


```python
from wordcloud import WordCloud, STOPWORDS
```


```python
import numpy as np
from PIL import Image
from wordcloud import STOPWORDS
```


```python
text = open("./data_science/07. alice.txt").read()
alice_mask = np.array( Image.open('./data_science/07. alice_mask.png') ) 

stopwords = set(STOPWORDS)
stopwords.add("said")
```


```python
plt.figure( figsize=( 8, 8) )
plt.imshow(
    alice_mask,
    cmap=plt.cm.gray,
    interpolation='bilinear'
)
plt.axis('off')
plt.show()
```


![png]({{ site.url }}/data/dataScience/08(1)/output_37_0.png)



```python
wc = WordCloud(
    background_color='white',
    max_words=2000,
    mask=alice_mask,
    stopwords=stopwords
)

wc = wc.generate(text)
```


```python
plt.figure( figsize=( 12, 12) )
plt.imshow(
    wc,
    interpolation='bilinear'
)
plt.axis('off')
plt.show()
```

![png]({{ site.url }}/data/dataScience/08(1)/output_39_0.png)



## 2) 예제2-스타워즈


```python
text = open('./data_science/07. a_new_hope.txt').read()

text = text.replace( 'HAN', "Han").replace("LUKE'S", 'LUKE')

mask = np.array( Image.open(  "./data_science/07. stormtrooper_mask.png")  )
```


```python
stopwords = set(STOPWORDS)
stopwords.add("int")
stopwords.add("ext")
```


```python
wc = WordCloud(
    max_words=1000,
    mask=mask,
    stopwords=stopwords,
    margin=10,
    random_state=1
).generate(text)

default_colors = wc.to_array()
```


```python
import random


def grey_color_func( word, font_size, position, orientation, random_state=None, **kwargs ):
    return 'hsi(0, 0%%, %d%%)' % random.randint( 60, 100)
```


```python
plt.figure( figsize=( 12, 12) )
plt.imshow(
    wc,
    interpolation='bilinear'
)
plt.axis('off')
plt.show()
```


![png]({{ site.url }}/data/dataScience/08(1)/output_45_0.png)



```python
data = ko.vocab().most_common(100)

wordcloud = WordCloud(
    font_path= '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf',
    relative_scaling=0.4,
    stopwords=stop_words,
    background_color='white',
).generate_from_frequencies( dict(data) )


plt.figure( figsize=(12, 6) )
plt.imshow(wordcloud)
plt.axis("off")
plt.show()
```

![png]({{ site.url }}/data/dataScience/08(1)/output_46_0.png)



