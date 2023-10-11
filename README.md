# java_design_pattern
자바 디자인패턴 스터디

iterator = array, list 등을 순회, 참조 할 수 있는 구현체
-> 구현과 분리하여 반복 가능 + 코드가 안전해진다

Iterator ∈ Iterable ??

hasNext(), next() 등을 사용해서 순회 가능

begin(), end()는 없는지??


// arr or list 등을 생성하는 interface
public interface Iterable<E> {
    public abstract Iterator<E> iterator();
}

// Iterable로 생성된 객체를 순회하는 interface
public interface Iterator<E> {

    public abstract boolean hasNext();

    public abstract E next();
}

// 기본 단
public class Book {

    private String name;

    public Book(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

}

// iterable 을 상속받고 사용 가능
public class BookShelf implements Iterable< Book > {

    private Book[] books;
    private int last = 0;

    public BookShelf(int maxsize) {
        this.books = new Book[maxsize];
    }

    public Book getBookAt(int index) {
        return books[index];
    }

    public void appendBook(Book book) {
        this.books[last] = book;
        ++last;
    }

    public int getLength() {
        return last;
    }

    @Override
    public Iterator<Book> iterator() {
        return new BookShelfIterator(this);
    }

}

// BookShelf를 순회하는 iterator
import java.util.NoSuchElementException;

public class BookShelfIterator implements Iterator<Book> {

    private BookShelf bookShelf;
    private int index;

    public BookShelfIterator(BookShelf bookShelf) {
        this.bookShelf = bookShelf;
        this.index = 0;
    }

    @Override
    public boolean hasNext() {

        if (index < bookShelf.getLength()) {
            return true;
        }

        return false;
    }

    @Override
    public Book next() {

        if (false == hasNext()) {
            throw new NoSuchElementException();
        }

        Book book = bookShelf.getBookAt(index);
        ++index;

        return book;
    }
}
