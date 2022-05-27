---
layout: post
comments : true
title : "데이터 시각화 라이브러리 Highcharts"
date : 2022-05-26
category : 'Essay'
published: true
---

데이터 분석을 공부하다 보면 데이터 시각화와 관련해서 python의 matplotlib, seaborn과 R의 ggplot 정도를 흔히 접하기 마련이다. 그 외에 실무자 분들의 글을 통해 D3가 유명하는 정도만 알고 있었다. 

그런데 지금 팀에서 개발 중인 대시보드의 UI/UX 개선 작업을 하면서, 관련 웹프레임워크가 데이터 시각화에 Highchart를 사용하고 있는 것을 알게 되었다. 개발자 분들도 낯선 라이브러리인 듯 하고 일이 바쁘기도 해서, 먼저 API 문서를 검토해서 개선사항을 요청드리고 있다. 

검색하다 보니 사용하기 쉬운 웹 시각화 라이브러리라는 얘기가 있어서 제대로 익혀보고 싶어졌다. 하지만 많이 들어본 건 D3인지라, 이걸 깊이 파고들어 익혀두는게 좋을지, 아니면 업무에 필요한 부분만 파악하고 제대로 배우는 건 D3로 선택하는 게 좋을지 고민이 되었다. 그래서 Highchart에 대해 한번 공부하면서 수집한 사항들을 정리해 보았다. 
<br>


#### Highchart,  누가 왜 만들었나? 
- Pure JavaScript에 기반한 차트 라이브러리
- 2009년에 노르웨이의 Highsoft라는 회사에서 개발함 
- 사용자들이 웹에 직접 차트를 퍼블리시할 수 있게 하고자 하였음
    - "humble quest for a simple charting tool for updating his homepage"에서 시작됨 (<a href="https://www.highcharts.com/blog/about/">Our Story</a>)
    - 미션 : "Make it easy for everyone to visualize data on the web."
- 여러 매체에서 Top 10 JavaScript Charting Library로 선정됨
- Highcharts JS 외에 Highcharts Stock/Maps/Gantt도 있다.
<br>

북유럽에서 만들어졌다는 점은 의외였다. 위치도 노르웨이의 작은 마을에서 시작했다는데, 핀란드 기업들이 게임 잘 만드는 건 알았어도 노르웨이 스타트업은 처음 본다. 개인적으로 팀의 미션이 심플하고 마음에 든다. 리서치를 하면서 홈페이지에 들어가봤는데, 40명 정도 되는 팀의 모습도 무척 좋아보였다. 영어 자료가 많지 않지만 나중에 시간이 되면 CEO 인터뷰가 있는지 찾아보고 싶다. 
<br>

#### 장점 vs. 단점
구글링을 통해 장점/단점을 파악해보았다. 하지만 D3만큼 사용하는 사람이 많지는 않고 아서, 100% 신뢰하기는 어렵다. 
(<a href="https://www.trustradius.com/reviews/highcharts-2019-09-14-11-14-20">참고</a>)

#### 장점
- 렌더링 속도가 빠르다.
- 브라우저에 대한 호환성이 좋다.
- 한두개의 간단한 차트가 아닌, 많은 차트를 효율적으로 컨트롤하기 좋다 .
    - global theme을 적용할 수 있음
- 참고할 수 있는 예제가 많다.
    - 많긴 하지만 퀄리티가 높지 않다는 의견도 있음
- export 모듈이 있다.
    - 사용자가 차트 이미지를 다운로드 가능(PDF, PNG, JPG, SVG 포맷)
    - 데이터 다운로드 가능 (CSV, XLS, HTML 테이블 포맷)
<br>

#### 단점
단점에 대해서는 객관적인 분석 글을 아직 찾아내지 못했다. 내용이 단편적이고 의견들이 서로 상반되어서, 각자 자기 경험치 안에서 얘기한 것으로 이해된다. 
- 러닝커브가 다소 있다. (무시할만한 수준은 아님)
    - 하지만 D3에 비해서는 러닝커브가 낮다는 의견도 있음
- 상대적으로 복잡한 편이다. (무엇에 비해?...)
- D3에 비해서는 커뮤니티가 덜 활성화되어 있다. 
    - Github의 star/fork 수에서 압도적인 차이가 남
- 상업용은 무료가 아니다.
<br>


#### Highcart vs. D3 비교

이 부분은 둘 다 경험해본 뒤에 정리해봐야 할 것 같다. 가장 큰 차이는 커스터마이징이 어디까지 가능한가 하는 부분인 것 같다.  
- 'D3는 자유도가 높은 만큼 배우기 어려우나, Highchart는 자바스크립트를 사용해 봤다면 10분이면 만들 수 있다" (<a href="https://brunch.co.kr/@jihoonleeh9l6/31"> [3분] Highchart 사용해보기(1)</a>)
- '전에 보지 못했던 새로운 차트를 만들고 싶으면 D3를 써라'(<a href="https://bshell.tistory.com/548">링크</a>)
<br>

#### 결론
개인적으로 시각화는 기본에 충실한 게 좋다고 생각한다. 언론/미디어가 아닌 비즈니스 도메인에서 일하고 있어서일지도 모르겠다. 신기하고 눈길을 끄는 시각화보다는, 단순하고 익숙한 그래프가 경영진이나 관련 부서에 데이터의 의미와 메시지를 전달하는 데에는 더 효율적이었다.
그러니 지금 시점에서는 굳이 D3를 찾아서 배우기보다는, 바로 실무에서 써야 하는 Highchart를 충분히 배워두는 게 앞으로도 두루 활용하기 좋을 것 같다. 완전히 익숙해지고, 해보고 싶은 게 있는데 Highchart로는 한계가 느껴지면 그때 D3를 살펴보면 좋을 것 같다. 
<br>



<b>ps.</b> 아래 글은 데이터 시각화 라이브러리를 선택할 때 고려할 사항들을 언급하고 있다. 지금은 잘 이해되지 않는 항목도 있지만, 나중에라도 도움이 될 듯해서 같이 정리해둔다.
<a href="https://blog.logrocket.com/top-javascript-data-visualization-libraries-2021/">The top JavaScript data visualization libraries for 2021 </a>
1. 어떤 타입의 차트를 지원하는가?
2. 기능 (Features)
3. 렌더링 방법 (Rendering method)
4. 얼마나 인터랙티브한지 (Interactivity)
5. API가 사용하기 쉬운지
6. 커뮤니티 지원
</br>

#### <참고 글>
- <a href="https://beomdev714.tistory.com/2"> [Highcharts.js] 하이차트를 활용한 데이터 시각화 시작하기 </a>
- <a href="https://brunch.co.kr/@jihoonleeh9l6/31"> [3분] Highchart 사용해보기(1)</a>
- <a href="https://blog.logrocket.com/top-javascript-data-visualization-libraries-2021/">The top JavaScript data visualization libraries for 2021 </a>
<br>