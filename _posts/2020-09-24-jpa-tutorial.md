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

설정이 완료된 다음 프로젝트를 기동하면, 아래와 같이 Hibernate ORM이 로딩되고 PostgreSQL JDBC 드라이버를 사용하여 데이터베이스와 정상 연결되어 초기화에 성공했다는 메시지가 출력됩니다.
![img0](/assets/img/2020-09-24-jpa-tutorial-img0.png)

# 데이터 테이블
튜토리얼에서 사용할 데이터에 대한 ERD는 다음과 같이 구성해 보았습니다.

![img1](/assets/img/2020-09-24-jpa-tutorial-img1.png)

institution 테이블과 person 테이블이 있고, 각각 적절한 id가 부여되어 있으며, person 테이블의 institution 필드가 institution 테이블의 id로 FK가 연결된 One to Many 구조입니다.
다음 쿼리를 사용하여 테이블을 구성할 수 있습니다.
```
CREATE TABLE public.institution
(
    id uuid NOT NULL,
    name character varying(64) COLLATE pg_catalog."default",
    CONSTRAINT "Institution_pkey" PRIMARY KEY (id)
);
CREATE TABLE public.person
(
    id character varying(64) COLLATE pg_catalog."default" NOT NULL,
    name character varying(64) COLLATE pg_catalog."default",
    email character varying(64) COLLATE pg_catalog."default",
    institution uuid,
    CONSTRAINT "person_pkey" PRIMARY KEY (id),
    CONSTRAINT fkq7tgo7kig3jfs8un8mhsh97mp FOREIGN KEY (institution)
        REFERENCES public.institution (id) MATCH SIMPLE
        ON UPDATE CASCADE
        ON DELETE CASCADE
);
```
<br/><br/><br/>
# 테이블 매핑
아래와 같이 각각의 테이블과 1:1 대응되는 2개의 클래스를 생성합니다.

```
package com.greencross.entity;

import lombok.Data;
import lombok.experimental.Accessors;

import javax.persistence.*;

@Entity
@Table(name="Person")
@Data
@Accessors(fluent = true)
public class Person {
	@Id
	private String id;
	@Column
	private String name;
	@Column
	private String email;
	@ManyToOne
	@JoinColumn(name="institution", referencedColumnName="id")
	private Institution institution;
}
```
Person 클래스에서 @ManyToOne과 @JoinColumn Annotation을 사용하여 Person 테이블의 institution 필드가 Institution 클래스의 id 필드와 FK 관계임을 표시하였습니다.
lombok 라이브러리를 사용하여 setter와 getter는 자동 생성되도록 하였습니다.

```
package com.greencross.entity;

import lombok.Data;
import lombok.experimental.Accessors;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.Id;
import javax.persistence.Table;
import java.util.UUID;

@Entity
@Table(name="Institution")
@Data
@Accessors(fluent = true)
public class Institution {
	@Id
	private UUID id;
	@Column
	private String name;
	@OneToMany(mappedBy="institution")
    @ToString.Exclude
	private List<Person> persons;
}
```
Institution 클래스에서는 Person 클래스와 반대로 @OneToMany Annotation을 사용하였습니다.
하나의 Institution은 여러 Person에 대응되기 때문에, 필드는 컬렉션 타입으로 정의됩니다. 
@ToString.Exclude 는 ToString으로 출력시 Person과 Institution 사이의 순환참조를 방지하기 위해 추가하였습니다.

<br/><br/><br/>
# 삽입, 삭제
삽입과 삭제 튜토리얼을 위해 다음과 같이 적절한 클래스를 하나 생성하였습니다.
```
package com.greencross;

import com.greencross.entity.Institution;
import com.greencross.entity.Person;
import org.springframework.stereotype.Component;
import org.springframework.transaction.annotation.Transactional;

import javax.persistence.EntityManager;
import java.util.UUID;

@Component
public class Tutorial {
	private final EntityManager em;
	public Tutorial(EntityManager em) {
		this.em = em;
	}

    @Transactional
	public void insert() {
		Institution inst = new Institution().id(UUID.fromString("9145dc43-5a6f-468d-9f32-943632d9292e")).name("GC녹십자지놈");
		inst = em.merge(inst);
		Person person1 = new Person().id("sayaya1090").name("홍길동").email("sayaya@gccorp.com").institution(inst);
		em.merge(person1);
		Person person2 = new Person().id("bigluke").name("성태용").email("sungty@gccorp.com").institution(inst);
		em.merge(person2);
	}
    @Transactional(readOnly=true)
	public void read() {
		Institution inst = em.find(Institution.class, UUID.fromString("9145dc43-5a6f-468d-9f32-943632d9292e"));
		System.out.println(inst);
		System.out.println(inst.persons());
	}
}
```

insert 함수에서는 Institution 객체 하나와 Person 객체 두개를 생성하고 EntityManager(em)의 merge 함수를 각각 호출하였습니다.
EntityManager의 merge 함수는 데이터베이스에 저장한 다음 저장된 객체를 리턴하는 함수입니다. 
@Transactional Annotation은 트랜잭션 범위를 나타냅니다. 함수 시작 전에 트랜잭션이 시작되고, 함수가 끝나면 Commit됩니다. 

다음과 같이 Spring boot 어플리케이션 시동 후 Tutorial의 insert와 read 함수를 한번씩 호출하였습니다.
```
package com.greencross;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.event.ApplicationReadyEvent;
import org.springframework.context.event.EventListener;

@SpringBootApplication
public class Application {
	public static void main(String[] args) {
		SpringApplication.run(Application.class, args).close();
	}
	private final Tutorial tutorial;
	public Application(Tutorial tutorial) {
		this.tutorial = tutorial;
	}
	@EventListener(ApplicationReadyEvent.class)
	public void jpaTutorial() {
		tutorial.insert();
		tutorial.read();
	}
}
```

insert 함수가 호출되면, 아래와 같은 쿼리가 생성되어 호출되는 것을 볼 수 있습니다.
```
Hibernate: 
    select
        institutio0_.id as id1_0_0_,
        institutio0_.name as name2_0_0_ 
    from
        institution institutio0_ 
    where
        institutio0_.id=?
Hibernate: 
    select
        person0_.id as id1_1_0_,
        person0_.email as email2_1_0_,
        person0_.institution as institut4_1_0_,
        person0_.name as name3_1_0_ 
    from
        person person0_ 
    where
        person0_.id=?
Hibernate: 
    insert 
    into
        institution
        (name, id) 
    values
        (?, ?)
Hibernate: 
    insert 
    into
        person
        (email, institution, name, id) 
    values
        (?, ?, ?, ?)
Hibernate: 
    insert 
    into
        person
        (email, institution, name, id) 
    values
        (?, ?, ?, ?)
```

merge는 insert or update로 작동합니다. 현재 아무 데이터가 입력되어있지 않기 때문에, insert 쿼리가 동작하였습니다.
Institution이 하나, Person 이 둘 입력되었습니다.

read 함수가 호출되면 아래와 같은 쿼리가 실행되고, JPA에 의해 Institution과 Person 객체를 생성하여 출력합니다.

```
Hibernate: 
    select
        institutio0_.id as id1_0_0_,
        institutio0_.name as name2_0_0_ 
    from
        institution institutio0_ 
    where
        institutio0_.id=?
Institution(id=9145dc43-5a6f-468d-9f32-943632d9292e, name=GC녹십자지놈)
Hibernate: 
    select
        persons0_.institution as institut4_1_0_,
        persons0_.id as id1_1_0_,
        persons0_.id as id1_1_1_,
        persons0_.email as email2_1_1_,
        persons0_.institution as institut4_1_1_,
        persons0_.name as name3_1_1_ 
    from
        person persons0_ 
    where
        persons0_.institution=?
[Person(id=sayaya1090, name=홍길동, email=sayaya@gccorp.com, institution=Institution(id=9145dc43-5a6f-468d-9f32-943632d9292e, name=GC녹십자지놈)), Person(id=bigluke, name=성태용, email=sungty@gccorp.com, institution=Institution(id=9145dc43-5a6f-468d-9f32-943632d9292e, name=GC녹십자지놈))]
```

inst.persons() 함수를 호출하면 매핑된 정보에 따라 person 목록을 select 하는 쿼리가 수행되고 각각의 Row가 Person 클래스에 매핑되는 것을 확인할 수 있습니다.

이상으로 튜토리얼을 마칩니다.
