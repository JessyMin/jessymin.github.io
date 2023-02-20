---
layout: post
comments : true
title : "Selenium 버전 4에서 달라진 점"
date : 2023-02-20
category : 'web-scraping'
published: true
---

**Motivation**
개인적으로 분석해 보고 싶은 주제가 생기면 웹사이트를 크롤링해 텍스트 데이터를 수집하는 경우가 있다. 처음에는 BeautifulSoup을 쓰다가 페이징, 스크롤다운 등 브라우저를 제어할 필요가 있어서 Selenium을 섞어서 쓰고 있다.

그런데 작년에 셀레니움을 쓰려고 보니 버전 4로 업그레이드되어 이전에 짜둔 코드가 작동하지 않았다. 검색을 해서 당장 필요한 건 해결했지만, 셀레니움 3에서 4로 넘어가면서 달라진 부분들을 제대로 이해하진 못한 상태였다. 특히 라이브러리를 import하는 부분의 코드가 많이 달라져서, 한번은 공부해봐야겠다는 생각이 들었다. 그래서 이번에 사이드 프로젝트를 시작하는 김에 셀레니움 4에 대해 한번 이해하고 넘어가기로 했다.
<br/>


#### What 셀레니움이란?
데이터를 분석하는 입장에서는 주로 웹크롤링을 하는 과정에서 셀레니움을 접하게 되는데, 원래는 웹 테스트 자동화 툴로 개발되었다고 한다.

위키피디어에서는 'software testing framework'라는 표현을 쓰고 있고, 정확한 설명은 아래와 같다.

- '셀레니움은 브라우저 자동화(browser automation)을 지원할 목적으로 만들어진 툴과 라이브러리들에 대한 오픈소스 프로젝트이다. 테스트 스크립트 언어를 배우지 않고도 대부분의 모던 웹 브라우저에서 기능 테스트를 할 수 있게 해준다.' 
- 웹 애플리케이션 테스트 자동화의 개발을 지원하기 위해 여러 구성요소가 특정한 역할을 맡고 있다. 

구성요소는 아래와 같다. 크롤링을 할 때는 Webdriver만 접해왔는데, 전체 구성이 궁금하니 위키를 기준으로 핵심 개념만 살펴보았다.

- Selenium IDE : 셀레니움 테스트를 위한 통합 개발환경. 기능 테스트를 녹화, 편집, 디버깅할 수 있다. 
- Selenium client API : 다른 프로그래밍 언어로 작성한 테스트를 이 API의 메소드를 사용해서 셀레니움과 커뮤니케이션할 수 있었다. 셀레니움 2부터는 WebDriver라는 새로운 Client API를 제공하고 있다. 
- Selenium Remote Control : Java로 만들어진 서버이다. 셀레니움 2부터는 웹드라이버로 인해 deprecated 되었다. 
- Selenium WebDriver : 웹드라이버는 명령을 받아서 브라우저에 보내준다. 대부분의 브라우저 드라이버들은 실제로 브라우저 어플리케이션(Firefox, Google Chrome, Safari, Edge) 등을 런치하고 접근한다.
- Selenium Grid : 원격지 기기에서 실행되는 웹브라우저를 테스트하게 해주는 서버이다. 서버가 hub 역할을 하고, 웹드라이버는 node가 된다.

(출처 : https://en.wikipedia.org/wiki/Selenium_(software)

크롤링에 참조할 목적으로 셀레니움 4에 대해 구글링을 해보면, 영어 문서는 테스트 자동화에 대한 글들이 대다수를 이루고 있다.  

어쨌든 핵심은 브라우저를 제어한다는 점이고, 내 경우 여러 페이지를 넘기면서 수집해야 하거나 무한 스크롤되는 리스트를 스크롤다운해야 하는 경우에 셀레니움이 필요했다. 

<br/>


#### Why 왜 셀레니움 4를 사용하려 하나? 무엇이 더 좋아졌나?
셀레니움 4 업데이트는 2021년 10월에 공개되었다고 한다. 별 생각없이 pip install selenium으로 설치하면 이전에 작성해 둔 코드가 작동하지 않는데,  크롤링에 쓰이는 문법이 바뀌었기 때문이다. 

하지만 바뀐 문법을 새로 배우는게 귀찮지 않을만큼 강력한 강점이 있다. 셀레니움 4에서는 웹드라이버 매니저를 이용할 수 있는데(WebDriver Manager), 이건 바뀐 문법을 찾아서 코드를 고치는 걸 감수할 만큼 강력한 장점이다.  

이전에는 브라우저 버전과 OS에 맞는 드라이버를 수동으로 직접 다운로드 받고, 코드 내에 파일이 위치한 경로를 지정해줘야 했다. 그래서 나처럼 1년에 1~2번 크롤링을 하느라 이전 코드를 재사용하거나, 다른 컴퓨터에 셀레니움을 다시 설치하는 경우, 크롬 버전을 확인하고 드라이버를 다시 찾아야 하는 번거로움이 있었다.

하지만 셀레니움 4에서는 간단히 아래 2가지만 실행하면 된다.
- Webdriver manager를 설치한다.
- 코드 앞부분의 선언하는 내용을 작성해 놓고, 재사용한다. 
<br/>


#### How 셀레니움 4는 버전3과 무엇이 달라졌나? 어떻게 사용해야 하나?
테스트 자동화에 대해서는 잘 알지 못하므로, 웹크롤링에 관련된 부분만 확인하고 정리해 보았다.

**1. 설치하기**

셀레니움을 설치할 때, 웹드라이버도 함께 설치한다. 

```python 
pip install selenium
pip install webdriver_manager
```

만약 셀레니움을 사용한 적이 있다면, 버전은 아래와 같이 확인할 수 있다.
```python
import selenium
print(selenium.__version__)
```
<br/>



**2. 설정하기**

공식문서 : <a href="https://www.selenium.dev/documentation/webdriver/getting_started/install_drivers/#2-driver-management-software">Install browser drivers</a>

아래는 공식 문서를 번역한 내용이다. 

1) WebDriver Manager를 임포트한다.

```python
from webdriver_manager.chrome import ChromeDriverManager
```

2) 매니저가 사용한 위치를 install()로 얻어와서, service 클래스 인스턴스에 넘겨준다.
전에는 드라이버가 위치한 경로를 인자로 직접 전달했으나, Service 객체를 전달하는 것으로 바뀌었다. 
```python
service = ChromeService(executable_path=ChromeDriverManager().install())
driver = webdriver.Chrome(service=service)
```
Webdriver.Chrome() 함수가 Service 객체를 (파라미터로) 전달한다. 

<br/>
검색을 하다보면 간결하게 줄인 코드도 보이지만, 구조를 이해하기 위해 공식문서의 코드를 먼저 옮겼다. 아래는 좀더 간결하게 정리된 코드다.
```python
import selenium
from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager
from selenium.webdriver.chrome.service import Service

# 제어할 브라우저를 띄운다.
driver = webdriver.Chrome(service = Service(ChromeDriverManager().install()))

# 원하는 url에 접속한다. 
url="http://www.naver.com"
driver.get(url)
```
<br/>

어떤 블로그에서 아래와 같은 코드도 발견해 따라해보았다. 바로 경로값을 넘겨줘도 아직 작동은 하지만, Service 객체를 이용할 것을 권장하고 있다. 
```python
import selenium
from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(ChromeDriverManager().install())

<stdin>:1: DeprecationWarning: executable_path has been deprecated, please pass in a Service object
```
(참고 : 코드잇 Q&A https://www.codeit.kr/community/threads/33729)
<br/>

**2. 웹 Element에 접근해서 값 가져오기**
By를 사용하는 것으로 문법이 바뀌었기 때문에, 아래와 같이 먼저 선언해야 한다.

```python
from selenium import webdriver
from selenium.webdriver.common.by import By

# 클래스명이 'goods_name'인 elements를 모두 가져온다. 
elements = driver.find_elements(By.CLASS_NAME, 'goods_name')

# 모두 출력
for e in elements:
    print(e.text)

# 첫 번째 값만 출력
print(name[0].text)
```
공식문서 : <a href="https://www.selenium.dev/documentation/webdriver/elements/finders/">Finding web elements</a>
<br/>

기존의 문법과 셀레니움 4의 문법을 비교해보면 아래와 같다.
```python
# 기존 
driver.find_elements_by_class_name('goods_name')

# 셀레니움 4
driver.find_elements(By.CLASS_NAME, 'goods_name')
```


전체 메소드 목록은 아래 문서에서 확인할 수 있다. 순서는 내가 사용해보았거나 익숙한 순서로 재정렬한 것이다.
```python
driver.find_element(By.ID, "id")
driver.find_element(By.NAME, "name")
driver.find_element(By.TAG_NAME, "tag name")
driver.find_element(By.CLASS_NAME, "class name")
driver.find_element(By.CSS_SELECTOR, "css selector")
driver.find_element(By.XPATH, "xpath")
driver.find_element(By.LINK_TEXT, "link text")
driver.find_element(By.PARTIAL_LINK_TEXT, "partial link text")


# fruits ID 안의 tomatoes 클래스를 가져온다.
driver.find_element(By.CSS_SELECTOR,"#fruits .tomatoes")

```
참조 : https://selenium-python.readthedocs.io/locating-elements.html
<br/>


**3. 그 외 브라우저 제어 (클릭, 텍스트 입력 등)**
텍스트를 수집할 때 참고할 용도로, 자주 사용하는 것만 우선 찾아보았다. 


```python
# Keys를 선언한다.
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys


# 검색창에 텍스트 입력하기
SearchInput = driver.find_element(By.ID, "query")
SearchInput.send_keys("파이썬")

# '검색' 버튼 클릭하기
driver.find_element(By.CLASS_NAME, 'schBtn').click()

# 입력된 텍스트 지우기
SearchInput.clear()


# 또는 한번에 하기 : 검색창에 단어를 입력하여 검색하기
driver.find_element(By.ID, "query").send_keys("파이썬" + Keys.ENTER)


```
공식문서 : <a href="https://www.selenium.dev/documentation/webdriver/elements/interactions/">Interacting with web elements</a>
<br/>


#### 마무리하며
- Webdriver Manager로 바뀐 부분이 어렵게만 느껴졌는데, 공식 문서와 국내 블로그를 오가며 자꾸 보다보니 조금은 알 것도 같다. 그 외에 셀레니움 4에서 달라진 점으로 브라우저 내에 새 창을 열 수 있다, 등의 장점도 있던데 사이드 프로젝트를 하면서 찾아 보아야겠다.
- find_elements 했을 때 반환되는 객체를 다루는게 늘 어렵다. 아직 머릿속에 개념이 잘 정립되지 못한 것 같다. 공식문서에 관련된 내용이 있는지도 다음엔 확인해보자.
