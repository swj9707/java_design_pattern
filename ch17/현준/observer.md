## ch17. Observer

Observer : 상태 변화를 알려주는 패턴

----

- main
```java
public class Main {

    public static void main(String[] args){
        NumberGenerator generator = new RandomNumberGenerator();
        Observer observer1 = new DigitObserver();
        Observer observer2 = new GraphObserver();
        generator.addObserver(observer1);
        generator.addObserver(observer2);
        generator.execute();
    }
}
```

- NumberGenerator
```java
import java.util.ArrayList;
import java.util.List;

public abstract class NumberGenerator {

    private List<Observer> observers = new ArrayList<>();

    public void addObserver(Observer observer){
        observers.add(observer);
    }

    public void deleteObserver(Observer observer){
        observers.remove(observer);
    }

    public void notifyObservers(){
        for(Observer observer:observers){
            observer.update(this);
        }
    }

    public abstract int getNumber();

    public abstract void execute();

}

```

- RandomNumberGenerator
```java
import java.util.Random;

public class RandomNumberGenerator extends NumberGenerator{ // 난수를 생성
    private Random random = new Random();
    private int number;


    @Override
    public int getNumber() {
        return number;
    }

    @Override
    public void execute() {
        number = random.nextInt(50);
        notifyObservers();
    }
}
```

- Observer
```java
public interface Observer {
    public abstract void update(NumberGenerator generator);
}
```

- GraphObserver
```java
public class GraphObserver implements Observer{

    @Override
    public void update(NumberGenerator generator) {
        System.out.print("GraphObserver:");
        int count = generator.getNumber();
        for (int i = 0; i < count; i++) {
            System.out.print("*");
        }
        System.out.println();

        try{
            Thread.sleep(100);
        }catch (InterruptedException e){
        }
    }
}

- DigitObserver
```java
public class DigitObserver implements Observer{

    @Override
    public void update(NumberGenerator generator) {
        System.out.println("DigitObserver:"+generator.getNumber());

        try{
            Thread.sleep(100);
        }catch (InterruptedException e){
        }
    }
}

```
