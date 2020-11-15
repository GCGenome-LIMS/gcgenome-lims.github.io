---
layout: post
title: "[Tutorial] Design pattern - Strategy"
date:   2020-11-16
categories:
author: bigluke
---

# Prerequisites
Tutorial을 시작하기 전에 기본 JAVA 프로그래밍 개념에 대해 이미 알고 있다고 가정합니다.
JAVA 개념에 대해 잘 모른다면 JAVA 프로그래밍에 대한 자습서를 살펴볼 것을 권장합니다.

# Software design patter - Strategy
디자인 패턴이란 프로그램을 개발할 때 주자 발생하는 반복적인 문제를 해결하기 위한 방법론으로, 설계에 관한 노하우에 이름을 붙여 재사용하기 위한 것이다.
본 블로그 Tutorial에서는 병원에서 암종별 처방을 자동화하는 시스템을 Strategy 패턴을 활용하여 코드를 구현해 보겠습니다.


암종 인터페이스를 만듭니다.
```groovy
public interface Cancer {
  public void medicine();
}
```

처방 인터페이스를 구현하는 구체적인 암종별 클래스를 만듭니다.
```groovy
public class LiverCancer implements Cancer{
  @Override
  public void medicine(){
    System.out.println("간암약 처방");
  }
}
```

```groovy
public class LungCancer implements Cancer{
  @Override
  public void medicine(){
    System.out.println("폐암약 처방");
  }
}
```

Prescription 클래스를 만듭니다.
```groovy
public class Prescription{
  private Cancer cancer;
  
  public setCancer(Cancer cancer){
    this.cancer = cancer
  }
  
  public void medicine(){
    cancer.medicine();
  }
}
```

Presctiption을 사용하여 암종을 변경할 때 동작의 변화를 확인 합니다.
```groovy
public class Main{
  public static void main(String[] args) {
    Prescription prescription = new Prescription();
    
    prescription.setCancer(new LiverCancer());
    presctiption.medicine();
    
    prescription.setCancer(new LungCancer());
    presctiption.medicine();
    
  }
}
```
