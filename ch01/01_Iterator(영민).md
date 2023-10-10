# 제 1부. 디자인 패턴에 익숙해지다

## Part 1. Interator - 처리를 반복한다

### Iterator 패턴
Java 언어에서 arr 배열의 모든 요소를 표시하려면 다음과 같이 for문을 사용한다
```java
for (int i = 0; i < arr.length; i++ {
  System.out.println(arr[i];
}
```

#### for문의 작동원리를 살펴보자
1. 여기서 사용되는 변수 i는 처음에 0으로 초기화되고 1,2,3 ... 증가한다
2. 그때마다 arr[i]의 내용이 표시된다
3. i++에서 i를 하나씩 증가시키면 현재 주목하는 요소는 '다음', '그다음' 으로 진행
4. i를 늘려가다 보면배열 arr의 요소 전체를 처음부터 순서대로 검색하게 된다

여기서 사용하는 i의 기능을 추상화하여 일반화 한 것을 `Iterator 패턴` 이라고 한다
- 무엇인가 많이 모여있을 때 이를 순서대로 가리키며 전체를 검색하고 처리를 반복하는 것
- iterate는 '반복하다'라는 뜻
- iterator를 반복자 라고도 부른다

### Iterator 예제 프로그램
책장(BookShelf)안에 책(Book)을 넣고, 책 이름을 차례로 표시하는 프로그램

<img width="189" alt="image" src="https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/c6bb0055-ce32-4029-9c35-aef048816f31">

1. Iterable<E> 인터페이스
   - 처리를 반복할 대상을 나타내는 것으로, java.lang 패키지에 선언되어 있다
   - 이 인터페이스를 구현하는 클래스는 배열처럼 '무엇인가가 많이 모여 있는 것' 즉, '집합체'가 된다
   - iterable은 영어로 '반복할 수 있다', '반복 가능' 이라는 뜻
   - 즉 반복 가능한 집합체가 된다 라고 이해할 수 있다
   - <E>는 타입 파라미터를 나타내며 여기에 '모여 있는 것'을 나타내는 타입
   - 예제 프로그램에서는 Book을 모은 집합체로 Iterable<Book> 형태로 사용
     ```java
     public interface Iterable<E> {
       public abstract Iterator<E> iterator();
     }

     // iterator 메서드는 이 집합체에 대응하는 Iterator<E> 를 만들기 위함
     // 집합체에 요소를 하나하나 처리하고 싶을 때는 ITERATOR 메서드를 사용해
     // Iterable<E> 인터페이스를 구현한 클래스의 인스턴스를 하나 만든다
     ```
  
2. Iterator<E> 인터페이스
   - Iterator<E> 인터페이스는 하나하나의 요솔르 반복처리하기 위한 것
   - 루피 변수와 같은 역할 (for 문에서의 i)
     ```java
     public interface Iterator<E> {
       public abstract boolean hasNext();
       public abstract E next();

     // hasNext : 다음 요소가 있는 확인하는 메서드 (boolean)
     //  - 값이 있으면 true, 값이 없으면 false
     // next : 다음 요소를 가져오는 메서드 (E)
     //  - 집합체의 요소 1개를 반환 후, 다음 요소를 반환할 수 있도록 내부 상태를 진행
     }

3. Book 클래스
   - 이 예제에서 Book 객체를 나타내는 클래스
   - 구체적인 설명은 생략
   - 기본적인 생성자와 getName() 함수로 이루어져 있다.
  
4. BookShelf 클래스
   - 책장을 나타내는 클래스로 Iterable<Book> 인터페이스를 구현한 클래스
   - Iterable<Book> 인터페이스에서 선언되어 있던 iterator 메서드의구현부가 있음
   - Book의 배열인 Books 필드가 있음
     ```java
     public class BookShelf implements Iterable<Book> {
       private Book[] books;
       ... 기타 등등 ...

       @Override
       public Iterator<Book> iterator() {
         return new BookShelfIterator(this);
       }
       // BookShelf 클래스에 대응하는 Iterator로서 BookShelfIterator 클래스의 인스턴스 반환
     }
     ```
      
5. BookShelfIterator 클래스
   - BookShelf 클래스의 검색을 실행하는 BookShelfIterator 클래스
   - Iterable<Book> 인터페이스를 구현하므로 Iterator<Book> 형으로 다룸
   - bookShelf 필드는 BookShelfIterator가 검색할 책장
   - index는 현재 보고 있는 책을 가리킴
     ```java
     public class BookShelfIterator implements Iterator<Book> {
       private BookShelf bookShelf;
       private int index;
      
       public BookShelfIterator(BookShelf bookShelf) {
         this.bookShelf = bookShelf;
         this.index = 0;
       }

       // Iterator<Book>의 hasNext() 메서드의 구현부
       // 다음 책이 있는지 조사하고 있으면 true, 없으면 false
       @Override
       public boolean hasNext() {
         if (index < bookShelf.getLength()) {
           return true;
         } else {
           return false;
         }
       }

       // 현재 주목하고 있는 책(Book)을 반환하고 다음을 진행시키는 메서드
       @Override
       public Book next() {
         if (!hasNext()) {
           throw new NoSuchElementException();
         }
         Book book = bookShelf.getBookAt(index);
         index++;
         return book;
       }
     }
     ```

6. Main 클래스
   - 책이 4권 들어가는 책장을 선언
   ```java
   public class Main {
     public static void main(String[] args) {
       BookShelf bookShelf = new BookShelf(4);
       bookShelf.appendBook(new Book("Around the World in 80 Days"));
       bookShelf.appendBook(new Book("Bible"));
       bookShelf.appendBook(new Book("Cinderella"));
       bookShelf.appendBook(new Book("Daddy-Long-Legs"));
    
       // 명시적으로 Iterator를 사용하는 방법 
       Iterator<Book> it = bookShelf.iterator(); // 책장을 검색할 때 사용되는 Iterator
       while (it.hasNext()) {
         Book book = it.next();
         System.out.println(book.getName());
       }
       System.out.println();
    
       // 확장 for문을 사용하는 방법
       // 위의 코드와 완전히 같은 동작을 한다
       // Iterator를 사용한 반복처리를 간결하게 기술할 수 있음
       // 확장 for문은 내부적으로 Iterator를 사용하여 처리
       for (Book book: bookShelf) {
         System.out.println(book.getName());
       }
       System.out.println();
     }
   }
   ```
   - 실행 결과 : <img width="136" alt="image" src="https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/53348fa9-c3db-461e-aca9-06d4101b817b">



### Iterator 패턴의 등장인물
<img width="293" alt="image" src="https://github.com/Ju-Yeongmin/java_design_pattern/assets/110506500/42b81b9b-1746-46c2-a8a7-6b7a5e475ea4">


### 사고를 넓혀주는 힌트

#### 어떻게 구현하든 Iterator를 사용할 수 있다!
왜 번거롭게 Iterator 패턴을 사용할까? 배열이면 for문을 사용하면 되는데 왜 Iterator를 사용하는가?
- 가장 큰 이유는 Iterator를 사용함으로써 구현과 분리하여 반복할 수 있다
  ```java
  while(it.hasnext()) {
    Book book = it.next();
    System.out.println(book.getName());
  }

  // 여기서 사용한 것은 hasNext와 next 메서드 뿐 BookShelf 구현에 사용된 메서드는 호출되지 않는다
  // 즉, while 루프는 BookShelf 구현에 의존하지 않는다
  ```
- BookShelf를 구현한 사람이 배열 대신 ArrayList를 사용하도록 프로그램을 변경했다면?
  -> 어떻게 변경하든 iterator 메서드를 가지고 있다면 위의 while 루프는 변경하지 않아도 동작한다


#### 추상 클래스와 인터페이스 사용하자
추상 클래스나 인터페이스 사용법을 모르는 사람은 Iterable<E>나 Iterator<E> 같은 인터페이스 없이
구체적인 클래스만을 사용해서 프로그래밍 한다
- 하지만 구체적인 클래스만 사용하면 재사용하기 어렵다
- 클래스를 재사용하기 쉽게 하고자 추상클래스 / 인터페이스를 도입하자
- 구체적인 클래스로만 프로그래밍하지 말고 추팡클래스나 인터페이스를 활용해서 프로그래밍하는 습관을 들일 것

#### next() 의 정확한 의미
next의 반환값은 현재 처리하는 요소일까 아니면 다음 요소일까?
- 정확하게 말하면 '현재 요소를 반환하고 다음 위치로 진행한다' 이다
  ```java
    return CurrentElementAndAdvanceToNextPosition
  ```

#### deleteIterator는 필요 없다!
java에서는 사용하지 않는 인스턴스는 자동으로 GC에 의해 삭제되므로 deleteIterator 메서드는 필요 없다


















