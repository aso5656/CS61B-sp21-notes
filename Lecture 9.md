## Extends, Casting, Higher Order Functions

### Extends
When a class is a hyponym of an interface, we use `implements`
- `SLList<Blorp> implements List61B<Blorp>`

If one class is to be a hyponym of another class, use `extends`

By using the extends keyword, subclasses inherit all members of the parent class. "Members" includes:

- All instance and static variables
- All methods
- All nested classes
  
Note that `constructors` are not inherited, and `private` members cannot be directly accessed by subclasses.
private声明的方法不能被子类覆写，但可以通过父类的方法调用

While constructors are not inherited, Java requires that all constructors must start with a call to one of its superclass's constructors

```java
public class VengefulSLList<Item> extends SLList<Item> {
    SLList<Item> deletedItems;

    public VengefulSLList() {
        super(); 
        //Java will automatically make a call to the superclass's no-argument constructor for us.
        //or we can either explicitly make a call to the superclass's constructor, using the super keyword:
        deletedItems = new SLList<Item>();
    }

    //Suppose we had a one-argument constructor that took in an item
    public VengefulSLList(Item x) {
        super(x);  //we must make an explicit call to the correct constructor by passing in the item as a parameter to super.
        deletedItems = new SLList<Item>();
    }

    @Override
    public Item removeLast() {
        Item x = super.removeLast(); // call the removeLast method defined in the parent class, SLList, using the super keyword.
        deletedItems.addLast(x);
        return x;
    }

    public void printLostItems() {
        deletedItems.print();
    }
}

```

Classes that do not have an explicit `extends` in their class still implicitly extend the `Object class`.

**Note: The extends keyword defines "is-a", or hypernymic relationships**  


### Encapsulation 封装
The root of encapsulation lies in this notion of hiding information from the outside. 

Take the `ArrayDeque` class, for example. The outside world is able to utilize and interact with an `ArrayDeque` through its defined methods, like `addLast` and `removeLast`. However, they need not understand the complex details of how the data structure was implemented

Using the `private` keyword in Java, it becomes virtually impossible to look inside an object - ensuring that the underlying complexity isn't exposed to the outside world.

#### How Inheritance Breaks Encapsulation
```java
public class Dog {
    ...

    public void bark() {
        barkMany(1);
    }

    public void barkMany(int N) {
        for (int i = 0; i < N; i += 1) {
            System.out.println("bark");
        }
    }
}

public class verboseDog extends Dog {

    @Override
    public void barkMany(int N) {
        System.out.println("As a dog say");
        for (int i = 0; i < N; i += 1) {
            bark();
        }
    }
}

vd.barkMany(3)
//call this.bark(), doesn't exist so go to superclass call bark();
// bark() excutes and goes to this.barkMany(1);
// this.barkMany(1) call bark() again ...infinite loop.
```

### Type Checking and Casting
#### Rules
编译时期是以静态类型为准，运行时是以动态类型为准。
1. Compiler allows memory box to hold any subtype
2. Compiler allows calls based on static type
3. Overriden non-static methods are selected at run time based on dynamic type

![type](https://joshhug.gitbooks.io/hug61b/content/assets/dynamic_selection.png)

```java
//The compiler determines whether or not something is valid based on the static type of the object
sl.addLast(50); //VengefulSLList did not override or implement addLast, so this is executed in SLList;
sl.removeLast(); // Overriden method run in dynamic type, in VengefulSLList
sl.printLostItems(); // compile-time error. printLostItems not implemented in SLList
VengefulSLList<Integer> vsl2 = sl; // compile-time error.  SLList is not a VengefulSLList, so cannot hold SLList

```

```java
SLList<Integer> sl = new VengefulSLList<Integer>(); //VengefulSLList "is-a" SLList, and allows this assignment
VengefulSLList<Integer> vsl = new SLList<Integer>(); //compiler checks if SLList "is-a" VengefulSLList, thus a compilation error
```

Method calls have compile-time types equal to their declared type
```java
// Poodle is Dog, Dog is not Poodle
public static Dog maxDog(Dog d1, Dog d2) { ... }

Poodle frank = new Poodle("Frank", 5);
Poodle frankJr = new Poodle("Frank Jr.", 15);

Dog largerDog = maxDog(frank, frankJr); 
Poodle largerPoodle = maxDog(frank, frankJr); //compile-time error, Dog is not a poodle 
```

### Casting
With casting, we can tell the compiler to view an expression as a different compile-time type.

`Poodle largerPoodle = (Poodle) maxDog(frank, frankJr); ` Right hand side has compile-time type Poodle after casting，by tricking the compiler.

```java
Poodle frank = new Poodle("Frank", 5);
Malamute frankSr = new Malamute("FrankSr", 100);

Poodle largerPoodle = (Poodle) maxDog(frank, frankJr);  //runtime exception - a ClassCastException. We try casting a Malamute as a Poodle

```


#### Typing Puzzle

```java
public class Dog {
    public void bark() {
        System.out.print("Dog bark");
        // Implements bark() method
    }
}

public class ShowDog extends Dog {

    @Override
    public void bark() {
        System.out.print("ShowDog bark");
        //Overide bark() method
    }
}
```

```java
Object o2 = new ShowDog("Mort","Corgi",25,512);
//o2 Static type: Object; Dynamic type: ShowDog
// ShowDog is Object. Allowed

ShowDog sdx = ((ShowDog) o2);
// treat o2 static type as ShowDog. ShowDog is ShowDog. Allowed
// //sdx Static type: ShowDog; Dynamic type: ShowDog
sdx.bark()
// ShowDog has bark method overriden, ShowDog.bark()

Dog dx = ((Dog) o2);
// Temprarily treat o2 as static type Dog. Dog is Dog. Allowed
//dx Static type:Dog; Dynamic type: ShowDog
dx.bark()
// Dog has bark method overriden by ShowDog. ShowDog.bark()

((Dog) o2).bark();
// Temprarily treat o2 as static type Dog. Dog has bark method overriden by ShowDog. ShowDog.bark()

Object o3 = (Dog) o2;
//Temprarily treat o2 as static type Dog. Dog is Object. Allowed
//o2 Static type: Object; Dynamic type: ShowDog
o3.bark();
//Object has no bark method, error
```