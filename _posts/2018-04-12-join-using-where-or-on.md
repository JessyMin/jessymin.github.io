---
layout : post
comments : true
title : "[번역] WHERE이나 ON을 이용해 필터링하기"
date : 2018-04-11
category : 'SQL'
---

아래 내용은 Mode Analytics 웹사이트의 SQL 튜토리얼 중 WHERE이나 JOIN ON을 이용한 필터링에 관한 내용을 번역한 것이다.

세줄요약 :
* `ON`에 사용한 필터링 조건은 `JOIN`이 일어나기 전에 적용된다.
   따라서 한 테이블에만 적용시키는 `WHERE`의 용도로 쓸 수 있다.
* `WHERE`은 두 테이블이 먼저 `JOIN`된 후에 결과 전체에 적용된다.
* 다만 `WHERE`을 이용하면 NULL값까지 필터링되므로 주의해야 한다.


* * *
<br>

### ON 절에서 필터링하기

필터링은 일반적으로 두 테이블의 조인이 이루어진 뒤에 `WHERE` 절에서 처리된다.
그러나 조인하기 전에 둘 중 하나나 두 개 모두 필터링하는 것도 가능하다. 예를 들어 두 개의 테이블을 특정한 상황에서만 매치시키고 싶을 수도 있다.

<a href='https://community.modeanalytics.com/sql/tutorial/sql-outer-joins/#the-crunchbase-dataset'>Crunchbase data</a>를 이용해서 앞서 배운 `LEFT JOIN` 예제를 다시 한 번 살펴보자. 이번에는 `ORDER BY` 절을 추가할 것이다.
()역자 주 : Mode Analytics는 튜토리얼 내용을 실습할 수 있는 데이터셋과 SQL 환경을 클라우드로 제공한다.)

```sql
SELECT companies.permalink AS companies_permalink,
                companies.name AS companies_name,
                acquisitions.company_permalink AS acquisition_permalink
                acquisitions.acquired_at AS acquired_date
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_acquisitions acquisitions
    ON companies.permalink = acquisitions.company_permalink
ORDER BY 1;
```

아래 쿼리를 위의 내용과 비교해보자. `company_permalink`가 `/company/1000memories`인 row를 제외하고  `tutorial.crunchbase_acquisitions` 테이블의 모든 데이터가 조인된 것을 알 수 있을 것이다.

``` sql
SELECT companies.permalink AS companies_permalink,
                companies.name AS companies_name,
                acquisitions.company_permalink AS acquisition_permalink
                acquisitions.acquired_at AS acquired_date
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_acquisitions acquisitions
    ON companies.permalink = acquisitions.company_permalink
AND acquisitions.company_permalink != '/company/1000memories'
ORDER BY 1;
```

위 쿼리에서 일어나는 일은 조건문인 `AND…`가 `JOIN`이 일어나기 전에 평가된다는 것이다. 이것은 한 개의 테이블에만 적용되는 `WHERE` 절로 생각해도 좋다. 이 일은 둘 중의 한 테이블에만 일어나는데, 다른 테이블로부터 끌어온 컬럼에는 1000memories라는 permalink가 여전히 보여지기 때문이다.

<img src='https://community.modeanalytics.com/images/intermediate/left-join-on-clause-results.png' />
<br>
<br>

### WHERE 절에서 필터링하기

위와 동일한 필터를 `WHERE` 절로 옮겨보자. 두 테이블이 `JOIN`된 후에 필터링이 일어나는 것을 알 수 있을 것이다. 결과는 이렇다. 1000memories 열은 원래의 테이블에 일단 `JOIN`되지만, 결과로 보여지기 전에 `WHERE` 절에서 두 테이블 모두 완전히 필터링된다.

``` sql
SELECT companies.permalink AS companies_permalink,
                companies.name AS companies_name,
                acquisitions.company_permalink AS acquisition_permalink
                acquisitions.acquired_at AS acquired_date
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_acquisitions acquisitions
    ON companies.permalink = acquisitions.company_permalink
WHERE acquisitions.company_permalink != ‘/company/1000memories’
        OR acquisitions.company_permalink IS NULL
ORDER BY 1;
```

위 쿼리를 실행시키면 1000memories가 포함된 열이 반환되지 않는 것을 알 수 있다. (원래는 아래 상자의 두 줄 사이에 위치해야 한다) 또한 알아두어야 할 것은, `WHERE` 절로 필터링하면 Null값까지 모두 필터링된다는 점이다. 그래서 위 쿼리는 Null값을 포함시키기 위해 한 줄을 더 추가했다.

<img src='https://community.modeanalytics.com/images/intermediate/left-join-where-clause.png' />

(이후 이어지는 실습에 관한 내용은 번역을 생략함)
