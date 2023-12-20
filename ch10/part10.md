# part 10 : Strategy - 알고리즘을 모두 바꾼다

## Strategy 패턴
- 구현한 알고리즘을 모조리 교환할 수 있다
- 스위치를 전환하듯 알고리즘을 바꿔서, 같은 문제를 다른 방법으로 해결하기 쉽게 만들어 주는 패턴


## 예제 프로그램
| 이름              | 설명                                   |
|-----------------|--------------------------------------|
| Hand            | 가위바위보의 '손'을 나타내는 클래스                 |
| Strategy        | 가위바위보의 '전략'을 나타내는 인터페이스              |
| WinningStrategy | 이기면 다음에도 같은 손을 내는 전략을 나타내는 클래스       |
| ProbStrategy    | 직전 손에서 다음 손을 확률적으로 계싼하는 전략을 나타내는 클래스 |
| Player          | 가위바위보를 하는 플레이어를 나타내는 클래스             |
| Main            | 동작테스트용 클래스                           |


### Hand
```java
public enum Hand {

    ROCK("바위", 0 ),
    SCISSORS("가위", 1),
    PAPER("보",2) ;

    private String name ;
    private int handvalue ;

    private static Hand[] hands = {
            ROCK, SCISSORS, PAPER
    } ;

    private Hand(String name, int handvalue) {
        this.name = name ;
        this.handvalue = handvalue ;

    }

    public static Hand getHand(int handvalue){
        return hands[handvalue] ;
    }

    public boolean isStrongerThan(Hand h){
        return fight(h) == 1 ;
    }

    public boolean isWeakerThan(Hand h){
        return fight(h) == -1 ;
    }

    private int fight(Hand h){
        if(this == h){
            return 0 ;
        }
        else if((this.handvalue+1)%3==hand.handvalue){
            return 1 ;
        }
        else{
            return -1 ;
        }
    }

    @Override
    public String toString(){
        return name ;
    }
}
```

- `Hand`형 `enum` 상수는 `SCISSORS`, `ROCK`, `PAPER` 3개뿐이고, 그 인스턴스는 배열 hands에 저장됨
  - 클래스 메소드 `getHand`를 사용하면 `Hand`형의 인스턴스를 얻을 수 있음
  - 손이 값(0,1,2)을 인수로 주면 값에 대응하는 인스턴스가 반환됨
  - 이 구조는 `Singleton` 패턴의 일종임
    - 또하느 `getHand` 메소드는 `static Factory Method`라고 할 수 있음
- `isStrongerThan`과 `isWeakerThan`은 손의 강약을 비교하는 메소드
- 손의 강약을 판정하는 것은 `fight`라는 메소드

### Strategy 인터페이스
```java
public interface Strategy {
    public abstract Hand nextHand() ;
    public abstract void study(boolean win) ;
}
```
- `nextHand`는 다음에 낼 손을 얻기 위한 메소드
- `study`는 직전에 낸 손으로 이겼는지 졌는지를 학습하는 메소드

### WinningStrategy 클래스
```java
import java.util.Random ;

public class WinningStrategy implements Strategy{

    private Random random ;
    private boolean won = false ;
    private Hand prevHand ;

    public WinningStrategy(int seed){
        random = new Random(seed) ;
    }

    @Override
    public Hand nextHand() {
        if(!won){
            prevHand = Hand.getHand(random.nextInt(3)) ;
        }

        return prevHand ;
    }

    @Override
    public void study(boolean win) {
        won = win ;
    }
}
```
- `Strategy` 인터페이스를 구현하는 클래스 중 하나
- `nextHand`와 `Study`라는 두 메소드를 구현하는 것

### ProbStrategy 클래스
```java
import java.util.Random ;

public class ProbStrategy implements Strategy{

    private Random random ;
    private int prevHAndValue = 0 ;
    private int currentHandValue = 0 ;
    private int[][] history = {
        {1, 1, 1},
        {1, 1, 1},
        {1, 1, 1}
    } ;

    public ProbStrategy(int seed){
        random = new Random(seed) ;
    }

    @Override
    public Hand nextHand() {
        int bet = random.nextInt(getSum(currentHandValue)) ;
        int handvalue = 0 ;
        if(bet<history[currentHandValue][0]){
            handvalue = 0 ;
        } else if(bet<history[currentHandValue][0] + history[currentHandValue][1]){
            handvalue = 1 ;
        } else{
            handvalue = 2 ;
        }

        prevHAndValue = currentHandValue ;
        currentHandValue = handvalue ;
        return Hand.getHand(handvalue) ;
    }

    private int getSum(int handValue){
        int sum = 0 ;
        for(int i=0;i<3;++i){
            sum += history[handValue][i] ;
        }
        return sum ;
    }

    @Override
    public void study(boolean win) {
        if(win){
            history[prevHAndValue][currentHandValue]++ ;
        }else{
            history[prevHAndValue][(currentHandValue+2)%3]++ ;
            history[prevHAndValue][(currentHandValue+1)%3]++ ;
        }
    }
}
```
- 과거의 이기고 진 이력을 활용해서 각각의 손을 낼 확률을 바꾸는 방법
  - `history` 필드는 과거의 승패를 반영한 확률 계산을 위한 표
- `study` 메소드는 `nextHand` 메소드에서 반환한 손의 승패를 바탕으로 `history` 필드의 내용을 갱신함

### Player 클래스
```java
public class Player {
    private String name ;
    private Strategy strategy ;
    private int wincount ;
    private int losecount ;
    private int gamecount ;

    public Player(String name, Strategy strategy){
        this.name = name ;
        this.strategy = strategy ;
        
        this.wincount = 0 ;
        this.losecount = 0 ;
        this.gamecount = 0 ;
    }

    public Hand nextHand(){
        return strategy.nextHand() ;
    }

    public void win(){
        strategy.study(true) ;
        wincount++ ;
        gamecount++ ;
    }

    public void lose(){
        strategy.study(false) ;
        losecount++ ;
        gamecount++ ;
    }

    public void even(){
        gamecount++ ;
    }

    @Override
    public String toString(){
        return "["
            + name + ":"
            + gamecount + " games, "
            + wincount + " win. "
            + losecount + " lose"
            + "]" ;
    }
}
```
- 주어진 `이름`과 `전략`으로 인스턴스를 만듦
- `nextHand`는 다음 손을 얻는 메소드, 실제로 다음 손을 결정하는 것은 자신의 `전략`
  - 전략의 `nextHand` 메소드의 반환값이 그대로 `Player`의 `nextHand`메소드의 반환값이 됨
  - `nextHand` 메소드는 자신이 해야 할 처리를 `Strategy`에 맡김(위임)
- 승부 결과를 다음 승부에 활용하고자 `Player` 클래스는 `strategy` 필드를 통해 `study` 메소드를 호출
  - `study` 메소드로 전략의 내부 상태를 변화시키는 것

### Main 클래스
```java
public class Main {

    public static void main(String[] args){
        if(args.length != 2){
            System.exit(0) ;
        }

        int seed1 = Integer.parseInt(args[0]) ;
        int seed2 = Integer.parseInt(args[1]) ;

        Player player1 = new Player("user1", new WinningStrategy(seed1)) ;
        Player player2 = new Player("user2", new WinningStrategy(seed2)) ;

        for(int i=0;i<10;++i){
            Hand nextHand1 = player1.nextHand() ;
            Hand nextHand2 = player2.nextHand() ;

            if(nextHand1.isStrongerThan(nextHand2)){
                System.out.println("Winner : "+player1) ;
                player1.win();
                player2.lose();
            } else if (nextHand2.isStrongerThan(nextHand1)) {
                System.out.println("Winner : "+player2) ;
                player1.lose() ;
                player2.win() ;
            } else{
                System.out.println("Even") ;
                player1.even() ;
                player2.even() ;
            }
        }
        System.out.println("total result : ");
        System.out.println(player1) ;
        System.out.println(player2) ;
    }
}
```

## Strategy 역할
- `Strategy` 패턴에서는 알고리즘 부분을 다른 부분과 의도적으로 분리함
- 알고리즘이 있는 인터페이스 부분만 규정해 두고 프로그램에서 위임을 통해 알고리즘을 이용
- 예를 들어, 알고리즘을 개량해서 더 빠르게 만들고 싶으면, `Strategy`패턴을 이용하여 `Strategy`역의 인터페이스를 변경하지 않도록 주의하고, `ConcreteStrategy`역만 수정
- 게다가, `위임`이라는 약한 결합을 사용하므로, 알고리즘을 용이하게 전환할 수 있음
