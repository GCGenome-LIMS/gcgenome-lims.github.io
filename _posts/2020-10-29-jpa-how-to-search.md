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
![img0](/assets/img/2020-10-29-jpa-search-img0.PNG)

GCGenome 기관의(Institution.name=GCGenome) Manager 목록(Person.role=MANAGER)을 가져와 보도록 하겠습니다.

표준 SQL에서는 다음과 같이 쿼리를 작성할 수 있습니다.
```sql
SELECT P.* 
FROM Person P 
JOIN Institution I 
ON P.institution=I.id 
WHERE I.name='GCGenome'
AND P.role='MANAGER';
```
![img1](/assets/img/2020-10-29-jpa-search-img1.PNG)

여기서는 다음과 같이 3가지 방법을 사용하여 동일한 검색을 수행해 보겠습니다.
 - Query : 쿼리문을 작성하여 검색합니다.
 - @Where : Entity 클래스에 연관된 Child entity의 Subset을 매핑합니다.
 - Criteria API : API를 사용하여 쿼리를 동적으로 생성합니다.
 
 
# Query를 사용하여 검색
직접 쿼리문을 작성하는 방법은 
1. Repository interface에 @Query annotation을 사용하여 작성
2. Entity class에 @NamedQuery annotation을 사용하여 작성
3. SQL String을 작성한 다음 EntityManager를 사용하여 Query 객체를 생성
등 다양한 방법이 지원됩니다. 여기서는 1번 방법을 사용하여 작성하겠습니다.

다음과 같이 작성하여 Repository interface를 작성합니다. 
```java
package com.greencross.repo;

import com.greencross.entity.Person;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;

import java.util.stream.Stream;

@Repository
public interface PersonRepo extends JpaRepository<Person, String> {
	@Query("SELECT p FROM Person p WHERE p.institution.name=?1 AND p.role=?2")
	Stream<Person> findByInstitutionNameAndRole(String institutionName, String role);
}
```

@Query를 사용하여 작성하는 SQL은 표준 SQL이 아닌, 보다 객체지향적 구문을 사용하는 JSQL이라는 규칙에 의해 작성합니다. 
p라는 alias를 사용하여 Person의 객체를 지칭하고 있습니다. 

파라메터는 ?1, ?2... 으로 생성하였는데, 메서드 파라메터가 순번에 맞게 삽입됩니다.

Entity의 필드가 FK로 참조하고 있는 다른 Entity field를 검색 조건에 사용할 때에는, dot(.)연산자를 사용합니다.
위에서는 institution.name로 Person의 참조 테이블 institution의 name 필드를 검색에 활용하고 있습니다.

다음 코드를 실행해보면
```java
private final PersonRepo repo;
	@Transactional(readOnly = true)
	public void search() {
		repo.findByInstitutionNameAndRole("GCGenome", "MANAGER")
			.forEach(System.out::println);
	}
```
![img2](/assets/img/2020-10-29-jpa-search-img2.PNG)

표준 SQL이 생성되어 실행됨을 확인할 수 있습니다.

 
# @Where를 사용하여 검색
두 번째 방법은, 부모 entity를 이미 

```java
package com.greencross.entity;

import lombok.Data;
import lombok.ToString;
import lombok.experimental.Accessors;
import org.hibernate.annotations.Where;

import javax.persistence.*;
import java.util.List;
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
	@Where(clause="role='MANAGER'")
	private List<Person> managers;
}

```

```java
package com.greencross.repo;

import com.greencross.entity.Institution;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.Optional;
import java.util.UUID;

@Repository
public interface InstitutionRepo extends JpaRepository<Institution, UUID> {
	Optional<Institution> findByName(String name);
}

```

```java
private final InstitutionRepo repo;
@Transactional(readOnly = true)
public void search() {
	repo.findByName("GCGenome").map(Institution::managers)
		.ifPresent(list->list.stream().forEach(System.out::println));
}
```

![img3](/assets/img/2020-10-29-jpa-search-img3.PNG)

# Criteria API를 사용하여 검색
