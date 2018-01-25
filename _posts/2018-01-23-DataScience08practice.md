---
layout: post
title:  "데이터 사이언스 08 자연어 처리(3)-practice "
date:   2018-01-23 22:20 +0900
categories: [Data-Science]
tags: [Data-Science, KoNLPy, NLTK, wordcloud, gensim]
---

* Kramdown table of contents
{:toc .toc}




# 1. 모듈

* 필수

> [requests 공식문서](http://docs.python-requests.org/en/master/)

* 선택

> [tqdm](https://pypi.python.org/pypi/tqdm)

> [git-tqdm](https://github.com/noamraph/tqdm)

```sh
(Jupyter) ➜  pip install tqdm

Collecting tqdm
  Downloading tqdm-4.19.5-py2.py3-none-any.whl (51kB)
    100% |████████████████████████████████| 61kB 725kB/s 
Installing collected packages: tqdm
Successfully installed tqdm-4.19.5
```


```python
import pandas as pd
import numpy as np

import matplotlib.pyplot as plt
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
from bs4 import BeautifulSoup
import requests
import time
```

# 2. Base URL, referer header



![png]({{ site.url }}/data/dataScience/08(3)/1.png)

> [Changing the referrer URL in python requests - Stack Overflow](https://stackoverflow.com/questions/20837786/changing-the-referrer-url-in-python-requests)

> [HTTP referer- 위키백과](https://en.wikipedia.org/wiki/HTTP_referer)

* referer - HTTP 리퍼러 (원래 referrer의 오자)는 요청되는 리소스에 링크 된 웹 페이지의 주소 (즉, URI 또는 ​​IRI)를 식별하는 HTTP 헤더 필드입니다. 리퍼러를 확인하면 새 웹 페이지에서 요청의 출처를 확인할 수 있습니다.   가장 일반적인 상황에서는 사용자가 웹 브라우저에서 하이퍼 링크를 클릭하면 브라우저가 대상 웹 페이지를 보유한 서버에 요청을 보냅니다. 요청에는 사용자가 있었던 마지막 페이지 (링크를 클릭 한 페이지)를 나타내는 referer 필드가 포함됩니다.  Referer 로깅은 웹 사이트 및 웹 서버가 홍보 또는 통계 목적으로 사람들이 방문하는 위치를 식별 할 수있게하는 데 사용됩니다. 

* 여기에서는 **naver.com에서 접근하는 것**으로 설정하였다.


```python
url = 'http://kin.naver.com/search/list.nhn?query={key_word}&page={num}'
url = url.format(
    key_word='여친 선물',
    num=1
)
url
```




    'http://kin.naver.com/search/list.nhn?query=여친 선물&page=1'




```python
response = requests.get(
    url,
    headers={ 'referer' : 'http://www.naver.com/' }
)
```


```python
soup = BeautifulSoup(
    response.text,
    'lxml'
)
```

# 3. Derived URL - 지식인 답변 페이지들



![png]({{ site.url }}/data/dataScience/08(3)/1(2).png)


```python
query_links = soup.find_all('dt')
query_links[0]
```




    <dt>
    <a class="_nclicks:kin.txt" href="http://kin.naver.com/qna/detail.nhn?d1id=7&amp;dirId=70122&amp;docId=290302927&amp;qb=7Jes7LmcIOyEoOusvA==&amp;enc=utf8&amp;section=kin&amp;rank=1&amp;search_sort=0&amp;spq=1" target="_blank"><b>여친선물</b> 해주셔야 하는데요 뭐가... </a>
    </dt>




```python
query_links[0].a['href']
```




    'http://kin.naver.com/qna/detail.nhn?d1id=7&dirId=70122&docId=290302927&qb=7Jes7LmcIOyEoOusvA==&enc=utf8&section=kin&rank=1&search_sort=0&spq=1'



![png]({{ site.url }}/data/dataScience/08(3)/3.png)



```python
url_query = query_links[0].a['href']

response_query = requests.get( query_links[0].a['href'] )

soup_tmp = BeautifulSoup( response_query.text, 'lxml' )

soup_tmp.find_all('div', '_endContentsText')
```




    [<div class="_endContentsText">
     <div>여자친구가 굉장히 푸석푸석한 얼굴스킨을 가지고 있거든요 . </div><div>항상 볼때마다 미스트를 이용하여 뿌려서 왜 그러는걸까 했는데요 </div><div>가만히 놔두면 많이 당김이느껴진다고 합니다 </div><div>이러므로 여친선물로 화장품을 한개를 선물해줄라고 하는데요 </div><div>건성기를 감소시킬수 있는 그런 여친선물이었음 좋겠습니다 . </div><div><br/></div><div>나이는 20대 초인데 어떤것이 좋을지 어떤것이 좋나요 </div><div>여친선물 해주셔야 하는데요 </div>
     </div>, <div class="_endContentsText">
     <div>스무살 여친선물로 줄 만한 화장상품이라면 </div><div>크림 같은 것보다도 스킨로션 정도가 나을것같습니다 ~ </div><div>특정 크림이나 색조품은 개인차마다 선호도가 상이하니깐요 </div><div><br/></div><div>스킨로션도 작용별로 다양하게 나온다는거 </div><div>건조한 피부라고 하셨지만 왜 건조한 건지 모르고있잖아요 </div><div>그러해서 건성기를 하락시키면서 얼굴자체에 무자극의 </div><div>천연화장품이 여친선물로 좋은 건 같네요 </div><div><br/></div><div>어떠한 스킨이라도 피부에 부담이 가해지지않고 </div><div>사용하기 효과적이기 때문이라는거 </div><div>실상 제가 사용하는 아이지아 천연스킨로션도 남자친구가 </div><div>여친선물 알아봐서 선물로주었다고 했었는데요 </div><div>진짜로 수분감도 괜찮고 발림성도 끈적끈적임도 없어가지구요 </div><div>얼굴피부에 깊숙히 스며드니까 여친선물 좋아요 </div><div><br/></div><div>그냥 권장해드리는 것보다 좀 소개드리자면 </div><div>우선적으로 갈락토미세스발효여과물이 밑베이스가 되는데요 </div><div>이 역시 천연이 피부결에 아미노산 , 비타민 등을 남겨주면서 </div><div>유해성분제거와 수분감에 괜찮으니 </div><div>여친선물로 주시면 여러가지 효능 보았다고 대만족일거에요 </div><div><br/></div><div>또 여친선물 말하실때 건조한 피부라고 하셨잖아요 </div><div>제가 쓰는 로션이 수분감 유기농성분이 완전 </div><div>가득하게 함유되어서 인기있다는데 혹 </div><div>스쿠알란 재료랑 쉐어버터성분라고 알고있으신지 모르겠어요 </div><div><br/></div><div>이 두재료가 보습 자연원료로 진짜 알려져있어요 </div><div>여친선물로 주셨으면 아마 다음에는 스킨미스트 덜 뿌리실거에요 </div><div><br/></div><div>뿐만아니라 아이지아 천연화장품이 괜찮은것이 </div><div>화학첨가물이 없잖습니까 </div><div>제가 바르는것은 진짜 전부 천연원료라 </div><div>여친선물로 줘도 좋답니다 </div><div><br/></div><div>가격도 삼만원 정도 하니 여친선물로 </div><div>고퀄이면서 실용성괜찮고 제가 여친선물 추천할게요 </div><div><br/></div><div><br/></div>
     </div>, <div class="_endContentsText">
     							수분크림, 영양크림 쪽으로 선물해주시면 좋아할것 같아요! <br/>네이버에 보습력좋운 수분크림으로 검색해보세요’
     						</div>]




```python
# 답변 페이지 url 모으기

url = 'http://kin.naver.com/search/list.nhn?query={key_word}&page={num}'
query_links = []

for n in range(1,10):
    url = url.format( key_word='여친 선물', num=n )
    response_query = requests.get(url)
    soup = BeautifulSoup( response_query.text, 'lxml' )
    query_links.extend( soup.find_all('dt') )
    
query_links[0].a['href']
```




    'http://kin.naver.com/qna/detail.nhn?d1id=7&dirId=70122&docId=290302927&qb=7Jes7LmcIOyEoOusvA==&enc=utf8&section=kin&rank=1&search_sort=0&spq=1'




```python
import time
# 글 모으기


girl_friend_text = []

for each_link in query_links:
    res = requests.get(each_link.a['href'])
    soup_tmp = BeautifulSoup( res.text, 'lxml' )
    
    query_result = soup_tmp.find_all( 'div',  '_endContentsText' )
    time.sleep(1)
    
    for each in query_result:
        girl_friend_text.append( each.get_text() )
```


```python
girl_friend_text[0]
```




    '\n여자친구가 굉장히 푸석푸석한 얼굴스킨을 가지고 있거든요 .\xa0항상 볼때마다 미스트를 이용하여 뿌려서 왜 그러는걸까 했는데요\xa0가만히 놔두면 많이 당김이느껴진다고 합니다\xa0이러므로 여친선물로 화장품을 한개를 선물해줄라고 하는데요\xa0건성기를 감소시킬수 있는 그런 여친선물이었음 좋겠습니다 .\xa0나이는 20대 초인데 어떤것이 좋을지 어떤것이 좋나요\xa0여친선물 해주셔야 하는데요\xa0\n'




```python
len( girl_friend_text )
```




    441




```python
# 리스트의 각 글을 하나의 텍스트로 만들기

present_text = ""
for each in girl_friend_text:
    present_text += each + '\n'
```


```python
present_text[:500]
```




    '\n여자친구가 굉장히 푸석푸석한 얼굴스킨을 가지고 있거든요 .\xa0항상 볼때마다 미스트를 이용하여 뿌려서 왜 그러는걸까 했는데요\xa0가만히 놔두면 많이 당김이느껴진다고 합니다\xa0이러므로 여친선물로 화장품을 한개를 선물해줄라고 하는데요\xa0건성기를 감소시킬수 있는 그런 여친선물이었음 좋겠습니다 .\xa0나이는 20대 초인데 어떤것이 좋을지 어떤것이 좋나요\xa0여친선물 해주셔야 하는데요\xa0\n\n\n스무살 여친선물로 줄 만한 화장상품이라면\xa0크림 같은 것보다도 스킨로션 정도가 나을것같습니다 ~\xa0특정 크림이나 색조품은 개인차마다 선호도가 상이하니깐요\xa0스킨로션도 작용별로 다양하게 나온다는거\xa0건조한 피부라고 하셨지만 왜 건조한 건지 모르고있잖아요\xa0그러해서 건성기를 하락시키면서 얼굴자체에 무자극의\xa0천연화장품이 여친선물로 좋은 건 같네요\xa0어떠한 스킨이라도 피부에 부담이 가해지지않고\xa0사용하기 효과적이기 때문이라는거\xa0실상 제가 사용하는 아이지아 천연스킨로션도 남자친구가\xa0여친선물 알아봐서 선물로주었다고 했었는데요\xa0진짜로 수분감도 괜'



# 4. 형태소 분석


```python
import nltk
from konlpy.tag import Twitter


t = Twitter()
```


```python
tokens_ko = t.morphs(present_text)
ko = nltk.Text( 
                      tokens_ko,
                      name='여자 친구 선물'
              )

ko.vocab().most_common(10)
```




    [('요', 819),
     ('선물', 720),
     ('이', 675),
     ('도', 648),
     ('\xa0', 558),
     ('을', 522),
     ('.', 459),
     ('에', 405),
     (',', 360),
     ('은', 351)]




```python
stop_words_list = []
for m, n in ko.vocab().most_common(500):
    stop_words_list.append(m)
    
print( stop_words_list[ :50 ])
```

    ['요', '선물', '이', '도', '\xa0', '을', '.', '에', ',', '은', '것', '좋', '가', '여친', '로', '..', '고', '는', '면', '~', '거', '를', '다', '?', '괜찮', '주', '서', '니다', '에서', '수', '만', '지', '한', '있', '시', '들', '있는', '나', '일', '는데', '되', '분', '저', '생일', '여자친구', '습니다', '같아', '여자', '시계', '라고']



```python
stop_words = ['요', '이', '도', '\xa0', '을', '.', '에', ',', '은', '것', '좋', '가', '로', '..', '고', 
  '는', '면', '~', '거', '를', '다', '?', '괜찮', '주', '서', '니다', '에서', '수', '만', '지', '한', '있', 
 '시', '들', '있는', '나', '일', '는데', '되', '분', '저', '습니다', '같아', '라고', '제', '!', '까', '데요', 
 '했', '개', '인데', '아요', '의', '때', '랑', '어요', '입니', '있어', '전', '게', '많이', '합', '야', '같네',
 '아', '알', '아니', '줘', '속', '중', '다른', '다면', '같은', '건', '적', '드리', '세요', '같이', '라구요',
 '...', 'ㅎㅎ', '넣어', '안', '어서', '넣', '있구', '님', '1', '하고', '데', '거든요', '좀', '으로', '실', 
'라', '안녕하세', '이런', '어떨', '않', '사', '어떠', '해서', '드려', '까지', '보시', '던', '길', '날', 
'있으', '다고', '하는', '음', '대', '어떤', '정도', '이나', '면서', '않고', '었', '구요', '그냥', '등', 
'와', '에요', '네이버', '추천해', '하시', '인', '없고', '듯', '3', '이랑', '더라구요', '질문', '여', 
'글구', '등등', '보고', '2', '만든', '하세', '또는', '일단', '생각하시', '한번', '쯤', '될', '이에요', 
'넘', '해', '배송', '제품', '원', '챙겨', '잘', '줬', '~^^', '가지', '항상', '마다', '20', '하니', 
'없어', '스', '보다', '역시',  '또', '으', '두', '해보세', '뭘', '너무', '엄청', '니', ')', '예', '!!', '걸', 
'ㅠㅠ', '을까', '알려', '보니', '답변', '볼게', '곳', '예요', '혹시', 'D', '하면', '용', '으로도',
'자신', '들어가서', '엮어', '나가는', '한마디', '책속', '바뀌', '받고', '얼마나', '웃었', '는지', 
'맨', '앞장', '아직', '해보셨', '한데', '9',  '첨부', '하구', '얼마', 'ㅋㅋㅋ', '^^', '이번', '싶은', '라는',
'하루', '만에', '오더', '아이디', '참조','에게', '있고', '과', '을까요', '더', '주세', '[', '마', ']',   
'대비', '여성', '근데', '요즘', '없이', '굉장히', '볼때', '왜', '건성', '기를', '이었', '꾸게', '해준', '다는',             
'겠', '을지', '해주셔', '상품', '같', '하셨', '지만', '잖', '사용하는','좋아할', '일정', '10', '일도', '큰',
'끈', '물이', '밑', '되는', '감', '완전', '재료', '원료', '아마', '다음', '뿐', '답', '실용', '게요',
'작은', '부탁드립니', '좋아하고', '겨울', '이니까', '(', '생활', '세트', '셔', '털', '몇', '매일', '즐겁',
'봤', '세', '한테', '하신', '찾으', '신', '아주', '골고루', '많', '올려', '됩', '해보시', '된', '고요', 
'보이', '찾고', '군요', '‘', '시간', '사람', '"', '네', '케이스', '관심', '해줬', '할','비교', '하', '가요', 
'ㅋㅋ', '\xa0\xa0', '이제', '그', '따로', '맞추는', '무난', '히', '쓸','애인', '자', '보','더니', '기념할',    
'선택', '이미지', '원하시', '많은', '보다는', '+', '000', '이라', '서울', '계신', "'", '해주고', 
'기대', '치가', '높아져', '200', '엔', '겟', '부탁드려', '비싸지', '오래', '모든', '남자',
'분들', '아래', '보는', '에서도', '처음', '들어', '꺼', '만원', '게게', '겔', '...?', '도하', '수공', '_', 
'아이디어', '정말', '푸석푸석', '이용하여', '뿌려', '그러', '는걸', '가만히', '놔두면', '당김', 
'느껴진', '이러므로', '감소', '시킬', '그런', '나이', '초', '나요', '스무살', '보다도', '특정', 
'색조', '품은', '개인', '차마', '선', '선물', '여친', '생일', '여자친구', '여자', '주소', '크리스마스',
'화이트데이', '특별한', '줄', '모르', '추천'
             ]

tokens_ko = [ each_word for each_word in tokens_ko if each_word not in stop_words]
```


```python
ko = nltk.Text( tokens_ko,  name='여자 친구 선물')
```


```python
plt.figure( figsize=(15, 6) )

ko.plot(50)
plt.show()
```


![png]({{ site.url }}/data/dataScience/08(3)/output_23_0.png)


---

# 5. 워드클라우드

Feed-Forward 신경망 언어 모형 (Neural Net Language Model)¶
이러한 단어 임베딩은 신경망을 이용하여 언어 모형을 만들려는 시도에서 나왔다. 자세한 내용은 다음 논문을 참고한다.

"A Neural Probabilistic Language Model", Bengio, et al. 2003

http://www.jmlr.org/papers/volume3/bengio03a/bengio03a.pdf
"Efficient Estimation of Word Representations in Vector Space", Mikolov, et al. 2013

https://arxiv.org/pdf/1301.3781v3.pdf
"word2vec Parameter Learning Explained", Xin Rong,

http://www-personal.umich.edu/~ronxin/pdf/w2vexp.pdf

더 많은 한국어 코퍼스를 사용한 단어 임베딩 모형은 다음 웹사이트에서 테스트해 볼 수 있다.

http://w.elnn.kr/



```python
from wordcloud import WordCloud, STOPWORDS
from PIL import Image

from wordcloud import ImageColorGenerator
```


```python
mask = np.array( Image.open("./data_science/heart.jpg"))

image_colors = ImageColorGenerator(mask)
```


```python
data = ko.vocab().most_common(200)

wordcloud = WordCloud(
    font_path= '/usr/share/fonts/truetype/nanum/NanumBarunGothic.ttf',
    relative_scaling=0.2,
    mask=mask,
    stopwords=stop_words,
    background_color='white',
    margin=10,
    random_state=1,
    min_font_size=1,
    max_font_size=100
).generate_from_frequencies( dict(data) )

default_colors = wordcloud.to_array()

plt.figure( figsize=(12, 12) )
plt.imshow(
    wordcloud, 
    interpolation='bilinear'
)
plt.axis("off")
plt.show()
```



![png]({{ site.url }}/data/dataScience/08(3)/output_27_0.png)



---

# 6. 모듈 gensim

> [gensim 공식문서](https://radimrehurek.com/gensim/)

> [한국어와 NLTK, Gensim의 만남](https://www.lucypark.kr/docs/2015-pyconkr/)


## 1) 설치

```sh

(Jupyter) ➜ pip install gensim

Successfully installed boto-2.48.0 boto3-1.5.20 botocore-1.8.34 bz2file-0.98 docutils-0.14 gensim-3.2.0 jmespath-0.9.3 s3transfer-0.1.12 smart-open-1.5.6
```

## 2) gensim의 토픽 제너레이션


```python
import gensim
from gensim.models import word2vec
```


```python
twitter=Twitter()
results = []
lines = present_text

for line in lines:
    malist = twitter.pos( line, norm=True, stem=True )
    r=[]
    for word in malist:
        if not word[1] in ["Josa", "Eomi", "Punctuation"]:
            r.append( word[0] )
    r1=("".join(r)).strip()
    results.append(r1)        
```


```python
results[ 100 : 110]
```




    ['선', '물', '로', '', '화', '장', '품', '', '', '하다']




```python
data_file = 'present_girl_friend.data'
with open(data_file, 'w', encoding='utf-8') as fp:
    fp.write("\n".join(results))
    
data = word2vec.LineSentence( data_file )    
model = word2vec.Word2Vec(
    data,
    size=200,
    window=10,
    hs=1,
    min_count=2,
    sg=1
)

model.save('present_girl_friend.model')
```

    WARNING:gensim.models.word2vec:under 10 jobs per worker: consider setting a smaller `batch_words' for smoother alpha decay



```python
model = word2vec.Word2Vec.load('present_girl_friend.model')
```


```python
model.most_similar( positive=['선'] )
```

    /home/learn/.pyenv/versions/3.6.2/envs/Jupyter/lib/python3.6/site-packages/ipykernel_launcher.py:1: DeprecationWarning: Call to deprecated `most_similar` (Method will be removed in 4.0.0, use self.wv.most_similar() instead).
      """Entry point for launching an IPython kernel.





    [('극', 0.22422043979167938),
     ('마', 0.17464378476142883),
     ('립', 0.1700051724910736),
     ('추', 0.16442108154296875),
     ('곧', 0.15310430526733398),
     ('오다', 0.1369200050830841),
     ('갖', 0.13653887808322906),
     ('행', 0.13436168432235718),
     ('닉', 0.13379661738872528),
     ('급', 0.13327571749687195)]


