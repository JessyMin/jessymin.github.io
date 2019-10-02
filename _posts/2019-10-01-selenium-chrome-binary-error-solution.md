---
layout : post
comments : true
title : "맥에서 Selenium 사용 시 Chrome driver 에러 해결"
date : 2019-10-01
category : 'web-scraping'
---

El Capitan을 사용하다가 맥북을 리셋할 일이 생겨 Mojave로 업데이트했더니, Selenium 설치 후 크롬 드라이버를 사용하려 하자 에러가 발생했다.

```python
from selenium import webdriver
chrome_path = "/usr/local/bin/chromedriver"
driver = webdriver.Chrome(chrome_path)
```

발생한 에러는 아래와 같다.

```python
selenium.common.exceptions.WebDriverException: Message: unknown error: cannot find Chrome binary
```

이에 대한 해결책을 구글링해보면 binary_location 옵션을 재설정하라는 해결책이 나온다.

하지만 Quora에 해당 질문의 답변 중 아래 내용이 간단하고 근본적인 해결 방법인 것 같아 기록으로 남겨둔다. (링크 : <a href="https://stackoverflow.com/questions/46026987/selenium-gives-selenium-common-exceptions-webdriverexception-message-unknown">Selenium gives “selenium.common.exceptions.WebDriverException: Message: unknown error: cannot find Chrome binary” on Mac</a>)

<br>

### 해결책

1. 크롬을 언인스톨한다.

2. 홈브루를 이용해 크롬 드라이버를 설치한다. : `brew cask install chromedriver`

3. 홈브루를 이용해 크롬을 다시 설치한다. : `brew cask install google-chrome`

이렇게 하면 크롬과 크롬 드라이버가 같은 위치에 설치되어 binary-location 문제가 해결된다.

- Quora 답변자 사용 버전 : MacOS 10.13.4
- 이 글을 쓰면서 테스트한 버전 : MacOS 10.14.6
