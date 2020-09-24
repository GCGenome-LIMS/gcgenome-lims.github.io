---
layout: post
title: "[Tutorial] JPA 튜토리얼"
date:   2020-09-24 11:35:00 +0900
categories:
tags: jpa,orm
---

```
Requires: Java, Database, Spring Boot, Gradle
```

일반적으로 프로그램의 메모리상에 존재하는 데이터는 휘발성입니다. 데이터를 상시적으로 활용할 수 있는 영속성을 부여하려면 파일 또는 데이터베이스를 사용하여 관리합니다. Java에서 객체(**O**bject) 형태로 관리하고 있는 데이터를 관계형 데이터베이스(**R**elational database)에서 관리하려면, 객체 데이터를 DB에서 요구하는 형식에 맞게 변환(**M**apping)하는 과정이 필요합니다. 이것을 ORM(Object-Relational Mapping)이라고 합니다. JPA(Java Persistent API)는 ORM기술에 대한 API 표준 명세입니다. 여기서는 JPA의 구현체인 Hibernate라는 라이브러리를 사용하여 Java에서 Postgresql 사이에 데이터를 교환하여 보겠습니다. ORM은 Spring Framework와 독립적으로 구성할 수 있지만 튜토리얼에서는 간편한 설정을 통해 보다 핵심적인 내용에 집중하기 위해 Spring Boot와 Gradle를 사용하겠습니다.
<br/><br/><br/>
# 프로젝트 세팅


# 데이터 테이블
튜토리얼에서 사용할 데이터에 대한 ERD는 다음과 같이 구성해 보았습니다.

institution 테이블과 person 테이블이 있고, 각각 적절한 id가 부여되어 있으며, person 테이블의 institution 필드가 institution 테이블의 id로 FK가 연결된 One to Many 구조입니다.


# 테이블 매핑



# 삽입, 삭제
