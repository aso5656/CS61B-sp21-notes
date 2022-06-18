## Subtype Polymorphism vs. Higher Order Functions

Polymorphism:
>providing a single interface to entities of different types
`deque.addFirst()`
deque的compile-time type是Deque，不管他的run-time type是什么，比如LinkedDeque或者ArrayDeque，deque.addFirst()都会根据当前run-time type，实现相同的功能。这就是Polymorphism。

Suppose we want to write a function max() that returns the max of any array, regardless of type.

```java
Dog d1 = new Dog("Clifford", 9000);
Dog d2 = new Dog("Doge", 5);
Dog d3 = new Dog("Grigometh", 200);
Dogs[] dogs = {d1, d2, d3};
Dog maxDog = (Dog) max(dogs);
maxDog.bark();
```

```java
Maximizer.java
public static Object max(Object[] items) {
    int maxIndex = 0;
    for (int i = 0; i < items.length; i++) {
        if (items[i] > items[maxIndex]) { 
        // operator ">" not working for types other than integer
            maxIndex = i;
        }
    }
    return items[maxIndex];
}
```

Bad solution: Leave it to Dog class

The fundamental problem: Objects cannot be compared to other objects with >
Solution:
- `Comparable` interface guarantees `compareTo` method
- implement `Comparable` in Dog class(make Dog object can use `compareTo`), and override `compareTo` method
- max function use object.compareTo(otherObject) to compare

```java
Maximizer.java
public static Comparable max(Comparable[] items) {//takes Comparable arguments, making sure it has compareTo method
    int maxIndex = 0;
    for (int i = 0; i < items.length; i++) {
        if (items[i].compareTo(items[maxIndex]) > 0) { 
            maxIndex = i;
        }
    }
    return items[maxIndex];
}

public class Dog implements Comparable<Dog> {
    ...

    @Override
    /*Returns negative if size smaller
    *0 if size equal
    *positive if size bigger    */
    public int compareTo(Dog otherDog) {
        //compareTo implies "Natural Order". Here means by default, compare dogs by size
        return this.size - otherDog.size;
    }
}
```

What if order objects in a different way? Example: by dog name.
The standard Java approach: Create `sizeComparator` and `nameComparator` classes that implement the Comparator interface.
- `Comparator` guarantees `compare` method to compare two objects.
  
```java
public class Dog {
    ...

    public static Comparator<Dog> getNameComparator() {
        return new NameComparator();
    }

    //Make nested class private
    private class NameComparator implements Comparator<Dog> {
        @Override
        /*Returns negative if smaller
        *0 if size equal
        *positive if bigger    */
        public int compare(Dog a, Dog b) {
            //dog.name is String object which has a default compareTo method
            return a.name.compareTo(b);
        }
    }
}
```

```java
Comparator<Dog> nc = Dog.getNameComparator();
if (nc.compare(dogs[0], dogs[1]) > 0) {
    dogs[0].bark();
} else {
    dogs[3].bark();
}
```

- `Comparable` guarantees objects of some type comparable using `compareTo` method; 实现了Comparable接口的类有一个特点，就是这些 类是可以和自己比较的. compareTo方法也被称为自然比较方法
- `Comparator` specify a way to compare objects using `compare` method. 个性化比较：如果实现类没有实现Comparable接口，又想对两个类进行比较（或者实现类实现了Comparable接口，但是对compareTo方法内的比较算法不满意），那么可以实现Comparator接口，自定义一个比较器，写比较算法。
Project 1 - MaxArrayDeque