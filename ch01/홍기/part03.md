Template은 물건을 만드는 틀을 의미한다.

어떤 물건을 만들기 위한 틀을 관찰하면 어떤 물건이 될 것인지는 유추 가능하지만, 그 물건이
실제로는 어떤 목적을 위해 만들어 질 것인지는 물건이 만들어진 뒤에 알 수 있다.

Template method 패턴도 이러한 뜻에서 가져와
상위클래스에서 기능이 어떻게 동작할 것인지는 유추 가능하지만, 해당 기능의 세부적인 목적은
하위 클래스를 정의하면서 정해진다고 이해하면 된다.

```java
public abstract class Abs {
    public void abstract void open();
    public void abstract void print();
    public void abstract void close();

    public final void display() {
        open();
        for(int i = 0; i < 5; i++) {
            print();
        }
        close();
    }
}
```
위 식은 추상클래스로 open(), print(), close() 추상메서드가 정의되어있고,
display()라는 메서드가 구현되어있다.

display() 메서드는 각 추상메서드를 사용하고 있는데, 현재는 명확하게 구현되어 있지 않다.

다만, 이 display() 메서드는 open(),print(),close() 라는 추상 메서드를 요소로 가지고 있다.

이제 이 Abs라는 클래스를 Extends 한 하위 클래스들에서 open(),print(),close()를 정의해주면

하위 클래스에서 정의한 것으로 코드가 동작할 것이다.

```java
public class SonOfAbs extends Abs {
    
    private char ch;

    public SonOfAbs(char ch) {
        this.ch = ch;
    }

    @Override
    public void open() {
        System.out.println("Hi~~");
    }

    @Override
    public void print() {
        System.out.println("my favorite alphabet is " + ch +);
    }

    @Override
    public void clsoe() {
        System.out.println("Bye!");
    }
}
```

```java
public class DaughterOfAbs extends Abs {
    
    private String str;

    public DaughterOfAbs(char str) {
        this.str = str;
    }

    @Override
    public void open() {
        System.out.println("***start");
    }

    @Override
    public void print() {
        System.out.println(str);
    }

    @Override
    public void clsoe() {
        System.out.println("end***");
    }
}
```

SonOfAbs, DaughterOfAbs 클래스는 Abs를 상속받았으며, 각 클래스는
Abs의 추상메서드를 재정의하였다.

다시 말하면,
Abs의 추상메서드들은 Abs를 상속받은 각각의 클래스에서
다른 동작을 하도록 정의 되었다. 

```java
public class Main {

    public static void main(String[] args) {
        Abs son = new SonOfAbs('S');

        Abs dau = new SonOfAbs("I am daughter");

        son.print();
        /*
        * Hi~~
        * my favorite alphabet is S
        * Bye~
        */

        dau.print();
        /*
        * ***start
        * I am daughter
        * I am daughter
        * I am daughter
        * I am daughter
        * I am daughter
        * ***end
        */
    }
}
```
하위 클래스의 인스턴스를 생성하여 상위 클래스의 메서드 실행시,
다른 기능을 하는 것을 확인 할 수 있다.

그렇다면 이러한 패턴을 사용함으로써 얻을 수 있는 장점은 무엇이 있을까?
상위 클래스에 알고리즘이 기술되어 있으므로, 하위클래스에는 알고리즘을 기술할 필요가 없어진다.

유사한 기능을 하는 클래스들은 상위클래스에 로직을 정의해두고, 상속하여 하위클래스에서 
각 세부 기능을 정의해준다면, 기능의 일부 순서가 변경된다면, 상위 클래스에 정의된 로직의 순서만 변경하면 될 것이다.

Template Method 패턴에서는 상위 클래스와 하위 클래스가 긴밀하게 연계되어 움직이는데,
상위 메서드의 추상메서드를 하위 클래스에서 어떻게 구현할 지, 어디서 호출되는지 잘 생각해야한다.

LSP원칙을 잘 지켜 구현하는 것이 좋다.
