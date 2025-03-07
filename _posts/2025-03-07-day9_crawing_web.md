---
layout: posts
title:  "Day 9: Crawling html"
date: 2025-03-07
categories: coding
tag: [python, blog, crawling, html, web]
excerpt: "Learning how to crawl web pages using Python libraries like requests, BeautifulSoup, and Selenium."
toc: true
author_profile: false
---

<head>
  <style>
    table.dataframe {
      white-space: normal;
      width: 100%;
      height: 240px;
      display: block;
      overflow: auto;
      font-family: Arial, sans-serif;
      font-size: 0.9rem;
      line-height: 20px;
      text-align: center;
      border: 0px !important;
    }

    table.dataframe th {
      text-align: center;
      font-weight: bold;
      padding: 8px;
    }

    table.dataframe td {
      text-align: center;
      padding: 8px;
    }

    table.dataframe tr:hover {
      background: #b8d1f3; 
    }

    .output_prompt {
      overflow: auto;
      font-size: 0.9rem;
      line-height: 1.45;
      border-radius: 0.3rem;
      -webkit-overflow-scrolling: touch;
      padding: 0.8rem;
      margin-top: 0;
      margin-bottom: 15px;
      font: 1rem Consolas, "Liberation Mono", Menlo, Courier, monospace;
      color: $code-text-color;
      border: solid 1px $border-color;
      border-radius: 0.3rem;
      word-break: normal;
      white-space: pre;
    }

  .dataframe tbody tr th:only-of-type {
      vertical-align: middle;
  }

  .dataframe tbody tr th {
      vertical-align: top;
  }

  .dataframe thead th {
      text-align: center !important;
      padding: 8px;
  }

  .page__content p {
      margin: 0 0 0px !important;
  }

  .page__content p > strong {
    font-size: 0.8rem !important;
  }

  </style>
</head>


### 크롤러를 만들기 위해 필요한 지식



- 클라이언트 : 사용자가 직접적으로 사용하는 프로그램. 여기서 클라이언트는 웹과 웹 브라우저를 의미

- 서버 : 웹 브라우저는 서버에게 데이터를 요청하여 웹 페이지(html, json 등)를 가져옴.

- 파이썬 : 클라이언트와 서버의 동작 원리를 이해하고 크롤러를 만들기위해 필요한 프로그래밍 언어



### 크롤러를 만들어야 하는 이유

1. 데이터 생산

- 데이터를 직접 수집하기 에는 생산속도가 너무 빠름.

- 데이터의 양이 사람이 직접 분류하기에 너무 많음.



2. 데이터 활용

- 음성인식, 의료, 핀테크, 금융, 검색엔진 등 각종 분야에서 데이터를 활용하여 인공지능 발전

- 2016년 알파고 VS 이세돌의 바둑 경기로 인공지능의 발전 가능성을 보여줌



3. 데이터 수집방법

- API를 활용한 데이터 수집 (API:해당 서비스에서 제공 하는 데이터 제공 방법)

- 크롤러를 활용한 데이터 수집

- 크롤러를 공부하면 개발을 모르더라도 타사 API를 활용할 수 있다.





### 서버와 클라이언트 데이터 주고받는 방법

#### 1. 요청 메소드(Request Methods)

- 요청 메소드란 클라이언트가 서버에게 데이터 요청시 목적을 알려주는 방법

- 요청 메소드 CRUD를 의미하는 4가지 메소드를 사용

- C : Create 데이터 생성

- R : Read 데이터 조회

- U : Update 데이터 수정

- D : Delete 데이터 삭제

 요청 메소드 종류

- Read에 해당하는 GET 메소드

- Read및 Create에 해당하는 POST 메소드

- Update에 해당하는 PUT 메소드

- Delete에 해당하는 Delete 메소드



#### 2. 응답코드(Response Code) 

- 응답 코드는 서버가 클라이언트 요청을 처리하고 난 후 상태에 따라 반환하는 코드

- 응답코드는 크게 5가지 타입이 있음

- 조건부 응답, 성공, 새로고침, 요청오류, 서버오류를 코드로 알려줌

- 100~ 500 번대로 상태 표현





응답코드 종류



- 100~ 199 : 조건부 응답



- 200~ 299 : 성공

200 : 서버가 클라이언트의 요청을 성공적으로 처리

201 : 클라이언트가 요청한 정보를 정상적으로 저장(ex. 회원가입)

204 : 클라이언트로 반환되는 데이터가 없을 때



- 300~ 399 : 새로고침

300~ 399에 해당되는 코드는 새로고침할 때 반환되는 코드



- 400~ 499 : 요청오류

400 : 잘못 요청

401 : 권한 없음(ex. 로그인이 필요한 페이지에 로그인 없이 접속할 때)

404 : 찾을 수 없음(ex. 잘못된 url 요청)



- 500~ 599 : 서버오류



500 : 서버 내부 오류(서버 내부에 코드가 잘못 됐을 때도 발생 함)

502 : 불량 게이트웨이(게이트웨이 == 웹 서버)

503 : 서비스를 사용할 수 없음

504 : 게이트웨이 시간초과



Tip) 조건부 응답인 100번대는 거의 사용하지 않음





#### 3 헤더

헤더란 편지를 보낼 때 주소, 우편번호, 받는 사람, 보낸사람과같이 주고받는 사람의 정 보를 나타내는 것처럼, 서버와 클라이언트의 정보를 포함하고 있는 것.





헤더종류



- 요청헤더(Request Header) : 클라이언트가 문서와 구성 형태 및 형식을 포함한 헤더. 클라이언트(크롤러)의 주요 헤더



  포함요소

- Accept : 클라이언트의 사용 가능 미디어 타입 !

- Accept-Charset : 클라이언트에서 사용할 수있는 문자셋

- Accept_Encoding: 클라이언트에서 제공되는 인코딩 방법

- Accept-language: 클라이언트가 인식할 수 있는 언어

- Host: 서버의 기본 URL

- Cookie: 쿠키정보

- Referred : 클라이언트가 서버에 요청하기 직전에 머물렀던 주소

- User-Agent : 브라우저 정보





- 응답헤더(Response Header) : 응답 헤더는 서버가 클라이언트에 응답 때 요청과 서버의 구성을 포함한 헤더



  포함요소

- HTTP-Version : http 버전

- Status-code : 응답코드 b

- Serve: 서버 프로그램의 이름과 버전정보

- Set-Cookie: 서버측에서 세션 쿠키 정보 설정

- Expires: 캐시 유효 기간





- 2EgF(General Header) 일반헤더



- 엔티티 헤더(Entity Header)



#### 4. URL



프로토콜://주소 또는 IP: 포트 번호/리소스 경로?쿼리스트링



-  프로토콜 : 인터넷에서 주로 사용하는 프로토콜은 HTTP(S). 이 외에도 FTP, SFTP, SSH와 같은 프로토콜 존재.

-  주소 또는 IP: 우리가 데이터를 요청하는 타겟

- 포트 번호 : HTTP는 80포트, HTTPS는 443번 포트가 된다.








```python
import webbrowser
```


```python
url = 'www.naver.com'
webbrowser.open(url)
```

<pre>
True
</pre>

```python
# 네이버 검색 (파이썬)
url = 'https://search.naver.com/search.naver?query=%ED%8C%8C%EC%9D%B4%EC%8D%AC'
webbrowser.open(url)
```

<pre>
True
</pre>

```python
import urllib.parse      #맥에선 오류 
url = 'https://search.naver.com/search.naver?query='
word = '파이썬'

encoded = urllib.parse.quote(word)
webbrowser.open(url+word)
```

<pre>
True
</pre>

```python
import urllib.parse

word = '이순신'

encoded = urllib.parse.quote(word)
url = f'https://search.naver.com/search.naver?query={encoded}'

webbrowser.open(url)
```

<pre>
True
</pre>

```python
# 네이버 검색 (유저 입력)
url = 'https://search.naver.com/search.naver?query='
search_word = input('검색어를 입력해주세요 : ')

webbrowser.open(url+search_word)
```

<pre>
검색어를 입력해주세요 : 안녕
</pre>
<pre>
True
</pre>

```python
# 구글 검색 (강아지)
word = '강아지'
encoded = urllib.parse.quote(word)
url = 'https://www.google.com/search?q='

webbrowser.open(url+encoded)
```

<pre>
True
</pre>

```python
urls = ['www.naver.com', 'www.google.com', 'www.daum.net']

for url in urls:
    webbrowser.open(url)
```


```python
words = ['강아지','고양이','햄스터']
url = 'https://www.google.com/search?q='

for word in words:
    encoded = urllib.parse.quote(word)
    webbrowser.open(url+encoded)
```


```python
# 구글 검색 (강아지)  #맥에선 이렇게
word = '강아지'
encoded = urllib.parse.quote(word)
url = 'https://www.google.com/search?q='

webbrowser.open(url+encoded)
```

<pre>
True
</pre>

```python
import requests

r = requests.get("https://www.google.com")
r
```

<pre>
<Response [200]>
</pre>
# 웹



웹 이란 인터넷에 연결된 클라이언트들이 정보를 공유할 수 있는 공간을 의미. 

이러한 공간을 사용할 수 있도록 해주는 것이 웹 브라우저

- 웹 페이지 : 웹 에서 보는 페이지 하나하나를 웹 페이지라고 함.

- 웹 사이트 : 웹 페이지의 집합



웹 페이지 구성요소 <Front hand>

- HTML : 웹 페이지의 레이아웃을 잡아주는 혁할

- JavaScript : 웹 페이지에 기능을 넣어주는 역할

- CSS : 웹 페이지를 꾸며주는 역할



HTML은 HyperText Markup Language의 약자로 웹을 이루고 있는 가장 기본이 되는 요소

확장자를 html로 해서 저장을 하면 html 파일 생성가능

html, head, body를 태그라고 부르며 html은 태그로 이루짐



```python
r.text[:100]
```

<pre>
'<!doctype html><html itemscope="" itemtype="http://schema.org/WebPage" lang="ko"><head><meta content'
</pre>

```python
r = requests.get("https://www.google.com").text
r[:100]
```

<pre>
'<!doctype html><html itemscope="" itemtype="http://schema.org/WebPage" lang="ko"><head><meta content'
</pre>

```python
url = "https://www.google.com/search"
res = requests.get(url, params={"q":"강아지"})
print(res.url)
```

<pre>
https://www.google.com/search?q=%EA%B0%95%EC%95%84%EC%A7%80
</pre>

```python
url = "https://www.example.com"
res = requests.get(url, params={"key1": "value1", "key2":"value2"}) # get 값을 조회하는 기능
print(res.url)
```

<pre>
https://www.example.com/?key1=value1&key2=value2
</pre>

```python
res = requests.post(url, data={"key1": "value1", "key2":"value2"}) # post 값을 생성(변경)하는 기능
print(res.url)
```

<pre>
https://www.example.com/
</pre>

```python
from bs4 import BeautifulSoup

html = """<html><p>test</p></html>"""
soup = BeautifulSoup(html, 'lxml')
print(soup)

html = """<body><p>test</p></body>"""
soup = BeautifulSoup(html, 'lxml')
print(soup)

html = """<p>test</p>"""
soup = BeautifulSoup(html, 'lxml')
print(soup)
```

<pre>
<html><body><p>test</p></body></html>
<html><body><p>test</p></body></html>
<html><body><p>test</p></body></html>
</pre>

```python
html = """<html><body><div><span>\
        <a href=http://www.naver.com>naver</a>\
        <a href=http://www.google.co.kr>google</a>\
        <a href=http://www.daum.net/>daum</a>\
        </span></div></body></html>"""

soup = BeautifulSoup(html, 'lxml')
soup
```

<pre>
<html><body><div><span> <a href="http://www.naver.com">naver</a> <a href="http://www.google.co.kr">google</a> <a href="http://www.daum.net/">daum</a> </span></div></body></html>
</pre>

```python
print(soup.prettify())
```

<pre>
<html>
 <body>
  <div>
   <span>
    <a href="http://www.naver.com">
     naver
    </a>
    <a href="http://www.google.co.kr">
     google
    </a>
    <a href="http://www.daum.net/">
     daum
    </a>
   </span>
  </div>
 </body>
</html>
</pre>

```python
html = """<html> <head><title>test site</title></head> <body> 
<p>test1</p><p id="d">test2</p><p>test3</p></p> </body></html>"""

soup = BeautifulSoup(html, 'lxml')

print(soup.find_all('p', id='d'))
print(soup.find_all('p', id='c'))
print(soup.find_all('p')) # find_all을 사용하면 리스트에 값을 넣어서 반환 
```

<pre>
[<p id="d">test2</p>]
[]
[<p>test1</p>, <p id="d">test2</p>, <p>test3</p>]
</pre>

```python
html = """<html> <head><title>test site</title></head> <body> 
<p>test1</p><p class="d">test2</p><p class="c">test3</p></p> <a>a tag</a> <b>b tag</b></body></html>"""

soup = BeautifulSoup(html, 'lxml')
print(soup.find_all(['a', 'b']))
```

<pre>
[<a>a tag</a>, <b>b tag</b>]
</pre>

```python
html = """<html> <head><title>test site</title></head> <body> 
<p>test1</p><p class="d">test2</p><p class="c">test3</p></p> </
body></html>"""

soup = BeautifulSoup(html, 'lxml')

print(soup.find_all('p', class_='d'))
print(soup.find_all('p', class_='c'))
print(soup.find_all('p'))
```

<pre>
[<p class="d">test2</p>]
[<p class="c">test3</p>]
[<p>test1</p>, <p class="d">test2</p>, <p class="c">test3</p>]
</pre>

```python
html = """<html> <head><title>test site</title></head> <body> 
<p>test1</p><p class="d">test2</p><p class="c">text3</p></p> </
body></html>"""

soup = BeautifulSoup(html, 'lxml')
print(soup.find_all('p', string="test1"))
print(soup.find_all('p', string="t"))
```

<pre>
[<p>test1</p>]
[]
</pre>

```python
import re

pattern = re.compile(r'^t')
print(soup.find_all('p', string=pattern))
```

<pre>
[<p>test1</p>, <p class="d">test2</p>, <p class="c">text3</p>]
</pre>

```python
html = """<html> <head><title>test site</title></head> <body> <p id="i"
class="a">test1</p><p class="d">test2</p><p class="d">test3</p></p> <a>a tag</a> <b>b
tag</b></body></html>"""
soup = BeautifulSoup(html, 'lxml')
print(soup.find('p', class_="d")) # 1개
print(soup.find('p', class_="d")) 
print(soup.find('p', id="i")) 
```

<pre>
<p class="d">test2</p>
<p class="d">test2</p>
<p class="a" id="i">test1</p>
</pre>

```python
html = """<html><body><div><span>\
        <a href=http://www.naver.com>naver</a>\
        <a href=http://www.google.co.kr>google</a>\
        <a href=http://www.daum.net/>daum</a>\
        </span></div></body></html>"""

soup = BeautifulSoup(html, 'lxml')
soup
```

<pre>
<html><body><div><span> <a href="http://www.naver.com">naver</a> <a href="http://www.google.co.kr">google</a> <a href="http://www.daum.net/">daum</a> </span></div></body></html>
</pre>

```python
soup.find('a')
```

<pre>
<a href="http://www.naver.com">naver</a>
</pre>

```python
soup.find('a').get_text()
```

<pre>
'naver'
</pre>

```python
names = soup.find_all('a')[:]

for name in names:
    print(name.get_text())
```

<pre>
naver
google
daum
</pre>

```python
soup.find('span').get_text().split()
```

<pre>
['naver', 'google', 'daum']
</pre>

```python
soup.find('a').get('href')
```

<pre>
'http://www.naver.com'
</pre>

```python
[a.get('href') for a in soup.find_all('a')]
```

<pre>
['http://www.naver.com', 'http://www.google.co.kr', 'http://www.daum.net/']
</pre>

```python
html = """<html> <head><title>test site</title></head> <body> <div><p id="i" 
class="a">test1</p><p class="d">test2</p></div><p class="d">test3</p></p> <a>a tag</a> 
<b>b tag</b></body></html>"""

soup = BeautifulSoup(html, 'lxml')
print(soup.select('body p')) # body 태그에서 p태그 가져오기
print(soup.select('body .d')) # body 태그에서 클래스가 d인 태그 가져오기
print(soup.select('body p.d')) # body 태그에서 클래스가 d인 p태그 가져오기
print(soup.select('body #i')) # body 태그에서 아이디가 i인 태그 가져오기
print(soup.select('body p#i')) # body 태그에서 아이디가 i인 p태그 가져오기
print(soup.select('div p')) # div 태그에서 p태그 가져오기
```

<pre>
[<p class="a" id="i">test1</p>, <p class="d">test2</p>, <p class="d">test3</p>]
[<p class="d">test2</p>, <p class="d">test3</p>]
[<p class="d">test2</p>, <p class="d">test3</p>]
[<p class="a" id="i">test1</p>]
[<p class="a" id="i">test1</p>]
[<p class="a" id="i">test1</p>, <p class="d">test2</p>]
</pre>

```python
html2 = """
<html>
 <head>
  <title>작품과 작가 모음</title>
 </head>
 <body>
  <h1>책 정보</h1>
  <p id="book_title">토지</p>
  <p id="author">박경리</p>
  
  <p id="book_title">태백산맥</p>
  <p id="author">조정래</p>

  <p id="book_title">감옥으로부터의 사색</p>
  <p id="author">신영복</p>
 </body>
</html>
""" 
# id가 중복
soup = BeautifulSoup(html2, "lxml")
```


```python
soup.title
```

<pre>
<title>작품과 작가 모음</title>
</pre>

```python
soup.body
```

<pre>
<body>
<h1>책 정보</h1>
<p id="book_title">토지</p>
<p id="author">박경리</p>
<p id="book_title">태백산맥</p>
<p id="author">조정래</p>
<p id="book_title">감옥으로부터의 사색</p>
<p id="author">신영복</p>
</body>
</pre>

```python
soup.body.h1
```

<pre>
<h1>책 정보</h1>
</pre>

```python
soup.body.p
```

<pre>
<p id="book_title">토지</p>
</pre>

```python
soup.find_all('p')
```

<pre>
[<p id="book_title">토지</p>,
 <p id="author">박경리</p>,
 <p id="book_title">태백산맥</p>,
 <p id="author">조정래</p>,
 <p id="book_title">감옥으로부터의 사색</p>,
 <p id="author">신영복</p>]
</pre>

```python
soup.find('p', id="book_title")
```

<pre>
<p id="book_title">토지</p>
</pre>

```python
soup.find_all('p', {"id":"author"})
```

<pre>
[<p id="author">박경리</p>, <p id="author">조정래</p>, <p id="author">신영복</p>]
</pre>

```python
book_titles = soup.find_all('p', id='book_title')
authors = soup.find_all('p', id='author')

for i in range(len(book_titles)):
    print(book_titles[i].get_text()+'/'+authors[i].get_text())

for title, author in zip(book_titles, authors):
    print(title.get_text()+'/'+author.get_text())
```

<pre>
토지/박경리
태백산맥/조정래
감옥으로부터의 사색/신영복
토지/박경리
태백산맥/조정래
감옥으로부터의 사색/신영복
</pre>

```python
p = soup.find_all('p')

for i in range(len(p)):
    if i%2 == 0:
        print(p[i].get_text(), end='/')
    else :
        print(p[i].get_text())
```

<pre>
토지/박경리
태백산맥/조정래
감옥으로부터의 사색/신영복
</pre>

```python
soup.select('body h1')
```

<pre>
[<h1>책 정보</h1>]
</pre>

```python
soup.select('body p')
```

<pre>
[<p id="book_title">토지</p>,
 <p id="author">박경리</p>,
 <p id="book_title">태백산맥</p>,
 <p id="author">조정래</p>,
 <p id="book_title">감옥으로부터의 사색</p>,
 <p id="author">신영복</p>]
</pre>

```python
soup.select('p')
```

<pre>
[<p id="book_title">토지</p>,
 <p id="author">박경리</p>,
 <p id="book_title">태백산맥</p>,
 <p id="author">조정래</p>,
 <p id="book_title">감옥으로부터의 사색</p>,
 <p id="author">신영복</p>]
</pre>

```python
soup.select('p#book_title')
```

<pre>
[<p id="book_title">토지</p>,
 <p id="book_title">태백산맥</p>,
 <p id="book_title">감옥으로부터의 사색</p>]
</pre>

```python
soup.select('#author')
```

<pre>
[<p id="author">박경리</p>, <p id="author">조정래</p>, <p id="author">신영복</p>]
</pre>

```python
# ./ : 현재 파일 위치
```


```python
%%writefile "./html_example.html"
<!doctype html>
<html>
    <head>
        <meta dataset='utf-8'>
        <title>사이트 모음</title>
    </head>
    <body>
        <p id = 'title'><b>자주 가는 사이트 모음</b></p>
        <p id = 'contents'>이곳은 자주 가는 사이트들을 모아주는 장소입니다.</p>
        <a href="https://www.naver.com" class = "portal" id = "naver">네이버</a>
        <a href="https://www.google.com" class = "search" id = "google">구글</a>
        <a href="https://www.daum.net" class = "portal" id = "daum">다음</a>
        <a href="https://www.data.go.kr" class = "government" id = "data">공공데이터포털</a><br>
    </body>
</html>
```

<pre>
Overwriting ./html_example.html
</pre>

```python
f = open('html_example.html', encoding='utf-8')

html = f.read()
f.close()

soup = BeautifulSoup(html, 'lxml')
soup
```

<pre>
<!DOCTYPE html>
<html>
<head>
<meta dataset="utf-8"/>
<title>사이트 모음</title>
</head>
<body>
<p id="title"><b>자주 가는 사이트 모음</b></p>
<p id="contents">이곳은 자주 가는 사이트들을 모아주는 장소입니다.</p>
<a class="portal" href="https://www.naver.com" id="naver">네이버</a>
<a class="search" href="https://www.google.com" id="google">구글</a>
<a class="portal" href="https://www.daum.net" id="daum">다음</a>
<a class="government" href="https://www.data.go.kr" id="data">공공데이터포털</a><br/>
</body>
</html>
</pre>

```python
soup.select('a')
```

<pre>
[<a class="portal" href="https://www.naver.com" id="naver">네이버</a>,
 <a class="search" href="https://www.google.com" id="google">구글</a>,
 <a class="portal" href="https://www.daum.net" id="daum">다음</a>,
 <a class="government" href="https://www.data.go.kr" id="data">공공데이터포털</a>]
</pre>

```python
# 네이버와 다음 가져오기
soup.select('.portal') # body a.portal 에서 body와 a를 굳이 명시하지 않아도 된다.
# 네이버만 가져오기
soup.select('#naver')
```

<pre>
[<a class="portal" href="https://www.naver.com" id="naver">네이버</a>]
</pre>

```python
%%writefile "./html_example2.html"
<!doctype html>
<html>
  <head>
    <meta charset="utf-8">
    <title>줄 바꿈 테스트 예제</title>
  </head>
  <body>
  <p id="title"><b>대한민국헌법</b></p>
  <p id="content">제1조 <br/>①대한민국은 민주공화국이다.<br/>②대한민국의 주권은 국민에게 있고, 모든 권력은 국민으로부터 나온다.</p>
  <p id="content">제2조 <br/>①대한민국의 국민이 되는 요건은 법률로 정한다.<br/>②국가는 법률이 정하는 바에 의하여 재외국민을 보호할 의무를 진다.</p>
  </body>
</html>
```

<pre>
Overwriting ./html_example2.html
</pre>

```python
f = open('html_example2.html', encoding='utf-8')

html = f.read()
f.close()

soup = BeautifulSoup(html, 'lxml')
```


```python
title = soup.find('p', id='title')
contents = soup.find_all('p', id='content')

print(title.get_text())
for content in contents:
    print(content.get_text())
```

<pre>
대한민국헌법
제1조 ①대한민국은 민주공화국이다.②대한민국의 주권은 국민에게 있고, 모든 권력은 국민으로부터 나온다.
제2조 ①대한민국의 국민이 되는 요건은 법률로 정한다.②국가는 법률이 정하는 바에 의하여 재외국민을 보호할 의무를 진다.
</pre>

```python
br_content = soup.find('br')
br_content.replace_with('\n')
print(soup)
```

<pre>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8"/>
<title>줄 바꿈 테스트 예제</title>
</head>
<body>
<p id="title"><b>대한민국헌법</b></p>
<p id="content">제1조 
①대한민국은 민주공화국이다.<br/>②대한민국의 주권은 국민에게 있고, 모든 권력은 국민으로부터 나온다.</p>
<p id="content">제2조 <br/>①대한민국의 국민이 되는 요건은 법률로 정한다.<br/>②국가는 법률이 정하는 바에 의하여 재외국민을 보호할 의무를 진다.</p>
</body>
</html>

</pre>

```python
br_contents = soup.find_all('br')
for br_content in br_contents:
    br_content.replace_with('\n')

print(soup)
```

<pre>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8"/>
<title>줄 바꿈 테스트 예제</title>
</head>
<body>
<p id="title"><b>대한민국헌법</b></p>
<p id="content">제1조 
①대한민국은 민주공화국이다.
②대한민국의 주권은 국민에게 있고, 모든 권력은 국민으로부터 나온다.</p>
<p id="content">제2조 
①대한민국의 국민이 되는 요건은 법률로 정한다.
②국가는 법률이 정하는 바에 의하여 재외국민을 보호할 의무를 진다.</p>
</body>
</html>

</pre>

```python
def replace_newline(soup_html):
    br_to_newlines = soup_html.find_all("br")
    for br_to_newline in br_to_newlines:
        br_to_newline.replace_with('\n')
    return soup_html
```


```python
soup = BeautifulSoup(html, 'lxml')

content = soup.find('p', id='content')
content = replace_newline(content)
print(content.get_text())
```

<pre>
제1조 
①대한민국은 민주공화국이다.
②대한민국의 주권은 국민에게 있고, 모든 권력은 국민으로부터 나온다.
</pre>

```python
title = soup.find('p', id='title')
contents = soup.find_all('p', id='content')

print(title.get_text(), '\n')

for content in contents:
    print(replace_newline(content).get_text(), '\n')
```

<pre>
대한민국헌법 

제1조 
①대한민국은 민주공화국이다.
②대한민국의 주권은 국민에게 있고, 모든 권력은 국민으로부터 나온다. 

제2조 
①대한민국의 국민이 되는 요건은 법률로 정한다.
②국가는 법률이 정하는 바에 의하여 재외국민을 보호할 의무를 진다. 

</pre>

```python
url = 'https://www.wikipedia.org'

response = requests.get(url)

if response.status_code == 200:
    html = response.text
    soup = BeautifulSoup(html, 'lxml')
    title = soup.title.text # get_text()

    print(title)
else:
    print("Error : ", response.status_code)
```

<pre>
Wikipedia
</pre>
find - select



find, find_all : 태그 기반 (간단한 특성을 가지고 있을때) 정확하게 일치하는 경우



select, select_one : CSS(속성) 선택자 기반 (정교한 탐색이 가능) DOM 구조가 복잡하여 규칙이 복잡해질 때



find보다 select가 더 좋다. 가독성은 find가 더 좋을 수 있다.



```python
url = "https://search.naver.com/search.naver?query=yesterday+%EA%B0%80%EC%82%AC"

response = requests.get(url)

if response.status_code == 200:
    html = response.text
    soup = BeautifulSoup(html, 'lxml')
    lyrics = soup.select_one('div.intro_box > p.text') # > : 태그 바로 아래에 있다.
    print(replace_newline(lyrics).get_text())
else :
    print("Error : ", response.status_code)
```

<pre>
 너 아닌 척하지마 
 눈에 보이지만 
 넘어가는 걸
 What did you do yesterday
 곰인 척하지마
 여우인 걸 알지만
 넘어가는 걸
 What did you do yesterday
 What did you do yesterday
 What did you do do do do
 
 일찍 잔다는 너 
 요새 피곤하다며 
 열한 시간 동안 잠수더니 
 또 피곤하대
 무심코 내려놓은 듯한 폰은
 오늘도 덮어놓고는 
 내가 예민하대
 
 어디까지가 진실 어디까지가 
 거짓인지 어디 설명해봐
 넌 얼굴색 하나 안 변하고 
 육하원칙으로 반론해
 언제나 쏘아붙이면 대활 거부해 
 팩트를 들이대면 모르쇠
 Yesterday all my trouble 
 came close to me
 또 나만 호구된 청문회
 
 너 아닌 척하지마
 눈에 보이지만 
 넘어가는 걸
 What did you do yesterday
 곰인 척하지마 
 여우인 걸 알지만
 넘어가는 걸
 What did you do yesterday
 
 넌 너가 예쁜 걸 알아서 문제야 
 No no no no
 은근슬쩍 시선 즐기는 너 
 불안하게 왜 그래 
 No no no no
 
 프로필 사진은 왜 바꾼 거야
 옷은 왜 어제랑 똑같은 거야
 일부러 싫증 유발하는 거야
 끝장내고 싶은 거야 뭐야
 I Don't Know Why I Love You eh
 심증뿐인 추궁은 본전도 못 건졌네
 넌 내 머리 위 난 네 발 밑
 아쉬운 쪽이 손해 보는 싸움
 
 너 아닌 척하지마 
 눈에 보이지만 
 넘어가는 걸
 What did you do yesterday
 곰인 척하지마 
 여우인 걸 알지만
 넘어가는 걸 
 What did you do yesterday
 
 라리라라라 라라라라라
 돌고 도는 패턴이지만 
 라리라라라 라라라라라
 넘어가는 걸 
 What did you do yesterday
 
 알면서도 속아주는 거야 
 그만큼 널 좋아하는 거야 
 다만 들키지만 말아줘
 그럼 다 눈감아줄게 
 뭐든지 해줄게
 
 너 아닌 척하지마 
 눈에 보이지만 
 넘어가는 걸
 What did you do yesterday
 곰인 척하지마 
 여우인 걸 알지만
 넘어가는 걸 
 What did you do yesterday
 
 라리라라라 라라라라라
 돌고 도는 패턴이지만 
 라리라라라 라라라라라
 넘어가는 걸 
 What did you do yesterday 
</pre>

```python
title = input("title : ")
artist = input("artist : ")

url = 'https://search.naver.com/search.naver?&query=' + artist + title + '가사'

response = requests.get(url)
response
```

<pre>
title : yesterday
artist : 블락비
</pre>
<pre>
<Response [200]>
</pre>