---
layout: post
title: "[Tutorial] Design pattern - Strategy"
date:   2020-11-16
categories:
author: bigluke
---

# Software design patter - Strategy
디자인 패턴이란 프로그램을 개발할 때 자주 발생하는 반복적인 문제를 해결하기 위한 방법론으로, 설계에 관한 노하우에 이름을 붙여 재사용하기 위한 것이다.
본 블로그 Tutorial에서는 병원에서 암종별 처방을 자동화하는 시스템을 Strategy 패턴을 활용하여 코드를 구현해 보겠습니다.

# Prerequisites
Tutorial을 시작하기 전에 기본 JAVA 프로그래밍 개념에 대해 이미 알고 있다고 가정합니다.  
JAVA 개념에 대해 잘 모른다면 JAVA 프로그래밍에 대한 자습서를 살펴볼 것을 권장합니다.


# Tutorial
  * 간암(LiverCancer)와 폐암(LungCancer) 클래스 구현  
  * 추상적인 접근점 암종(Cancer) 인터페이스 구현  
  * LiverCancer와 LungCancer 객체를 사용하는 처방(Prescription) 클래스 구현  
![img1](/assets/img/2020-11-16-strategy-tutorial-img1.png)



Cancer 인터페이스를 생성합니다.    
Cancer 인터페이스에 약 처방 기능을 추가합니다.
```groovy
public interface Cancer {
  public void medicine();
}
```

Cancer 인터페이스를 상속받는 LiverCancer 클래스를 생성합니다.  
Cancer 인터페이스의 medicine() 함수를 Override합니다.
```groovy
public class LiverCancer implements  Cancer{
	@Override
	public void medicine() {
		System.out.println("간암약 처방");
	}
}
```
Cancer 인터페이스를 상속받는 LungCancer 클래스를 생성합니다.  
Cancer 인터페이스의 medicine() 함수를 Override합니다.
```groovy
public class LungCancer implements Cancer{
	@Override
	public void medicine() {
		System.out.println("폐암약 처방");
	}
}
```

Prescription 클래스를 만듭니다.
```groovy
public class Prescription {
	private Cancer cancer;

	public void setCancer(Cancer cancer){
		this.cancer = cancer;
	}
	public void result(){
		cancer.medicine();
	}
}
```

Presctiption 클래스를 생성하여 암종별 처방 동작을 확인 합니다.
```groovy
public class Main {
	public static void main(String[]  args){
		Prescription prescription = new Prescription();

		prescription.setCancer(new LiverCancer());
		prescription.result();

		prescription.setCancer(new LungCancer());
		prescription.result();

	}
}
```

Prescription클래스의 medicine함수가 호출되면 아래와 같이 등록된 암종의 처방 결과값이 출력 됩니다.
```
간암약 처방  
폐암약 처방
```


이상으로 Strategy Pattern 튜토리얼을 마칩니다.


