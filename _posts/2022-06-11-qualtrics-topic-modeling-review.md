---
layout: post
comments : true
title : "[번역] Topic Modeling in Text Analysis"
date : 2022-06-11
category : 'Essay'
published: true
---

회사 업무 때문에 고객 피드백 텍스트를 분석하는 해외 솔루션들을 살펴보고 있다. 그 과정에서 토픽 모델링과 관련해 이해에 도움되었던 글이 있어서 번역해보았다. 아무래도 영어로 쓰인 글이라, 한 문장씩 번역하다 보니 더 깊게 이해할 수 있었다. 

주의할 점 : 아래 글에서의 '토픽 모델링'은 흔히 텍스트마이닝 분야에서 얘기하는 LDA 기반의 topic modeling보다는, 좀 더 광의의 개념으로 사용되고 있다. 
<br/>
<br/>

<b>원문 : </b> <a href="https://www.qualtrics.com/au/experience-management/research/text-analysis/"> [Qualtrics] Topic modeling in text analysis </a>
<br/>

### Text Analysis 분야에서의 토픽 모델링 
- 텍스트 분석에서 데이터에 구조를 부여하는 방법 중 하나이다.
- Topic이란? 
    - topic 또는 category란, 유사한 개념이나 주제들의 그룹을 가리킨다. 
    - 예를 들어, '컬러가 딱 제 취향이에요'와 '세련된 색감이에요'에 사용된 단어는 다르지만, '컬러'라는 동일한 토픽으로 묶일 수 있음
- 토픽 모델링이란?
    - 서로 다른 토픽들을 이해할 수 있는 단일한 구조로 합치는 과정이다.
    - 그룹핑이나 위계 구조(hierarchical structure)는 없으나, 다중의 레이어를 가지는 경향이 있다.
    - 이러한 parent-child 토픽 그룹핑은 보통 Taxonomy라고 지칭한다. 
        - 유사한 토픽들을 특정 비즈니스 분야에서 의미있는 더 넓은 개념으로 묶어주는 것
        - 예를 들어 호텔 비즈니스에서 'Staff Experience' 카테고리는 직원들을 교육시키는 매니저와 관련이 있고, 'Room Experience' 카테고리는 하우스키핑 매니저에게 관련이 있다. 
<br/>


### 텍스트 분석에서 토픽 모델링할 때의 핵심 요소
<span style="color:grey">Key Elements of topic modelling in text analysis</span>

#### 1) 레이어의 개수
- 토픽 모델은 여러 개의 hierarchical level을 가질 수 있으나, 고객의 경험을 관리하는 Experience Management에서는 2개 레이어로 제한하기를 권한다.
- 2개 이상은 비즈니스 사용자가 탐색하고 이해하기에 너무 복잡함 (extremely complex to understand and navigate)
- 만들기도 어렵고, 시간이 지날수록 유지하기가 버거워짐
<br/>

#### 2) Exclusive Topic
- 하나의 문장이나 설문응답 내용은 여러 개의 토픽을 가질 수 있다.
- 이것은 토픽들이 mutually non-exclusive하다는 것을 뜻한다. (MECE하게 구분해야 하는 것과 반대 상황)
- 예를 들어 "My baggage loss was a cause of extreme frustration"은 2개의 감성 분석 토픽으로 분류될 수 있다.
    - 'Lost Baggage'와 'Emotion-Frustration'
<br/>

#### 3) Multi language
- 비즈니스 분야에 쓰인다면 다양한 언어를 커버해야 함.
- 일관성을 위해 단일한 모델을 사용해야 함
<br/>


### 토픽을 모델링하는 2가지 접근 방법
토픽 모델링을 하는 2가지 방식이 있다.

- Bottom up : 데이터셋으로부터 토픽을 추출한다.
- Top Down : 데이터셋과는 별개로 만들어져 있는 토픽을 적용한다.
<br/>

#### Bottom-up 토픽 모델링 방식
<b>1) 머신러닝 알고리즘</b>
- 좋은 텍스트 분석 소프트웨어라면 일반적으로 갖춘 기능이다. 
- 종종 레퍼런스 데이터셋을 활용하며, 사용할 수 있게 공개되어 있는 데이터인 경우가 많다.(리서치 아티클, 미디어 컨텐츠, 블로그 등) 언어학적으로는 훌륭한 접근법이라 하겠다.
- 그러나 당신이 분석하려는 데이터와 주제가 다르다면, 별 도움이 되지 않을 것이다. 토픽 모델을 개발함에 있어서 이런 학습 알고리즘만을 사용하는 것은 주의해야 한다. 
<br/>

<b>2) 통계적인 기법 (클러스터링)</b>
- 클러스터링 등의 고급 통계 분석을 적용해, 키워드의 출현 여부나 빈도에 기반한 top keyword 또는 키워드의 조합을 제안할 수 있다. 
- 가장 기초적인 접근법이지만, 경험 데이터를 분석할 때는 도움이 된다.
    - 특히, 고객 여정 상의 특정 터치포인트에서 수집되었고 데이터량이 많을 때

<b>3) 직접 쿼리하기</b>
- 가장 간단하면서 효과적인 방법이다.
- 데이터셋에 출현한 단어 빈도를 바탕으로 직접 토픽을 만드는 것
- 노동집약적이고 비효율적인 구시대적 방법이라서 제외될 수 있으나, 몇 가지 간단한 기술들을 적용하면 이 과정을 신속하게 단축시킬 수 있다.
<br/>

#### Top-down 토픽 모델링 방식
<b>1) Industry model</b>
- 미리 만들어진 taxonomy model을 적용할 수 있다.
- 많은 텍스트 분석 소프트웨어들이 산업별/주제별 모델을 제공한다. 다른 고객사나 비슷한 use case로부터 만든 것들이다.
- 토픽 모델링 연습의 시작점으로는 좋으나, 여기에만 의존해서는 안된다. 같은 분야의 기업이라 해도 비즈니스의 결이 저마다 다르고, 고객들이 제품/서비스/프로모션 등을 언급할 때 사용하는 언어도 완전히 다를 수 있다. 
- 모델의 재현율을 체크하는 것도 중요함
<br/>

<b>2) 도메인 경험을 바탕으로 직접 쿼리하기</b>
- bottom-up 모델링에서 언급한 직접 쿼리하기와 매우 유사함
- 실제 데이터에 기반하기보다는, 이미 가지고 있는 지식을 기반으로 하는 점이 다르다. 
- 경험 많은 비즈니스 사용자가, 고객들이 주로 언급하는 것으로 알고 있는 내용들을 서술할 수 있음
<br/>


### Our best-practice approach to modeling topics for text analysis
(역자 주. 이 글을 번역하게 된 핵심 목적이 아래 내용 때문이다.)
각 접근법 별로 장단점을 살펴봤지만, 텍스트 분석을 목적으로 직접 모델링을 해야 한다면 위 방법들을 조합하는게 가장 효과적이다.
고객의 경험이든 내부 지구언의 피드백이든, 아래 스텝별로 따라하면 효율적으로 토픽 모델링을 할 수 있을 것이다.

#### Step 1: Top Dwon with a pre-built model
미리 만들어둔 모델로 탑다운 방식으로 접근한다.
- 특정한 고객 접점으로부터 수집된 10만개의 verbatim에 대해, 데이터에 있는 모든 토픽을 분석해야 한다고 해보자. 가장 빠른 방법은 이미 만들어둔 모델을 적용하는 것이다. 2가지 방법이 있다.
<br/>
- Industry models : 텍스트 분석 소프트웨어가 산업별/주제별로 미리 만들어진 모델을 제공한다면 이를 적용할 수 있다. 
- Pre-configurated models : 조직 내에서 비슷한 use-case로 만들어 둔 모델이 있다면 사용할 수 있다. 텍스트 분석 소프트웨어가 다른 프로젝트의 모델을 적용할 수 있도록 지원하는지가 중요하다. 이 단계에서는 precision을 너무 걱정하지 않아도 된다. 
<br/>

#### Step 2: Bottom Up - Automatic Topic Detection
- 대부분의 텍스트 분석 소프트웨어는 데이터셋으로부터 주제를 탐지하거나, 자동으로 토픽을 집어 준다. 어떤 머신러닝이나 클러스터링 방식을 쓰던간에 말이다.
토픽 자동추천에 완전히 의존할 수는 없지만, 이것은 첫 단계에서 적용했던 모델을 보완하는 두 번째 단계로는 유용하다.
자동 생성된 토픽들을 잘 살펴보고, 흥미로워 보이는 것을 기존 모델에 추가한다.
<br/>

#### Step 3: Enhance Precision 정확률 개선하기
미리 만들어진 모델과 자동 생성된 토픽들은 이제 정확률을 손봐줘야 한다. 각 토픽을 살펴보면서 제대로 된 내용을 잡아서 태깅하고 있는지 살펴본다. 정확률이 괜찮은지 살펴보기 위해, 처음에는 각 토픽별로 15~20개의 verbatim을 체크해볼 것을 권장한다. 
<br/>
당신이 사용하는 텍스트 분석 소프트웨어가 편리한 UI를 제공하는지가 중요하다. 
1. 각 토픽을 선택해서 각각의 재현율을 체크하기가 쉬울 것
2. 각 토픽의 rule을 체크할 수 있을 것
3. 품사태깅된 내용을 체크할 수 있을 것
4. 룰을 수정할 수 있고, 수정에 따라 태깅된 verbatim의 갯수가 어떻게 변하는지를 체크할 수 있을 것
<br/>

#### Step 4: Increase the Recall 재현율 높이기
- 마지막으로 가장 중요한 단계는 모델의 재현율을 높이는 것이다. 
- 최소 1개의 토픽에 속하는 코멘트의 %를 높이는 수동 작업
- 2개의 접근법이 있다.

<b>1) 기존 토픽을 개선한다.</b>
- 모델에 이미 존재하는 토픽들을 개선하기 위해 유의어나 동의어를 더해줘야 할 수 있다. 
- 기존의 토픽 룰에 새로운 단어를 추가해야 하는데, 이 프로세스는 내용을 일일이 읽어보는 지루한 작업이 될 수 있다.
- 머신러닝이 도움이 될 수 있는데, 소프트웨어가 토픽에 이미 사용된 단어와 유사하게 언급된 단어들을 제안해준다면 이 과정을 훨씬 신속하게 처리할 수 있다. 

<br/>

<b>2) Untagged/Unknown Bucket으로부터 verbatim을 캡처하기 위해 추가적인 토픽을 생성하기</b>
- 진정한 바텀업 접근법이라면 verbatim에서 출발해 모델을 만들어야 하지만, 1만개의 리뷰를 다 읽을 수는 없음
- 그 대신, 워드 클라우드 등일 이용해 출현 빈도가 높은 단어를 살펴볼 수 있음
- 모델이 잡아내지 못한 단어가 무엇인지 쉽게 살펴보고, 다른 토픽에 더해져야 하는지 새로운 토픽을 생성해야 하는지를 판단할 수 있음


<hr style="border:none; height=0.5px;">
<br/>
#### 번역을 마치며

- 본문에 종종 등장하는 text analysis software를 제공하는 기업에서 쓴 글이라, 현실적으로 도움되는 내용들이었다. 하지만 그런 소프트웨어를 쓸 거라고 전제하고 글이 전개되어서 아쉬운 점도 있었다.

- 모델을 통해 자동 탐지된 주제들 외에, Untagged/Unknown Bucket에서 키워드 빈도로 주제를 더 건져올리라는 조언은 무척 유익했다. 실무에서 적용해 볼 계획이다. 

- 이 글을 게재한 Qualtrics는 2002년에 설립된, 업력이 20년된 회사이다. 고객 피드백 텍스트를 분석하는 해외의 text analysis software들이 가진 일반적인 기능들을 글을 통해 살펴볼 수 있어서 도움이 되었다. 추가로 더 조사해보기 위한 시작점이 될 수 있을 것 같다. 