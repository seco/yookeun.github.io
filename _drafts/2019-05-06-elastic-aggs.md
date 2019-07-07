---
layout: post
title:  "Elasticsearch Aggregations"
date:   2019-05-06
categories: elasticsearch
---

### 1. Metrics

메트릭은 document에서 숫자와 관련된 것을 추출해서 계산해서 집계화하는 것을 말한다. 그래서 avg, sum, min, max, cardinality 등의 집계가 있다.

#### Avg/Min/Max Aggregations

product라는 document에 price라는 필드가 있는데 이것에 대한 평균을 구해보자.

```json 
GET /products/_search?size=0
{
  "aggs": {
    "avg_price": {
      "avg": {
        "field": "price"
      }
    }
  }
}
```

결과는 아래와 같다. 최소(min), 최대(max) 로 변경하면 된다. 

```json 
{
  "took" : 18,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : 0.0,
    "hits" : [ ]
  },
  "aggregations" : {
    "avg_price" : {
      "value" : 105.923
    }
  }
}
```

`aggregaions` 에 우리가 구하려는 값이 보여진다. 

#### Cardinality Aggregation

카디날리티는 document에서 주어진 필드의 고유값의 대략적인 개수를 의미한다.

```json 
GET /products/_search?size=0
{
  "aggs": {
    "type_price": {
      "cardinality": {
        "field": "price"
      }
    }
  }
}
```

```json 
....  
"aggregations" : {
    "type_price" : {
      "value" : 188
    }
  }
```

price에서 중복되지 않는 고유의 값의 188개라는 의미이다. 이것은 어디까지 알고리즘에 의해 대략적인 갯수를 표시하는 것이다.

### 2. Bucket

버킷은 특정 기준을 충족하는 문서들의 모음을 말하다. 예를 들어 우리가 영수증들을 가지고 있다고 하면 각자 기준대로 분류할 수 있다. 외식용, 가격이 만원 이상것들, 월별 등으로 기준으로 묶을 수 있는데 이렇게 묶은 문서들을 Bucket 이라고 부른다.  