---
layout: post
comments : true
title : "그래프 DB란? 개요와 특징, 관계형DB와의 차이점"
date : 2022-07-24
category : 'Text Analysis'
published: true
---


곧 시작하게 될 회사 프로젝트에서 텍스트 데이터를 지식 그래프(Graph Knowledge) 형식으로 시각화하는 방법을 검토해볼 일이 생겼다. 이걸 하려면 그래프 DB로 구현해야 한다는데, 관련해서 개인적으로 몇 가지 궁금한 점이 생겼다. 연관 키워드를 분석하는 시각화는 텍스트 분석 서비스들에서 종종 보이던데, 이걸 하는데에 꼭 그래프 DB를 이용해야 하나? 아니면 그래프 DB를 적용하면 기존에 불가능했던 어떤 점들이 가능해지는 건가?
<br>
아직 급한 일은 아니지만 미리 공부해두면 나쁘진 않을 것 같아서 그래프 DB에 대해 찾아보았다. 아래의 궁금증이 가장 컸는데, 일부는 해결된 것도 있고 아직 더 살펴보아야 하는 것들도 있다. 그래도 기록 차원에서 남겨둔다.  
<br>

#### Motivation
- 그래프 DB를 왜 써야 하는가? 이걸 쓰면 기존의 RDB(관계형 DB)와 다르게 무엇을 할 수 있게 되나? 어떤 점이 더 좋은가?
- Knowledge Graph 형식의 시각화는 RDB로는 구현할 수 없나? 일부 구현한 사례가 있어 보이던데, 한계점은? (구현 측면, 활용 측면)
- 실제로 데이터는 어떤 형식으로 저장하게 되나? 수집한 데이터를 RDB로 먼저 적재해 놓는다면, 어떻게 준비해두어야 그래프 DB로 옮겨가기에 수월한가? 또는 네트워크 시각화를 하기에 용이한가?
<br>

#### WHAT | 그래프 DB란?
아래 내용은 DataCamp의 <a href="https://app.datacamp.com/learn/courses/nosql-concepts">NoSQL Concepts</a> 코스의 4챕터 'Graph Databases'를 참고해 정리하였다.

- Graph DB란 무엇인가?
  - 네트워크를 점과 선으로 연결하는 방식으로 저장하는 DB이다. 
  - 각각의 데이터뿐만 아니라 데이터 간의 관계도 똑같이 중요하게 다루기 위해 고안되었다. 
    - 수학의 Graph 이론에 근거하고 있으며, <a href="https://rastalion.me/graph-db-%EA%B7%B8%EB%9E%98%ED%94%84-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4/">이 글</a>에 수학적 기원이 잘 정리되어 있음.  
<br/>
- 구성 요소는 노드(Node, 또는 vertice)와 엣지(Edge, 또는 link, arc)로 구성된다. 
  - 노드는 entity를 뜻한다. 서울, 대전, 대구, 부산 등 각각의 도시는 노드가 될 수 있다.
  - 엣지는 각 노드를 연결하고 노드 간의 관계를 정의한다. 서울과 부산을 오가는 항공편의 경로는 엣지가 될 수 있다.    
    - 또한 엣지는 property를 가질 수 있는데, 예를 들어 쿠팡과 우리집 사이에 배송이 이루어졌다면 이것은 구매일수도, 환불일수도 있다. 
    - 엣지에는 directed / undirected의 2종류가 있다. 
<br/>
- 삼성SDS 블로그에 실린 아래 이미지의 'Performance' 부분을 보고 RBD와의 차이점을 이해할 수 있었다. <br/>
  -  출처 : <a href="https://www.samsungsds.com/kr/insights/techtoolkit_2021_knowledge_graph.html">세상 모든 것들의 연결고리, 지식 그래프 안에 있습니다</a>
    <image src="https://image.samsungsds.com/kr/insights/knowledge_graph_img04.jpg" style="width: 800px, objectfit: cover"><br/><br/>
    

- 저장 방식
  - 모든 데이터를 하나의 큰 저장소에 저장한다. 즉, RBD처럼 여러 개의 테이블로 쪼개놓지 않는다.
    - RBD에서 10개 이상의 테이블을 조인하면 성능저하가 발생하지만, 그래프DB는 복잡한 연산도 효율적으로 가능
    - index free adjacency
    - 고정된 스키마가 없어 스키마를 유연하게 변경할 수 있다. 
  - 검색을 고도화할 수 있다. 
    - 인덱스 조회 문제나 테이블 조인 문제를 해결할 수 있다고 한다. (참고 : <a href="https://joey2the.tistory.com/m/80">Google Cloud Database Week - Day 2</a>)
    - 시작점을 정한 뒤 연결만 따라가면 되기 때문이다. 
<br/>
- 쿼리 방식
  - Cyper, Gremlin 등 그래프에 특화된 쿼리 언어를 사용한다. 
    -  예) 두 개의 키워드를 포함한 뉴스 기사를 검색하고자 할 때
    - (:Keyword)<-[:Include]-(:Article)-[:Include]->(:Keyword)
<br>
<br>

#### 그래프 DB의 장점/한계점
- 어떤 때 사용하면 좋은가?
  - 데이터 간의 연결이 매우 많을 때 사용하면 좋다.
    - 예를 들어 소셜 네트워크, COVID-19와 같은 질병의 감염경로를 모델링할 때 등
    - 장소와 거리를 표현할 수도 있다. 내비게이션 경로 찾기나 가까운 식당, 영화관, 병원을 추천해줄 때 등
  - 추천시스템 구현에 용이하다.
  - 실시간으로 이상 탐지(Fraud detection)가 필요할 때. 카드 사용 패턴, 여러명이 같은 IP 주소로 접속하는 것을 감지할 때 등
<br/>

- 사용이 적절하지 않은 경우
  - 일반적인 검색만 수행하는 애플리케이션에는 적절하지 않다. 특정한 시작점이 없이 전체 DB를 검색해야 하는 경우. 
  - entity property가 극도로 큰 값을 가진 경우. 
    - 예를 들어 BLOB(Binary Large Object, 멀티미디어 데이터를 다룰 때 사용), CLOB(Charset Large Object, 매우 긴 문자열데이터)
<br>
<br>


####   HOW | 사용해보려면 어떻게 해야 하나?
- 대표적인 Graph DB 업체인 Neo4j를 살펴보는 게 좋을 것 같다. (참고기사 : <a href="https://byline.network/2021/06/22-129/">그래프 DB가 뭐길래... Neo4j, 기업가치 2조원(2021.6)</a>)
- 제공 솔루션들
  - Neo4j Sandbox : 임시로 사용 가능한 클라우드 서비스. 생성 후 3일간 유지된다. (<a href="https://pizzathief.oopy.io/graphdb-neo4j-introduction">참조</a>>)
  - Neo4j Desktop, Enterprise Server, Community Server 중에서 다운받아 사용할 수 있다.
    - Neo4j Desktop : 셋업 방법을 보니 나같은 분석가/기획자가 써보기에는 DB연결에 어려려워 보이는데, 한번 해봐야 알 수 있을 것 같다. 다음 과제로 남겨둔다. 
  - Neo4j Bloom : 시각화 툴. 일단 써보고 싶었는데, 공식 문서를 보니 Enterprise 계정이 있어야 하는 듯 하다. (<a href=" https://neo4j.com/docs/bloom-user-guide/current/bloom-quick-start/">Bloom Quick Start</a>)
  - Neo4j Aura : 클라우드 DB 서비스이며, Google Cloud에 연결할 수 있다. (<a href="https://joey2the.tistory.com/m/80">참고 : Google Cloud Database Week - Day 2</a>)
- 결론 : 나는 간단히 구조만 파악해보고 싶은데, 우선은 Desktop을 설치해서 해봐야겠다. 
<br>  
<br>
    

#### 그래프 DB를 도입하지 않고, RDB를 바탕으로 지식 그래프를 구현할 수는 없나? 
- '네트워크 시각화'로 검색해보았다. python의 networkx 라이브러리로도 네트워크 시각화는 가능하다. 그 외에 시각화 가능한 JavaScript 라이브러리들이 몇몇 있다. 
- 실제로도 웹서비스로 제공되는 텍스트 분석 솔루션 중 썸트렌즈, 빅카인즈 등이 연관어 분석을 제공한다. 
  - 다만, 서로 비교할 검색어를 최대 3개까지만 입력할 수 있게 되어 있다. 
  - 성능 때문이던 구조 때문이던 유연하게 제공할 수 없는게 Graph DB와 RDB 기반으로 구현했을 때의 차이점인 것 같다. 
- 결론 : 못하는 건 아니지만, 한계가 있어 보인다. 
<br>
<br>


#### 마무리하며
- 다음 스텝으로는 일단 Neo4j Desktop을 설치해서 써봐야겠다. 
  - 의외로 네이버에서 검색해보니 친절하게 정리해둔 블로그들이 꽤 있다. 
- 비정형데이터, 특히 텍스트 데이터 분석에 활용한 케이스를 살펴보고 싶다. 
  - Google Cloud Platform 관련 세미나에서는 비정형 데이터 분석에 유리하다고 발표했던데, 한두번 시도가 아닌 상용화된 사례가 있는지 알고 싶다. 
  - https://neo4j.com/case-studies/
- 이 조사를 하는 동안 알게 되었는데, GraphQL은 그래프 DB와는 상관없는 프론트엔드 쪽 기술이었다.. 
<br> 
<br>

키워드 : graph dB, graph visualizations, Neo4j 
<br>
<br>

#### 참고 글
- DataCamep, <a href="https://app.datacamp.com/learn/courses/nosql-concepts">NoSQL Concepts</a>
- <a href="https://www.samsungsds.com/kr/insights/techtoolkit_2021_knowledge_graph.html">세상 모든 것들의 연결고리, 지식 그래프 안에 있습니다</a>
- <a href="https://rastalion.me/graph-db-%EA%B7%B8%EB%9E%98%ED%94%84-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4/">Graph DB? 그래프 데이터베이스</a>
- <a href="https://slownews.kr/82043">그래프 데이터베이스, 시장 현황과 분석 (2021.9, 슬로우 뉴스)</a>
- Neo4j 설치방법 총정리: https://blog.naver.com/kgh9080/222771221790 
- Neo4j 로컬 DBMS 만들기 https://blog.naver.com/kgh9080/222772603268 

