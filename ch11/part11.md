# part 11 : Composite 패턴 - 그릇과 내용물을 동일시하다

## Composite 패턴이란?
- 재귀적인 구조를 만들기 위한 패턴
- 컴포지트의 의도는 트리 구조로 작성하여, 전체-부분(whole-part) 관계를 표현하는 것

## 예제 프로그램
파일과 디렉터리를 도식적으로 표현한 프로그램을 작성해보자
| 이름        | 설명                            |
|-----------|-------------------------------|
| Entry     | File과 Directory를 동일시하는 추상 클래스 |
| File      | 파일을 나타내는 클래스                  |
| Directory | 디렉터리를 나타내는 클래스                |
| Main      | 동작 테스트용 클래스                   |

### Entry 클래스
```java
public abstract class Entry{
    
    //이름을 얻는다
    public abstract String getName();
    
    //크기를 얻는다
    public abstract int getSize();
    
    //목록을 표시한다
    public void printList(){
        printList("");
    }
    
    //prefix를 앞에 붙여서 목록을 표시한다
    protected abstract void printList(String prefix);
    
    //문자열 표시
    @Override
    public String toString(){
        return getName() + " (" + getSize() + ")";
    }
}
```
- `Entry` 클래스는 추상 클래스이고 디렉터리 엔트리를 표현함.
  - 이 하위 클래스로 `File` 클래스와 `Directory` 클래스가 만들어 짐
- `getName`과 `getSize` 메소드의 구현은 하위 클래스에 맡김
- `printList`는 목록을 표시하는 메소드
  - 인수가 없는 `printList()`와 인수가 있는 `printList(String)` 메소드
  - `오버로드(다중정의)` 되어있음

### File 클래스
```java
public class File extends Entry{
    private String name;
    private int size;
    
    public File(String name, int size){
        this.name = name;
        this.size = size;
    }
    
    @Override
    public String getName(){
        return name;
    }
    
    @Override
    public int getSize(){
        return size;
    }
    
    @Override
    protected void printList(String prefix){
      System.out.println(prefix + "/" + this);
    }
}
```

- `Entry` 클래스의 하위 클래스로 선언되어 있음


### Directory 클래스

```java
import java.util.ArrayList;
import java.util.List;
import java.util.Map;

public class Directory extends Entry {
  private String name;
  private List<Entry> directory = new ArrayList<>();

  public Directory(String name) {
    this.name = name;
  }

  @Override
  public String getName() {
    return name;
  }

  @Override
  public int getSize() {
    int size = 0;
    for (Entry entry : directory) {
      size += entry.getSize();
    }
    return size;
  }

  @Override
  protected void printList(String prefix) {
    System.out.println(prefix + "/" + this);
    for (Entry entry : directory) {
      System.out.println(prefix + "/" + name);
    }
  }

  // 디렉터리 엔트리를 디렉터리에 추가한다
  public Entry add(Entry entry){
      directory.add(entry);
      return this;
  }
}
```
- `Entry` 클래스의 하위 클래스로 선언되어 있음
- 필드 `name`은 디렉터리 이름을 나타내며 `File` 클래스와 동일하다
- `Directory`에는 크기를 나타내는 필드가 없음
  - 디렉터리의 크기를 동적으로 계산해서 구하기 때문

### Main 클래스
```java
public class Main{
    public static void main(String[] args){
      System.out.println("Making root entries...");
      Directory rootdir = new Directory("root");
      Directory bindir = new Directory("bin");
      Directory tmpdir = new Directory("tmp");
      Directory usrdir = new Directory("usr");
      rootdir.add(bindir);
      rootdir.add(tmpdir);
      rootdir.add(usrdir);
      bindir.add(new File("vi", 10000));
      bindir.add(new File("latex", 20000));
      rootdir.printList();
      System.out.println();

      System.out.println("Making user entries...");
      Directory youngin = new Directory("youngin");
      Directory gildong = new Directory("gildong");
      Directory dojun = new Directory("dojun");
      usrdir.add(youngin);
      usrdir.add(gildong);
      usrdir.add(dojun);
      youngin.add(new File("diary.html", 100));
      youngin.add(new File("Composite.java",200));
      gildong.add(new File("memo.tex", 300));
      dojun.add(new File("game.doc",400));
      dojun.add(new File("junk.mail", 500));
      rootdir.printList();
    }
}
```

## Composite 패턴의 등장인물
### Leaf(잎)
- `내용물`을 나타냄
- 이 안에는 다른 것을 넣을 수 없음
- 예제 프로그램의 `File` 클래스

### Composite(복합체)
- `그릇`을 나타냄
- `Leaf` 역이나 `Composite` 역을 넣을 수 있음
- 예제 프로그램의 `Directory` 클래스

### Component
- `Leaf`역과 `Composite`역을 동일시하기 위한 역할
  - 이 두개의 역에 공통되는 상위 클래스로 구현
- 예제 프로그램의 `Entry` 클래스

### Client(의뢰자)
- `사용자`
- 예제 프로그램의 `Main` 클래스

## 북수와 단수 동일시 하기
- `Composite` 패턴은 그릇과 내용물을 동일시하는 패턴인데, 이를 복수와 단수의 동일시로 부를 수 있음
  - 여러 개를 모아서 마치 하나의 것처럼 취급
