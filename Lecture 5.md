# SLLists, Nested Classes, Sentinel Nodes
In last lecture, we bulit "naked" linked lists. While functional, hard to use because users have to know references and think recursively. 通过中间类，架起外部类接触到内部数据结构的桥梁
```java
public class IntList {
    public int first;
    public IntList rest;

    public Intlist(int f, IntList r) {
        first = f;
        rest = r;
    }

    IntList L = new IntList(15, null);
    L = new IntList(10, L);
    L = new IntList(5, L);
}
```

### 首先构造内部的数据结构class IntNode:
```java
public class IntNode {
    public int item;
    public IntNode next;

    public IntNode(int i, IntNode n) {
        item = i;
        next = n;
    }
}
```

### 然后构造中间类SLList，可以接触到内部数据结构
```java
public class SLList {
    private IntNode first; //Prevent access from outside class

    public SLList(int x) {
        first = new IntNode(x, null);
    }
}
```

#### Nested classes

```java
public class SLList {

    //Only SLList class have access to class IntNode, so make class IntNode nested;
    //1.save some meory;
    //2. private to SLList, hide from outside class;
    //3. Declare IntNode static, since it never uses any of SLList's instance variables or methods; (never look outwards)
    //static classes cannot access outer class's instance variables or methods.
    private static class IntNode { 
        public int item;
        public IntNode next;

        public IntNode(int i, IntNode n) {
            item = i;
            next = n;
        }
    }

    private IntNode first; //Prevent access from outside class

    public SLList(int x) {
        first = new IntNode(x, null);
    }

    public static void main (String[] args) {
        SLList L = new SLList(15);      //不需要null就可以直接创建实例
    }
}
```

###  给中间类添加操作内部数据结构的方法
```java
public class SLList {

    ....
    public SLList(int x) {
        first = new IntNode(x, null);
        size = 1;
    }

    /*Add x to the front of the list.*/
    public void addFirst(int x) {
        first = new IntNode(x, first);
        size ++;
        
    }

    /*Return the first item in the list .*/
    public int getFirst() {
        return first.item;
    }

    /*Add x to the end of the list.*/
    public void addLast(int x) {
        IntNode p = first;
        /*Move p until it reaches the end of the list*/
        while (p.next != null) {
            p = p.next;
        }
        p.next = new IntNode(x, null);
        size ++;
    }

    /*Return the size of the lists that starts at IntNode p*/
    // private static int size(IntNode p) {
    //     if (p.next == null) {
    //         return 1;
    //     } return 1 + size(p.next);
    // }

    /*public middle class for outside*/
    public int size() {
        return size;
    }   

    public static void main (String[] args) {
        SLList L = new SLList(15);      //不需要null就可以直接创建实例
        L.addFirst(10);
        L.addFirst(5);
        L.getFirst(); //5
    }

}
```

A better and faster way to savi important data to speed up retrieval (size or biggest/smallest item) is to maintain a special ***size*** variable that caches the size of the list.

### Instantiation of an empty list 
```java
public class SLList {
    ...
    public SLList () {
        first = null;
        size = 0;
    }
    ...
}
```

***Problem***:  addLast method throw *NullPointerException* because we check if `first.next` is null. However, first itself is `null`.

***Solution***:

1. check if first is `null` before first.next (Bad idea! Ruin code's simplicity to check special case)
2. Make all SLLists (even empty) the same. (transform first as a sentinel, store data structure in sentinel's next, so that `sentinel` is never `null`.)

```java
public class SLList {

    private static class IntNode { 
        public int item;
        public IntNode next;

        public IntNode(int i, IntNode n) {
            item = i;
            next = n;
        }
    }

    /*The first node is at sentinel.next*/
    private IntNode sentinel;
    private int size;

    /*Create a empty list*/
    public SLList() {
        sentinel = new IntNode(999, null); //sentinel's item can be any number since never be used
        size = 0;
    }

    //Overloaded methods are differentiated by the number and the type of the arguments passed into the method. In the code sample, SLList() and SLList(int x) are distinct and unique methods

    /*Create a list of x*/
    public SLList(int x) {
        sentinel = new IntNode(999,null)
        sentinel.next = new IntNode(x,null);
        size = 1;
    }

    public void addFirst(int x) {
        sentinel.next = new IntNode(x, sentinel.next);
    }

    public int getFirst() {
        return sentinel.next.item;
    }

    public void addLast(int x) {
        IntNode p = sentinel;

        while (p.next != null) {
            p = p.next;
        }
        p.next = new IntNode(x, null);
        size ++;
    }

    public int size() {
        return size;
    }

    public static void main(String[] args) {
        SLList L = new SLList(10);
        L.addFirst(5);
        L.addLast(15);

    }
}
```

**A `SLList` with a sentinel node has at least the following invariants:**

- The sentinel reference always points to a sentinel node.
- The front item (if it exists), is always at sentinel.next.item.
- The size variable is always the total number of items that have been added.