---
layout : post
comments : true
title : "파이썬 업무자동화 : 자동실행과 메일/슬랙 메시지 보내기"
date : 2019-03-24
category : 'python'
---


#### 들어가며

업무시간에 떳떳하게 코딩하려고 웹크롤링을 시작했더니, 마케터를 위한 업무자동화 같은 패스트캠퍼스 커리큘럼이 눈에 들어오기 시작했다. 제한된 업무시간을 활용하다 보니 나 역시 일의 효율을 높여주는 '업무자동화'로 접근하게 되던 참이었다. 해당 커리큘럼들에선 특히 아래와 같은 사용씬들이 눈에 들어왔다.

- 상품 정보, 가격현황을 빠르게 수집하기
- 소비자 반응 파악을 위해 다양한 참여도 데이터 수집 (게시글, 댓글, 평점, 리뷰 등)
- 커뮤니티에서 우리 브랜드/제품명이 포함된 게시글을 빠르게 수집
- 네이버 뉴스에서 브랜드 관련 보도자료 수집
- 네이버 카페에서 검색어로 특정 카페와 게시판 선택, 해당 게시글 수집하기
- 인스타그램에서 검색어로 해시태그 및 이미지 수집하기
(출처 : <a href="https://www.fastcampus.co.kr/mktg_camp_crawling/">패스트캠퍼스, 인사이트를 찾는 고객반응 수집 자동화 CAMP 커리큘럼</a>)

그래서 마케터나 그로스해커를 타겟으로 하는 커리큘럼들을 교재삼아 배워보기로 했다. 현재 내 업무에선 ad-hoc 분석이 주를 이루지만, 지정한 업무자동화라면 자동실행이 핵심이니 어떻게 하는지를 알아보기로 했다.
<br>


### 자동실행

가장 궁금했던 부분이었다. 24시간 돌아가는 서버에 띄워둬야 하리라는 건 어렴풋이 알지만, 일반 직장인들이 모두 그렇게 하긴 어렵기 때문이다. 검색을 좀 해보니 학습비용을 적게 들이고 해볼 수 있는 방법들이 있었다.

#### 1. Windows
- Task Schedular 이용
- 참고 : <a href="https://fnmj.tistory.com/19">작업스케줄러를 이용한 파이썬 프로그램 자동 실행</a>
- `.py` 파일만 준비되어 있으면 됨. 간단함


#### 2. Mac/Unix
- crob 이용
- 쉽다고들 하지만, 막상 해보니 학습비용이 좀 있었다.
- 참고 : <a href="https://tariat.tistory.com/364">파이썬 자동화, 파이썬 코드 자동 실행하기</a>

<br>

### 크롤링 결과 리포팅

업무자동화의 핵심은 내가 일일이 챙기지 않아도 알아서 일이 돌아간다는 점이다. 그리고 매일의 결과 혹은 특이사항을 보고받아야 자동화라 할 수 있을 것이다. 그래서 결과를 전달하는 방법이 궁금했다. 크게는 아래와 같은 방법들이 있을 것 같다.

- 크롤링한 결과 DB에 쌓기
- 메일/메신저로 보내기
- 대시보드에 띄우기


실무에 계신 분들이 활용하시는 방법이 더 있을 것으로 생각된다. 일단 나는 당장, Learning by doing을 위해 가장 쉬운 것들을 찾아서 해보았다.  

<br>

#### 1. 슬랙으로 메시지 보내기

슬랙 챗봇을 사용하기 위한 과정이 필요하다. 아래 글을 참고했다.

- <a href="https://namkisec.tistory.com/entry/%EB%B3%B4%EC%95%88%EA%B8%B0%EC%82%AC-%EB%89%B4%EC%8A%A4-%ED%81%AC%EB%A1%A4%EB%A7%81-%ED%9B%84-%EC%B1%97%EB%B4%87%EC%97%90-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%8D%BC%EB%B6%93%EA%B8%B0Slack">보안기사 뉴스 크롤링 후 챗봇에 데이터 퍼붓기(Slack)</a>

1) 슬랙 앱 생성
2) 봇 생성
3) Access Token 발급
4) 슬랙봇의 권한 설정


다음부터는 단순히 메시지를 보내는 간단한 버전의 코드이다.
`test_slack.py` 파일을 만들어, 커맨드에서 실행했다. 덕분에 처음으로 jupyter notebook을 벗어나서 python을 써봤다.(패키지 설치할 때 빼고)

```python
from slacker import Slacker

# 토큰 설정 : Bot User OAuth Access Token
token = "[Bot User OAuth Access Token]"
slack = Slacker(token)

slack.chat.post_message("#random", "Hi, I am spider.")
```

- 토큰 설정에는 Bot User OAuth Access Token을 이용해야 생성한 봇 이름으로 메세지 발송됨
- OAuth Access Token를 이용하면 내 이름으로 메시지 발송

<br>

#### 2. 메일 발송하기

구글의 SMTP 서버를 이용하는 방법이 가장 간단한 듯 하다. 역시 앱 설정을 해주는 단계가 필요하다. 아래 글이 상세히 다루고 있다.

참고 : <a href="https://yeolco.tistory.com/93">파이썬으로 이메일 보내기(SMTP)</a>

글을 검색하다 보면 smtplib 등을 설치하라는 경우가 있는데, 최근 버전 python 모두 내장되어 있는 것 같다. 아래 코드만으로 실행 가능하다.

```python  
import smtplib
from email.mime.text import MIMEText


#smtp 인스턴스 생성 (서버url, port)
smtp = smtplib.SMTP('smtp.gmail.com', 587)

# TLS 보안 시작
smtp.starttls()

# 서버 로그인을 위해 login 함수 호출
smtp.login('지메일 계정(gmail@gmail.com)', '앱 비밀번호')

# 보낼 메시지
message = MIMEText('본문 : Hello')
message['Subject'] = '제목 : 테스트'

# 메일 보내기
smtp.sendmail('보내는 이메일 계정', '받는 이메일 계정', message.as_string())

# 세션 종료
smtp.quit()

```


#### 3. 기타
라인 메신저로 보내기
https://tibsender.tistory.com/76

<br>


#### 느낀 점
- 생각보다 간단해서 놀랐다. 엑셀이 직장인의 기본기가 된 것처럼, 코딩과 업무자동화도 그렇게 될 것 같다는 걸 확실히 느꼈다.
- 검색을 하다 보니, '생활 속의 불편함은 왠만하면 프로그래밍으로 해결하는 편'이라는 어떤 분의 글이 인상적이었다. 5월달 레고 할인을 기다리고 있는데 이참에 나도...? 가격 모니터링 자동화를 해볼까 싶다.
- 상품 가격 모니터링은 이커머스 회사들도 필요로 하는 부분일 듯. 특히 Dynamic pricing이 화두가 될수록..
