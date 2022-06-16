## Hypernyms, Hyponyms, and Interface Inheritance

We will formalize this relationship in Java: if a SLList is a hyponym of List61B, then the SLList class is a subclass of the List61B class and the List61B class is a superclass of the SLList class.

![List61B](https://joshhug.gitbooks.io/hug61b/content/assets/subclass.png)

express this hierarchy in Java:
1. Define a type for the general list hypernym (List61B)
2. Specify that SLList and AList are hypernym of that type

The new `List61B` is what Java calls an interface

```java
/** Specify what hyponyms of the type must be able to do (subclass inherits signatures), can provide default implementations; 
*/
public interface List61B<Item> {
    public void addFirst(Item x);
    public void add Last(Item y);
    public Item getFirst();
    public Item getLast();
    public Item removeLast();
    public Item get(int i);
    public void insert(Item x, int position);
    public int size();

    /**Default implementation for print(), can be overidden by hyponyms*/
    default public void print() {
        for (int i = 0; i < size(); i++) {
                System.out.print(get(i) + " ");
        }
        System.out.println();
    }
}

/** AList is a List61B, must override all 
*/
public class AList<Item> implements List61B<Item> {
    ...

    @override
    public Item get(int i) {
        return items[i]
    }

}
```

When it runs, and SLList is created and its address is stored in the someList variable. Then the string "elk" is inserted into the SLList referred to by addFirst
```java
public static void main(String[] args) {
    List61B<String> someList = new SLList<String>();
    someList.addFirst("elk");
}
```


`List61B<String> lst = new SLList<String>();`

In the above declaration and instantiation, lst is of type "List61B". This is called the "static type". The object that lst points to is of type SLList.
When Java runs a method that is **overriden**, it searches for the appropriate method signature in it's dynamic type and runs it. **Not work for overloaded methods** (same name, different signature)


```java
public interface Animal {
    default void greet(Animal a) {
        System.out.print("Hello animal");
    }
    default void sniff(Animal a) {
        System.out.print("Sniff animal");
    }
    default void praise(Animal a) {
        System.out.print("cool animal");
    }
}

public class dog implements Animal {
    
    @override
    public void sniff(Animal a) { //sniff is overriden, has same signature.
        System.out.print("dog sniff animal");
    }

    public void praise(Dog a) { //praise is overloaded not overriden because signature is different.
        print("cool dog");
    }
}

Animal a = new Dog();
Dog d = new Dog();
a.greet(d); //Hello animal
a.sniff(d); //dog sniff animal
d.praise(d); //cool dog  call Dog.praise(d)
a.praise(d); //cool animal  call Animal.praise(d)

```

**When you are creating these hierarchies, remember that the relationship between a subclass and a superclass should be an "is-a" relationship.**
Cat should only implement Animal Cat is an Animal. You should not be defining them using a "has-a" relationship. Cat has-a Claw, but Cat definitely should not be implementing Claw.