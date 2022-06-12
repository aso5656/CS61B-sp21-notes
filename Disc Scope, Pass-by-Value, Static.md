#  Scope, Pass-by-Value, Static
```java
class Book {
    public String title; // instance variable (instance attribut, self.title in python)
    public Library library;// instance variable (instance attribut, self.library in python)
    public static Book last = null; //class variable (class attribute Book.last in python)

    /*Constructor*/
    public Book(String name) {
        title = name;
        last = this;
        library = null;
    }
}

class Library {
    public Book[] books;
    public int index;
    public static int totalBooks = 0;

    /*Constructor*/
    public Library(int size) {
        books = new Book[size];
        index = 0;
    }
}
```

A **static** variable is not specific to an instance. 
All instances **share** the same static variables. (class attributes)

```java
class Book {
    ....
    /*class method, have no access to instance variables.*/
    public static String lastBookTitle() {
        return last.title;
    }

    /*instance method have access to instance variables and class variables*/
    public String getTitle() {
        return title;
    } 
}

class Library {
    ....

    public void addBook(Book book) {
        books[index] = book;
        index ++;
        totalBooks ++;
        book.Library = this;
    }
}
```
A static methods belongs to the class (class methods) but an instance has access to both instance methods and variables and class methods and variables. 

1. Change the totalBooks variable to non static (instance variable); 
   
    will compile because totalBooks is changed in method addBook, a instance method.

2. Change the lastBookTitle method to non static;
   
   will compile because last is a class variable, instance have access to class variables.

3. Change addBook to static;

    Compile error. addBook involves instance variables (books,index)

4. Change the last variable to non static;

    Compile error. lastBookTitle cannot access instance variable

5. Change the library variable to static;
   
   will compile. in addBook


  