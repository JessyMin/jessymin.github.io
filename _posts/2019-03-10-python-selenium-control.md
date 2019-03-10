---
layout : post
comments : true
title : "웹크롤링 시 Selenium을 이용한 브라우저 컨트롤 - 스크롤다운"
date : 2019-03-10
category : 'python'
---


특정 기업 홈페이지에서 상품명과 가격, 옵션을 크롤링하는 작업을 하고 있다. 웹사이트마다 만들어진 방식이 다양하다 보니, 주로 아래와 같은 작업이 필요했다.

1. 전체 상품 크롤링
  - 스크롤다운
  - 페이지네이션
2. 상세 상품정보 획득
  - 마우스오버 (hovering)
  - URL 통해 상세 페이지로 이동

이 중에는 for 문을 이용해 BeautifulSoup에서 해결할 수 있는 것도 있고, Selenium과 Webdriver를 이용하는 게 효율적인 것도 있을 것이다. 우선 이번 주에 진행한 Selenium을 사용한 스크롤다운 방법을 정리해본다.
이하 내용에서는 크롬 브라우저를 사용하였다.

<br>

### 개념 이해



#### 1. 웹드라이버를 이용해 브라우저 열기

**개념 이해**
- Webdriver는 코드를 이용해 웹브라우저를 제어할 수 있는 API이다.
  - 즉, 창을 하나 새로 띄우고,
  - 코드를 통해/또는 수동으로 제어하여(스크롤다운, 버튼 클릭 등)
  - 창의 내용을 크롤링할 수 있다.

**작업 순서**
- 본인이 사용할 웹브라우저(이하 크롬)을 제어하기 위한 드라이버를 다운받아 놓는다.
  - https://sites.google.com/a/chromium.org/chromedriver/downloads

- driver라는 이름의 webdriver 객체를 만들어 준다.
  - chrome, browser 아무거나 무관
- 웹드라이버를 이용해 크롬 브라우저를 열어준다.
  - 드라이버 경로는 폴더명/파일명으로 지정

``` python
# 크롬 브라우저 조작을 위한 드라이버
from selenium import webdriver

# 다운로드 받은 드라이버 경로 (폴더명/파일명)
DRIVER_DIR = 'driver/chromedriver'

# 크롬 브라우저를 실행/조작하기 위한 드라이버 객체 생성
driver = webdriver.Chrome(DRIVER_DIR)

```
여기까지 실행하면 '‘Chrome이 자동화된 테스트 소프트웨어에 의헤 제어되고 있습니다’는 메시지와 함께 텅 빈 브라우저가 뜨게 된다.

<br>

#### 2. 타겟 URL 내용 띄우기 (Webdriver)

다음은 크롤링할 문서를 브라우저에 띄울 차례다.

``` python
# 크롤링할 url의 내용을 가져옴
url = "http://www.elkay.com/sinks/kitchen-sinks"

driver.get(url)
```
제어 중인 브라우저에 해당 url의 문서가 뜬다.  


<br>

#### 3. 브라우저 컨트롤하기

#### 1) 버튼 클릭
크롤링하려는 내용이 여러 페이지에 걸쳐 있을 때, 페이지네이션에 다음 페이지로 이동하는 버튼을 클릭하는 방법이다.

```python
# 페이지네이션이 있는 웹문서
url = "http://www.elkay.com/sinks/kitchen-sinks#q=|100|0|1|"
driver.get(url)

# 버튼 클릭하기 ( class=right_arrow)
driver.find_element_by_class_name("right_arrow").click()
```

<br>


#### 2) 스크롤다운

스크롤다운에는 크게 3가지 시나리오가 있다.
(참고 : https://www.guru99.com/scroll-up-down-selenium-webdriver.html)

**- 특정 픽셀까지 스크롤다운 하기 (by pixel)**
``` python
driver.execute_script(“window.scrollTo(0, Y);”)
```
<br>

**- 맨 아래까지 스크롤다운 하기 (bottom of the page)**

```python
driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")
```
<br>

**- 특정 요소가 보일 때까지 스크롤다운 하기 (by the visibility of the element)**

``` python
element = driver.find_element_by_class_name("improve-site")

driver.execute_script("arguments[0].scrollIntoView(true);", element)
```

<br>

scroll down으로 검색하다 보면 아래와 같은 방식의 코드가 많이 보인다.
```python
WebElement element = driver.findElement(By.id("id_of_element"));
((JavascriptExecutor) driver).executeScript("arguments[0].scrollIntoView(true);", element);
Thread.sleep(500);
```
python에서 javascript를 실행하려니 생기는 복잡함인데, 위의 코드로 실행해도 모두 문제가 없다.

<br>


#### 향후 과제

내가 크롤링하려는 상품 리스트 페이지는 아래와 같이 동작한다.

- 화면에 12개의 상품이 보여진다.
- 화면에 특정 요소가 보일 때까지 스크롤다운하면 상품 리스트가 추가된다.

이 사이트 내에서 여러 상품을 크롤링해야 하는 상황이라 자동화할 수 있는 방법을 고민하고 있다.

페이지 맨 아래까지 스크롤다운하는 두 번째 방법을 사용하면, 브라우저 상으로는 맨 아래까지 바로 이동한 뒤 상품 리스트가 자동으로 계속 갱신된다. 문제는 만약 처음 보는 웹페이지라면 몇 초나 대기한 뒤 크롤링해야 하는지 판단할 수 없다는 점이다.

그래서 리스트가 갱신되는 기준이 되는 element가 보일 때까지 스크롤다운하는 방식에 loop를 적용해보려 한다. 모든 상품이 로딩되었고 더 이상 리스트 갱신이 일어나지 않는다는 걸 어떻게 판별할지가 이번 주 작업 내용이 될 듯 하다.


<br>



**참고한 글**
 - <a href="https://selenium-python.readthedocs.io/navigating.html"> 공식 문서 : Navigating/Interacting with the Page </a>

 - <a href="https://loadfocus.com/blog/2013/09/05/how-to-locate-web-elements-with-selenium-webdriver/">How to Locate Web Elements with Selenium WebDriver?</a>

 - <a href="https://beomi.github.io/2017/02/27/HowToMakeWebCrawler-With-Selenium/">Selenium으로 무적 크롤러 만들기</a>

 - <a href="https://medium.com/@peteryun/python-selenium%EC%9D%84-%ED%99%9C%EC%9A%A9%ED%95%9C-%ED%81%AC%EB%A1%A4%EB%9F%AC-%EB%A7%8C%EB%93%A4%EA%B8%B0-b055cefd1195">Selenium을 활용한 크롤러 제작 후기 </a>
