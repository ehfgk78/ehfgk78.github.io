---
layout: post
title:  "데이터 사이언스 08 자연어 처리(2)-Naive Bayes 분류"
date:   2018-01-23 21:20 +0900
categories: [Data-Science]
tags: [Data-Science, Naive-Bayes]
---

* Kramdown table of contents
{:toc .toc}




# 1. 나이브 베이즈 분류 

> [위키백과 - 나이브 베이즈 분류](https://ko.wikipedia.org/wiki/%EB%82%98%EC%9D%B4%EB%B8%8C_%EB%B2%A0%EC%9D%B4%EC%A6%88_%EB%B6%84%EB%A5%98)


## 1) 예제1


```python
from nltk.tokenize import word_tokenize
import nltk
```


```python
train = [
    ( 'i like you', 'pos'),
    ( 'i hate you', 'neg'),
    ( 'you like me', 'neg'),
    ( 'i like her', 'pos')
]
```


```python
all_words = set( word.lower() for passage in train for word in word_tokenize(passage[0]) )

all_words
```




    {'hate', 'her', 'i', 'like', 'me', 'you'}




```python
t = [
    (
        { word: ( word in word_tokenize(x[0]) )  for word in all_words }, 
        x[1]
    )
    for x in train
]

t[0]
```




    ({'hate': False,
      'her': False,
      'i': True,
      'like': True,
      'me': False,
      'you': True},
     'pos')




```python
classifier = nltk.NaiveBayesClassifier.train(t)
classifier.show_most_informative_features()
```

    Most Informative Features
                         her = False             neg : pos    =      1.7 : 1.0
                        like = True              pos : neg    =      1.7 : 1.0
                           i = True              pos : neg    =      1.7 : 1.0
                        hate = False             pos : neg    =      1.7 : 1.0
                         you = True              neg : pos    =      1.7 : 1.0
                          me = False             pos : neg    =      1.7 : 1.0



```python
test_sentence = "i like MeRui"

test_sent_features = {
    word.lower()  :  ( word in word_tokenize(test_sentence.lower()))
    for word in all_words
}

test_sent_features
```




    {'hate': False,
     'her': False,
     'i': True,
     'like': True,
     'me': False,
     'you': False}




```python
classifier.classify( test_sent_features )
```




    'pos'



## 2) 예제2


```python
train = [
    ( 'I love this sandwich.', 'pos'),
    ( 'This is an amazing place!', 'pos' ),
    ( 'I feel very good about these beers.', 'pos'),
    ( 'This is my best work.', 'pos'),
    ( 'What an awesome view', 'pos'),
    ( 'I do not like this restaurant', 'neg'),
    ( 'I am tired of this stuff.', 'neg'),
    ( "I can't deal with this", 'neg'), 
    ( 'He is my sworn enemy!', 'neg'),
    ( 'My boss is horrible.', 'neg')    
]
```


```python
from nltk.tokenize import word_tokenize
```


```python
all_words = set( word.lower() for passage in train for word in word_tokenize(passage[0]) )

len(all_words)
```




    39




```python
t = [
    (
        { word: ( word in word_tokenize(x[0]) )  for word in all_words }, 
        x[1]
    )
    for x in train
]

t[0]
```




    ({'!': False,
      '.': True,
      'about': False,
      'am': False,
      'amazing': False,
      'an': False,
      'awesome': False,
      'beers': False,
      'best': False,
      'boss': False,
      'ca': False,
      'deal': False,
      'do': False,
      'enemy': False,
      'feel': False,
      'good': False,
      'he': False,
      'horrible': False,
      'i': False,
      'is': False,
      'like': False,
      'love': True,
      'my': False,
      "n't": False,
      'not': False,
      'of': False,
      'place': False,
      'restaurant': False,
      'sandwich': True,
      'stuff': False,
      'sworn': False,
      'these': False,
      'this': True,
      'tired': False,
      'very': False,
      'view': False,
      'what': False,
      'with': False,
      'work': False},
     'pos')




```python
classifier = nltk.NaiveBayesClassifier.train(t)
classifier.show_most_informative_features()
```

    Most Informative Features
                        this = True              neg : pos    =      2.3 : 1.0
                        this = False             pos : neg    =      1.8 : 1.0
                          an = False             neg : pos    =      1.6 : 1.0
                           . = True              pos : neg    =      1.4 : 1.0
                           . = False             neg : pos    =      1.4 : 1.0
                        like = False             pos : neg    =      1.2 : 1.0
                       these = False             neg : pos    =      1.2 : 1.0
                    horrible = False             pos : neg    =      1.2 : 1.0
                         not = False             pos : neg    =      1.2 : 1.0
                          am = False             pos : neg    =      1.2 : 1.0



```python
test_sentence = "This is the best band I've ever heards!"

test_sent_features = {
    word.lower()  :  ( word in word_tokenize(test_sentence.lower()))
    for word in all_words
}

test_sent_features
```




    {'!': True,
     '.': False,
     'about': False,
     'am': False,
     'amazing': False,
     'an': False,
     'awesome': False,
     'beers': False,
     'best': True,
     'boss': False,
     'ca': False,
     'deal': False,
     'do': False,
     'enemy': False,
     'feel': False,
     'good': False,
     'he': False,
     'horrible': False,
     'i': True,
     'is': True,
     'like': False,
     'love': False,
     'my': False,
     "n't": False,
     'not': False,
     'of': False,
     'place': False,
     'restaurant': False,
     'sandwich': False,
     'stuff': False,
     'sworn': False,
     'these': False,
     'this': True,
     'tired': False,
     'very': False,
     'view': False,
     'what': False,
     'with': False,
     'work': False}




```python
classifier.classify( test_sent_features )
```




    'pos'



## 3) 한글 예제
### (1) 영어와 같은 방식


```python
from nltk.tokenize import  word_tokenize
import nltk
from konlpy.tag import Twitter
```


```python
pos_tagger = Twitter()
```


```python
train = [
    ( '메리가 좋아', 'pos' ),
    ( '고양이도 좋아', 'pos' ),
    ( '난 수업이 지루해', 'neg' ),
    ( '메리는 이쁜 고양이야', 'pos' ),
    ( '난 마치고 메리랑 놀거야', 'pos' )
]
```


```python
all_words = set( word.lower() for passage in train for word in word_tokenize( passage[0]) )
all_words
```




    {'고양이도',
     '고양이야',
     '난',
     '놀거야',
     '마치고',
     '메리가',
     '메리는',
     '메리랑',
     '수업이',
     '이쁜',
     '좋아',
     '지루해'}




```python
t = [
    (
        { word: ( word in word_tokenize(x[0]) )  for word in all_words }, 
        x[1]
    )
    for x in train
]

t[0]
```




    ({'고양이도': False,
      '고양이야': False,
      '난': False,
      '놀거야': False,
      '마치고': False,
      '메리가': True,
      '메리는': False,
      '메리랑': False,
      '수업이': False,
      '이쁜': False,
      '좋아': True,
      '지루해': False},
     'pos')




```python
classifier = nltk.NaiveBayesClassifier.train(t)
```


```python
classifier.show_most_informative_features()
```

    Most Informative Features
                           난 = True              neg : pos    =      2.5 : 1.0
                          좋아 = False             neg : pos    =      1.5 : 1.0
                          이쁜 = False             neg : pos    =      1.1 : 1.0
                         마치고 = False             neg : pos    =      1.1 : 1.0
                         놀거야 = False             neg : pos    =      1.1 : 1.0
                         메리가 = False             neg : pos    =      1.1 : 1.0
                        고양이야 = False             neg : pos    =      1.1 : 1.0
                         메리랑 = False             neg : pos    =      1.1 : 1.0
                         메리는 = False             neg : pos    =      1.1 : 1.0
                        고양이도 = False             neg : pos    =      1.1 : 1.0



```python
test_sentence = '난 수업이 마치면 메리랑 놀거야'
```


```python
test_sent_features = { word.lower() : ( word in word_tokenize(test_sentence.lower() ) ) for word in all_words }
```


```python
test_sent_features
```




    {'고양이도': False,
     '고양이야': False,
     '난': True,
     '놀거야': True,
     '마치고': False,
     '메리가': False,
     '메리는': False,
     '메리랑': True,
     '수업이': True,
     '이쁜': False,
     '좋아': False,
     '지루해': False}




```python
classifier.classify( test_sent_features )
```




    'neg'



* test_sentence가 'neg'로 분류되었다. 
* 영어와 같은 방식으로 나이브 베이즈 분류하는 것은 실패하였다. 
* 한글은 **형태소 분석**을 해야한다. 

### (2) 형태소 분석


```python
def tokenize(doc):
    return [ '/'.join(t) for t in pos_tagger.pos( doc, norm=True, stem=True ) ]
```


```python
train_docs = [ ( tokenize(row[0]), row[1] )  for  row in train ]

train_docs
```




    [(['메리/Noun', '가/Josa', '좋다/Adjective'], 'pos'),
     (['고양이/Noun', '도/Josa', '좋다/Adjective'], 'pos'),
     (['난/Noun', '수업/Noun', '이/Josa', '지루하다/Adjective'], 'neg'),
     (['메리/Noun', '는/Josa', '이쁘다/Adjective', '고양이/Noun', '야/Josa'], 'pos'),
     (['난/Noun', '마치/Noun', '고/Josa', '메리/Noun', '랑/Josa', '놀다/Verb'], 'pos')]




```python
tokens = [ t for d in train_docs for t in d[0]  ]

tokens[ : 10]
```




    ['메리/Noun',
     '가/Josa',
     '좋다/Adjective',
     '고양이/Noun',
     '도/Josa',
     '좋다/Adjective',
     '난/Noun',
     '수업/Noun',
     '이/Josa',
     '지루하다/Adjective']




```python
def term_exists(doc):
    return { 'exists({})'.format(word) : (word in set(doc)) for word in tokens }
```


```python
train_xy = [ ( term_exists(d),  c)  for  d,c in train_docs ]

train_xy[0]
```




    ({'exists(가/Josa)': True,
      'exists(고/Josa)': False,
      'exists(고양이/Noun)': False,
      'exists(난/Noun)': False,
      'exists(놀다/Verb)': False,
      'exists(는/Josa)': False,
      'exists(도/Josa)': False,
      'exists(랑/Josa)': False,
      'exists(마치/Noun)': False,
      'exists(메리/Noun)': True,
      'exists(수업/Noun)': False,
      'exists(야/Josa)': False,
      'exists(이/Josa)': False,
      'exists(이쁘다/Adjective)': False,
      'exists(좋다/Adjective)': True,
      'exists(지루하다/Adjective)': False},
     'pos')




```python
classifier = nltk.NaiveBayesClassifier.train(train_xy)
```


```python
classifier.show_most_informative_features()
```

    Most Informative Features
              exists(난/Noun) = True              neg : pos    =      2.5 : 1.0
             exists(메리/Noun) = False             neg : pos    =      2.5 : 1.0
            exists(고양이/Noun) = False             neg : pos    =      1.5 : 1.0
        exists(좋다/Adjective) = False             neg : pos    =      1.5 : 1.0
              exists(는/Josa) = False             neg : pos    =      1.1 : 1.0
             exists(놀다/Verb) = False             neg : pos    =      1.1 : 1.0
              exists(야/Josa) = False             neg : pos    =      1.1 : 1.0
              exists(고/Josa) = False             neg : pos    =      1.1 : 1.0
              exists(도/Josa) = False             neg : pos    =      1.1 : 1.0
              exists(랑/Josa) = False             neg : pos    =      1.1 : 1.0



```python
test_sentence = [ ('난 수업이 마치면 메리랑 놀거야') ]
```


```python
test_docs = pos_tagger.pos( test_sentence[0] )

test_docs
```




    [('난', 'Noun'),
     ('수업', 'Noun'),
     ('이', 'Josa'),
     ('마치', 'Noun'),
     ('면', 'Josa'),
     ('메리', 'Noun'),
     ('랑', 'Josa'),
     ('놀거', 'Verb'),
     ('야', 'Eomi')]




```python
test_docs = tokenize( test_sentence[0] )

test_docs
```




    ['난/Noun',
     '수업/Noun',
     '이/Josa',
     '마치/Noun',
     '면/Josa',
     '메리/Noun',
     '랑/Josa',
     '놀다/Verb']




```python
test_sent_features = { word : (word in tokens)   for word in test_docs  }

test_sent_features
```




    {'난/Noun': True,
     '놀다/Verb': True,
     '랑/Josa': True,
     '마치/Noun': True,
     '메리/Noun': True,
     '면/Josa': False,
     '수업/Noun': True,
     '이/Josa': True}




```python
classifier.classify( test_sent_features )
```




    'pos'


