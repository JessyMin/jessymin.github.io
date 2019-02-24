---
layout : post
comments : true
title : "python 웹 크롤링/스크래핑 라이브러리"
date : 2019-02-24
category : 'python'
---



### 들어가며

도저히 개인적으로 포트폴리오 만들 시간을 내기 어려워, 회사에서 업무에 관련된 내용을 웹크롤링/분석하는 방법을 시도해보기로 했다. 점심시간과 자투리 시간을 떳떳하게 사용하는 것이다.

개인이 취미로 코딩할 때와 실제 업무에서 할 때의 가장 큰 차이는, 시간 효율이 중요해지고 업무량과 소요시간을 예측할 수 있게 된다는(할 수 있어야 한다는) 점일 것이다. 내 경우는 R을 이용해 크롤링해본 적은 있지만 python은 처음이라 어떤 라이브러리를 써야 할 지 고민이 되었다. 학습비용을 최소화하기 위해서다.

- 귀동냥으로 들어온 `beautifulsoup`.
- 어떤 브런치 글에서 여러 페이지를 크롤링하느라 썼다는 `selenium`.
- 이도저도 모르겠어서 DataCamp 강의를 찾아봤더니 주력으로 가르치는 `scrapy`.

시행착오를 줄이기 위해 출퇴근시간을 이용해 간단히 사전조사를 해보기로 했다. 아래는 그 결과를 정리한 내용이다. (처음 공부하는 입장이라 잘못 이해하거나 번역한 내용이 있을 수 있으니 오류를 발견하시면 거침없이 피드백 부탁드립니다!)


<br>

### 웹 크롤링 vs. 웹 스크래핑

#### 웹 스크래핑
- 웹사이트의 내용을 긁어오는 것
- 긁어온 HTML 소스로부터 원하는 데이터를 추출하기 위해 **파싱** 을 하게 된다.

#### 웹 크롤링
- 사전적으로만 말하면 '웹 크롤러가 하는 일'에 해당된다. 크롤링 봇, 구글봇 등을 생각하면 개념 이해가 쉽다.
- 검색엔진 등은 데이터의 최신 상태를 유지하기 위해, 크롤러를 만들어 링크를 통해 웹페이지들을 자동으로 탐색하게 한다.
- 텍스트 분석의 관점에서 보면 좀 더 조직적이고 자동화된 방법으로 여러 문서들을 수집하는 것이며, 스크래핑을 포괄하는 개념이다.


주로 쓰이는 라이브러리에는 beautifulsoup, selenium이 있고, 크롤링 프레임워크로 scrapy가 있다.

<br>

참고 :
https://blog.michaelyin.info/web-scraping-framework-review-scrapy-vs-selenium/



### 1. Beautifulsoup
- 웹스크래핑에 가장 널리 쓰이는 python 라이브러리
- HTML/XML 파일을 파싱 / 데이터 추출
  - parser는 원하는 것을 사용한 뒤 beautifulsoup을 이용해 parser tree로부터 필요한 데이터를 추출할 수 있음
- 웹페이지를 다운로드할 때 `urlib2` 나 `requests`를 사용해야 함
- 배우기가 매우 쉽다. 낮은 러닝커브.
- 생태계는 잘 발달되어 있지 않음. 관련 프로젝트나 플러그인 적음.
- 속도를 향상시키려면 `multiprocessing` import 필요
- 참고 : <a href="https://blog.michaelyin.info/scrapy-tutorial-1-scrapy-vs-beautiful-soup/"> Scrapy Tutorial #1: Scrapy VS Beautiful Soup</a>

<br>

### 2. Selenium
- 웹 애플리케이션의 테스팅을 자동화하기 위해 만들어진 툴이다.
  ("Selenium automates browsers" : https://www.seleniumhq.org/)
  - Javascript를 조작할 수 있음
- 하지만 어쨌든, 쉽고 직관적이라서 크롤링에 많이 쓰이는 듯하다.
- 긁어오려는 컨텐츠가 HTML보다는 JavaScript로 문서에 더해진 경우에 적합하다.
  - 예를 들어, 컨텐츠를 로딩하기 위해 Javascript가 웹 API를 호출하는 경우

일반적인 정적 HTML 문서라면 HTTP 라이브러리(`requests` 또는 내장된 `urllib.request`)와 `lxml` 또는 `beautifulsoup`을 쓰는 것이 좋다. selenium을 쓸 때의 단점은 아래와 같다.
(참고 : <a href="https://stackoverflow.com/questions/17436014/selenium-versus-beautifulsoup-for-web-scraping"> Selenium versus BeautifulSoup for web scraping(stackoverflow) </a>)

- 불필요한 광대역 사용 : stylesheets, scripts, images 등을 모두 가져옴
- `request`를 쓰는 것보다 안정성과 에러복구의 용이성이 떨어짐
- CPU와 메모리 사용량이 상대적으로 많음

간단히 사용해 본 소감으로도, 처음에 driver가 해당 웹페이지를 브라우저에 띄울 때 시간이 꽤 걸리는 느낌이다. 급하다고 다음 코드를 바로 실행시켜도 웹페이지가 완전히 뜬 다음이 아니면, 제대로 추출되지 않는다.

<br>

### 3. Scrapy  

- `spider`라는 크롤러를 만들기 위한 웹크롤링 프레임워크
  - Django를 다뤄봤다면 유리하다.
  - 프레임워크를 다뤄본 경험이 없다면 다소 부담스러울 수 있다.
- 가파른 러닝커브
  - Scrapy 전문가가 되려면 좀 많이 노력해야 함
  - 하지만 학습비용이 아깝지 않을 만큼, 확장성이 뛰어남
- 웹사이트를 스크래핑하면서 발생하는 이슈들에 대한 내장 솔루션 제공
  - 리다이렉션, 특정 타입의 리퀘스트를 반복하기, HTTP 캐싱, 중복된 리퀘스트 필터링하기, 여러 리퀘스트에 대해 세션/쿠키 보존하기 등
  - 참고 : <a href="https://www.quora.com/How-do-I-choose-between-using-Beautiful-Soup-or-Scrapy-It-seems-using-frameworks-like-Scrapy-could-address-many-issues-like-IP-blocking-and-data-storing-easier-than-using-a-pure-parsing-library-like-Beautiful-Soup"> How do I choose between using Beautifulsoup or Scray? </a>
- 비동기 네트워킹 라이브러리인 Twisted에 built on 되어, 퍼포먼스가 매우 뛰어남
  - 같은 작업을 할 때 Selenium에 비해 매우 빠를 수 있음  
- 데이터 추출에 CSS selector와 XPath 모두 지원
  - Scrapy spider 내에 beautifulsoup 사용 가능
- javascript를 건드려야 한다면,
  - 번거롭지만 방법이 없는 것은 아님
  - 긁어올 데이터가 많은 ajax/pjax 리퀘스트 후에 나타나는 경우라면 selenium을 쓰는 걸 권장

<br>



<br>


### 마치며

- 당장의 업무에는 이미 손대기 시작한 selenium으로 충분할 것 같다.
- 하지만 아무래도 장기적인 관점에서 scrapy를 배워두면 좋을 것 같다. SNS나 앱 리뷰 등을 모니터링하려면 scrapy로 파이프라인을 구축해두는 게 필요할 듯.
- 지금 내가 필요로 하는 웹페이지의 크롤링에 Javascript가 영향을 미치는지 확인하기 위해, beautifulsoup으로 살짝만 트라이해봐야 겠다.
