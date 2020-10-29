---
layout: post
title: "[How-To] JPA를 사용하여 데이터를 검색하는 방법"
date:   2020-10-29 20:26:00 +0900
categories:
author: sayaya1090
tags: jpa,orm
---

```
Java: JDK 11
Spring Boot:  2.3.4.RELEASE
```

JPA를 사용하여 데이터를 검색하는 방법

본 문서에서는 JPA를 사용하여 검색 대상을 필터링하는 방법을 정리합니다. 
다음과 같은 관계의 데이터가 있을 때,

GCGenome 기관의(Institution.name=GCGenome) Manager 목록(Person.role=MANAGER)을 가져와 보도록 하겠습니다.

표준 SQL에서는 다음과 같이 쿼리를 작성할 수 있습니다.
```
SELECT P.* 
FROM Person P, Institution I 
WHERE P.institution=I.id 
AND I.name='GCGenome'
AND P.role='MANAGER';
```

JPA에서 검색 기능을 사용하는 방법은 크게 3가지가 있습니다.

 - @Query
 - @Where
 - Criteria API
