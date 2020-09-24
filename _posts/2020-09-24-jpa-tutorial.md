---
layout: post
title: "[Tutorial] JPA 튜토리얼"
date:   2020-09-24 11:35:00 +0900
categories:
author: sayaya1090
tags: jpa,orm
---

```
Requires: Java, Database, Spring Boot, Gradle
```

일반적으로 프로그램의 메모리상에 존재하는 데이터는 휘발성입니다. 데이터를 상시적으로 활용할 수 있는 영속성을 부여하려면 파일 또는 데이터베이스를 사용하여 관리합니다. Java에서 객체(**O**bject) 형태로 관리하고 있는 데이터를 관계형 데이터베이스(**R**elational database)에서 관리하려면, 객체 데이터를 DB에서 요구하는 형식에 맞게 변환(**M**apping)하는 과정이 필요합니다. 이것을 ORM(Object-Relational Mapping)이라고 합니다. JPA(Java Persistent API)는 ORM기술에 대한 API 표준 명세입니다. 여기서는 JPA의 구현체인 Hibernate라는 라이브러리를 사용하여 Java에서 Postgresql 사이에 데이터를 교환하여 보겠습니다. ORM은 Spring Framework와 독립적으로 구성할 수 있지만 튜토리얼에서는 간편한 설정을 통해 보다 핵심적인 내용에 집중하기 위해 Spring Boot와 Gradle를 사용하겠습니다.
<br/><br/><br/>
# 프로젝트 세팅
라이브러리 의존성 관리를 위해 gradle(6.4.1)을 사용하였습니다. 다음과 같이 설정합니다.
```
plugins {
    id 'org.springframework.boot' version '2.3.4.RELEASE'
    id 'io.spring.dependency-management' version '1.0.10.RELEASE'
    id 'java'
}

group 'com.greencross'
version 'jpa-test-1.0'

repositories {
    mavenCentral()
}

dependencies {
    implementation('org.springframework.boot:spring-boot-starter')
    implementation('org.springframework.boot:spring-boot-starter-data-jpa')
    implementation('org.postgresql:postgresql')
    implementation('org.projectlombok:lombok:1.18.12')              // Setter, Getter 자동 생성을 위해 lombok 라이브러리를 추가하였습니다.
    annotationProcessor('org.projectlombok:lombok:1.18.12')
}
```

그리고 데이터베이스 접속 설정을 위해 application.properties를 아래와 같이 입력합니다.
```
# -----------------------
# POSTGRESQL DATABASE CONFIGURATION
# -----------------------
spring.datasource.url=jdbc:postgresql://{DB HOST IP}:{PORT}/{DB NAME}
spring.datasource.username={USER NAME}
spring.datasource.password={USER PASSWORD}
spring.datasource.driver-class-name=org.postgresql.Driver
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```
<br/><br/><br/>
# 데이터 테이블
튜토리얼에서 사용할 데이터에 대한 ERD는 다음과 같이 구성해 보았습니다.

institution 테이블과 person 테이블이 있고, 각각 적절한 id가 부여되어 있으며, person 테이블의 institution 필드가 institution 테이블의 id로 FK가 연결된 One to Many 구조입니다.
<br/><br/><br/>
# 테이블 매핑


<br/><br/><br/>
# 삽입, 삭제
