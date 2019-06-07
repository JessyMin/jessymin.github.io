---
layout : post
comments : true
title : "웹크롤링 시 xpath 사용하는 법"
date : 2019-06-07
category : 'python'
---

#### Motivation
Selenium을 활용해 인스타크램 웹뷰어 페이지를 스크래핑 해보는 중이다. HTML 구조가 깔끔하지 못해 class를 찾는 것만으로 원하는 데이터를 뽑아내지 못하는 경우가 생겼다. xpath를 이용하면 좀 더 쉽게 처리할 수 있을까 싶어 배워보기로 했다.

아래는 DataCamp의 <a href="https://www.datacamp.com/courses/web-scraping-with-python">Web Scraping in Python </a> 강의를 들으며 발췌/요약한 내용이다.
<br>

#### XPath Notation이란?
- HTML의 트리 구조를 네이게이팅하기 위해 사용
- HTML 속성을 활용해서 요소를 선택할 수 있다.
(select elements by their attributes using an XPath)
- 웹스크래핑 할 때의 제너럴한 방법
  - python뿐만 아니라 R 라이브러리도 대부분 지원
<br>

#### Xpath Notation 익히기
- forward-slash(`/`) : single generation을 탐색
- double forward-slash(`//`) : 모든 generation을 탐색
- square bracket(`[ ]`) : 특정 엘러먼트를 선택
- backward-slash(`\`)

```python
# 문서 내의 모든 <p> 찾기
xpath = '//p'

```

```python
# attribute id equals uid
'//div[@id="uid"]'
```
<br>

- 더블 슬래시 내용을 먼저 찾은 뒤 해당되는 모든 sibling에 브라켓을 적용함
  - 사실 이렇게 쓸 일이 많진 않음

```python
xpath = '//p[1]'
```

- 와일드카드 사용 가능
```python  
xpath = '/html/body/p/*'

```

- @ : 애트리뷰트
  - @id, @class, @href ...
```python
# p 중에서 class가 product인 것 찾기
xpath = '//p[@class="product"]'

# 전체에서 찾아내기
xpath = '//*[@id="uid"]'
```  

- contains 문법 사용하기
  - 포함된 것을 모두 찾음
  - `contains(@attri-name, "string-expr")`
```python
# 포함하는 모든 경우를 찾아냄
xpath = '//*[contains(@class, "class-1")]'

# 완전히 일치하는 것 찾기
xpath = '//*[@class="class-1"]'
```

<br>

#### 어떨 때 xpath를 써야 하나?
이 부분이 너무 궁금해서 구글링을 해봤는데, 셀레니움 커뮤니티에서도 늘 뜨거운 감자같은 문제라고 한다.

참고 글 :<a href="http://elementalselenium.com/tips/32-xpath-vs-css"> Elemental Selemiun : Css Vs. X Path</a>

CSS의 장점
- 가독성이 좋음
- 속도가 빠름 (특히 Explorer를 이용할 때)

Xpath의 장점
- 페이지를 가로질러(transverse) 스크래핑 할 수 있음 (CSS로 할 수 없을 때)

그런데 위의 글에서 벤치마킹한 결과를 보면 속도 차이는 거의 없다고... 허무한 결과지만, 일단은 편한대로 쓰면 될 듯 하다.
<br>


#### 참고링크
- <a href="https://www.popit.kr/web-scraping-by-selenium/"> Selenium 으로 Web Scraping 아이 해봤니? </a>
