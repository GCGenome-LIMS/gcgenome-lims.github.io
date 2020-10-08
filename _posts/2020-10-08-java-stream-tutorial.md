---
layout: post
title: "[Technical reference] JAVA11 Stream"
date:   2020-10-08
categories:
author: bigluke
---

```
Requires: Java 11
```

# Stream
자바 8에서 추가한 Stream는 람다를 활용할 수 있는 기술 중 하나입니다. 자바 8 이전에는 배열 또는 컬렉션 인스턴스를 다루는 방법은 for 또는 foreach 문을 돌면서 요소 하나씩을 꺼내서 다루는 방법이었습니다. 간단한 경우라면 상관없지만 로직이 복잡해질수록 코드의 양이 많아져 여러 로직이 섞이게 되고, 메소드를 나눌 경우 루프를 여러 번 도는 경우가 발생합니다.
Stream은 배열 또는 컬렉션 인스턴스에 함수 여러 개를 조합해서 원하는 결과를 필터링하고 가공된 결과를 얻을 수 있습니다. 또한 람다를 이용해서 코드의 양을 줄이고 간결하게 표현할 수 있습니다. 즉, 배역과 컬렉션을 함수형으로 처리할 수 있습니다.

# Stream 생성
Stream을 사용하기 위해서는 Stream을 생성할 필요가 있습니다. collection, 배열 또는 자체적으로 생성할 수 있습니다.

```groovy
//Array
String[] arr = new String[]{"a", "b", "c"};
Stream<String> stream = Arrays.stream(arr);

//Collection
List<String> lists = Arrays.asList("a", "b", "c");
Stream<String> stream = lists.stream();

//Stream Class
Stream<String> stream = Stream.of("a", "b", "c");
```

# Stream 가공
전체 요소 중에서 여러 API를 이용해서 원하는 요소를 뽑아낼 수 있습니다
```groovy
List<String> names = Arrays.asList("Sung", "Kim", "Hong");
```
# Filter
Filter는 Stream 내 요소들을 조건에 따라 분류하는 작업입니다.
```groovy
Stream<String> stream = names.stream().filter(name -> name.contains("ng"));
stream.forEach(System.out::println);
```
```groovy
Sung
Hong
```
# Map
Map은 Stream 내 요소들을 특정 값으로 변환할 수 있습니다.
```groovy
Stream<String> stream = names.stream().map(String::toUpperCase);
stream.forEach(System.out::println);
```
```groovy
SUNG
KIM
HONG
```

# Flatmap
Flatmap은 Stream 내 요소를 단일 Stream 요소로 반환할 수 있습니다.
```groovy
List<List<String>> list = 
  Arrays.asList(Arrays.asList("Sung", "Kim", "Hong"),
                Arrays.asList("Lee", "Nam", "Bae")); //[[Sung, Kim, Hong], [Lee, Nam, Bae]]

List<String> flatList =
  list.stream()
    .flatMap(Collection::stream)
    .collect(Collectors.toList()); // [Sung, Kim, Hong, Lee, Nam, Bae]
    
flatList.forEach(System.out::println);
```
```groovy
Sung
Kim
Hong
Lee
Nam
Bae
```

# Sorted
Sorted는 Stream 내 요소를 조건에 따라 요소를 정렬할 수 있습니다.
```groovy
List<Integer> stream = IntStream.of(55, 32, 2, 45, 85)
				.sorted()
				.boxed()
				.collect(Collectors.toList());
stream.forEach(System.out::println);
```
```groovy
2
32
45
55
85
```
```groovy
List<String> lang = Arrays.asList("Java", "Python", "C", "HTML", "CSS");

List<String> sort = lang.stream()
    .sorted()
    .collect(Collectors.toList());

sort.forEach(System.out::println);
System.out.println("======================");

List<String> reverse = lang.stream()
    .sorted(Comparator.reverseOrder())
    .collect(Collectors.toList());

reverse.forEach(System.out::println);
```
```groovy
C
CSS
HTML
Java
Python
======================
Python
Java
HTML
CSS
C
```
