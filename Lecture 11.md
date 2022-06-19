## Exceptions, Iterators, Object Methods

### Lists in Real Java Code
```java
import java.util.List;
import java.util.ArrayList;
import java.util.LinkedList;
// Lists
List<Integer> AL = new ArrayList<>();
List<Integer> LL = new LinkedList<>();

import java.util.set;
import java.util.HashSet;
//Store a set of values with no duplicates. No sense of order.
Set<String> S = new HashSet<>();
```
Create a class ArraySet based on ArrayList
```java
public class ArraySet<T> {
    private T[] items;
    private int size;

    public ArraySet() {
        items = (T[]) new object[100];
        size = 0;
    }

    public size() {
        return size;
    }

    public boolean contains(T x) {
        for (int i = 0; i < size; i++) {
            if (items[i].equals(x)) {
                return true;
            }
        } return false;
    }

    public void add(T x) {
        if (!contains(x)) {
            items[size] = x;
            size += 1;
        }
    }
}
```
Adding a null is possible, but adding next item will throw `java.lang.NullPointerException`
We can also throw our own exceptions using the throw keyword. 
- Can provide more informative message to a user.
- Can provide more information to code that “catches” the exception.
```java
public void add(T x) {
  if (x == null) {
    throw new IllegalArgumentException("Cannot add null!");
  }
  ...
}

```

### Iteration
Build out own class that supports "enhanced for" loop
The code above is just shorthand for the code below.
```java
Set<Integer> javaset = new HashSet<Integer>();

for (int x : javaset) {
   System.out.println(x);
}


```
```java
Iterator<Integer> seer = javaset.iterator();

while (seer.hasNext()) {
  System.out.println(seer.next());
}

```
To support the enhanced for loop:
1. Add `implements Iterable<T>` to the line defining your class.
2. Add an iterator() method to your class that returns an Iterator<T>.
3. The Iterator<T> returned should have a useful hasNext() and next() method.

```java
import java.util.List;
import java.util.ArrayList;
import java.lang.Iterable;
import java.util.Iterator;

public class ArraySet<T> implements Iterable<T> {
    private T[] items;
    private int size;

    public ArraySet() {
        items = (T[]) new Object[100];
        size = 0;
    }
    
    public Iterator<T> iterator() {
        return new ArraySetIterator();
    }

    private class ArraySetIterator implements Iterator<T> {
        private int pos;
        public ArraySetIterator() {
            pos = 0;
        }

        public boolean hasNext() {
            return pos < size;
        }

        public T next() {
            T item = items[pos];
            return item;
        }

    }
}
```

### Object Methods
#### toString()
toString() method provides a string representation of an object.
```java
// Slow. Strings are immutable, add + operation creates new string.
@Override
public String toString() {
    String returnString = "{";
    for (int i = 0; i < size; i += 1) {
        returnString += keys[i];
        returnString += ", ";
    }
    returnString += "}";
    return returnString;
}

```

Append operation for a StringBuilder is fast. 
```java
@Override
public String toString() {
    StringBuilder returnSB = new StringBuilder("{");
    for (int i = 0; i < size; i += 1) {
        returnSB.append(items[i]);
        returnSB.append(", ");
    }
    returnSB.append("}");
    return returnSB.toString();
}

//Other way to do it.
@Override
public String toString() {
    List<String> listofItems = new ArrayList<>();
    for (T item : this) {
        listofItems.add(item.toString());
    }
    return "{" + String.join(", ", listofItems) + "}"
}

```
#### equals()
Default implementation of `.equals` uses `==`, `==` compares the bits. It means “referencing the same object.” (not what we want sometimes.)

```java
@Override
public boolean equals(Object o) {
  if (o == null) { return false; }
  if (this == o) { return true; } // optimization
  if (this.getClass() != o.getClass()) { return false; }
  ArraySet<T> other = (ArraySet<T>) o;
  if (this.size() != other.size()) { return false; }
    for (T item : this) {
      if (!other.contains(item)) {
        return false;
      }
    }
  return true;
}

```