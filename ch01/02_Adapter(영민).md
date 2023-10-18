# Part2. Adapter

## Adapter 패턴 : 사이에 끼워 재사용한다
예를들어 직류 12볼트 노트북을 교류 100볼트 AC 전원에 연결하려고한다
- AC 어댑터라는 장치 사용
- 가정용으로 제공되는 교류 100볼트를 직류 12볼트로 변환한다
- 제공된 것과 필요한 것 사이에 들어가서 사이를 채우는 것이 어댑터의 역할
- 어댑터는 Adapt (적응)시키는 것을 뜻함

프로그램의 세계에도 제공된 코드를 그대로 사용할 수 없을 때 필요한 형태로 바꾸어 사용한다
Adapter 패턴은 Wrapper 패턴이라고 불리기도 한다. (상품을 깨끗한 포장지로 싸서 선물용 상품을 만드는 것 처럼 무엇인가를 포장해서 다른용도로 사용할 수 있도록 변환해주는 것이 래퍼이자 어댑터)

Adapter 패턴에는 두 종류가 있다
1. 클래스에 의한 Adapter 패턴 (상속을 위한 패턴)
2. 인스턴스에 인한 Adapter 패턴 (위임을 사용한 패턴)


## 예제 프로그램 1. 상속을 사용한 패턴
Banner 클래스를 사용하여 Print 인터페이스를 충족하는 클래스 만들기

### Banner 클래스
여기서 사용할 Banner 클래스는 문자열을 괄호로 묶어서 표시하는 showWithParen,
문자열 앞뒤에 *를 붙여서 표시하는 showWithAster 메서드를 가지고 있다. Banner 클래스를
교류 100볼트 처럼 '이미 제공된 것' 이라 가정한다

```java
public class Banner {
  private String string;

  public Banner(String string) {
    this.string = string;
  }

  public void showWithParen() {
    System.out.println("(" + string + ")");
  }

  public void showWithAster() {
    System.out.println("*" + string + "*");
  }
}
```

### Print 인터페이스
Print 인터페이스 에서는 문자열을 괄호로 묶어 약하게 표시하는 메서드 printWeak,
문자열을 *로 강조해서 표시하는 printString 이 있다.
```java
public interface Print {
  public abstract void printWeak();
  public abstract void pirntString();
}
```

### PrintBanner 클래스
PrintBanner 는 어댑터 역할을 하는 클래스
- 제공된 Banner 클래스를 상속받아 필요한 Print 인터페이스를 구
- Banner를 확장하여 showWithParen 메서드와 showWithAster 메서드를 상속
- 필요한 Print 인터페이스를 구현하여 PrintWeak 메서드와 PrintString 메서드를 구현

```java
public class PrintBanner extends Banner implements Print {
  public PirntBanner(String string) {
    super(string);
  }

  @Override
  public void printWeak() {
    showWithParen()
  }

  @Override
  public void printStrong() {
    showWithAster()
  }
}
```

### Main 클래스
Main 클래스는 어댑터 역할을 하는 PrintBanner 클래스를 이용해 Hello 문자열을
약하게(괄호로 묶음) 혹시 강하게(*로 묶음) 표시

```java
public class Main {
  public static void main(String[] args) {
    Print p = new PrintBanner("Hello);
    p.printWeak();
    p.printStrong();
  }
}
```

위 코드의 실행결과는 아래와 같다
```java
(Hello)
*Hello*
```

여기서 Main 클래스는 어디까지나 Print 인터페이스를 사용해서(printWeak 메서드와 PrintString 메서드) 프로그래밍 하고 있다. Banner 클래스나 showWithParen, showWithAster 메서드는 Main 클래스의 코드에서 완전히 숨겨져 있다.

즉, PirntBanner 클래스가 어떻게 구현됐는지 Main 클래스는 모른다. 따라서 Main 클래스를 전혀 변경하지 않고도 PrintBanner 클래스의 구현을 바꿀 수 있다.


## 예제 프로그램 2. 위임을 사용한 패턴
위임이란? 
- 자바에서 위임은 어떤 메서드의 실제 처리를 다른 인스턴스 메서드에 맡기는 것을 의미함

Main과 Banner 클래스는 예제 1과 동일하지만 Print는 인터페이스가 아닌 클래스라고 가정하자

즉, Banner 클래스를 이용해서 Print 클래스오 같은 메서들르 갖는 클래스를 실현하려고 한다
- Java에서는 두 개의 클래스를 동시에 상속할 수 없다
- 다시 말해, PrintBanner 클래스를 Print와 Banner 양쪽의 하위 클래스로 정의할 수 없다
- PrintBanner 클래스는 banner 필드로 Banner 클래스의 인스턴스를 갖는다
  (이 인스턴스는 PrintBanner)의 생성자에서 생성
- printWeak, printString 메서드는 banner 필드를 통해 showWithParen, showWithAster 메서드를 호출
- 앞의 예제에서는 상속받은 메서드를 호출했지만, 여기서는 banner 필드를 경유해서 호출한다
- 이때 위임이 등장. 자신이 처리하지 않고 다른 인스턴스(banner)에게 맡기는 것



## Adapter 패턴의 등장인물
1. Target(대상) 역
- 지금 필요한 메서드를 결정한다
- 노트북을 작동시키는 직류 12볼트에 해당
- 예제 프로그램에서는 Print 인터페이스와 Print 클래스가 이 역할 
2. Client(의뢰자) 역 
- Target의 메서드를 사용해 일한다
- 직류 12볼트로 작동하는 노트북 노트북에 해당
- 예제 프로그램에서는 Main 클래스가 이 역할
3. Aaptee(적응 대상자) 역
  - Adapter(적응자)가 아닌 Adaptee (적응 대상자)
  - Adaptee는 이미 준비된 메서드를 가지는 역할
  - 교류 100볼트인 AC 전원에 해당
  - 예제 프로그램에서는 Banner 클래스가 이 역할
  - Adapter의 메서드가 Target의 메서드와 일치하면 Adapter는 필요 없음
4. Adapter(적응자) 역
- Adaptee의 메서드를 사용해 어떻게든 Target을 만족시키는 것이 목적
- 교류 100볼트를 직류 12볼트로 변환하는 어댑터에 해당
- 예제 프로그램에서는 PrintBanner 클래스가 이 역할



