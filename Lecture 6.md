## DLList and Arrays

The issue with `addLast` method of `SLList` class is that it is slow because it walks though the entire list. We can attempt to speed things up by adding a `last` variable. 

```java
public class SLList {
    private IntNode sentinel;
    private IntNode last;
    private int size;    

    ... 

     public SLList () { 
        sentinel = new IntNode(999, null);
        size = 0;
        //Special case: last cam point at sentinel node.
    }

    public SLList(int x) {
        sentinel = new IntNode(999, null);
        sentinel.next = new IntNode(x,null);
        last = sentinel.next;
        size = 1;
    }

    public void addLast(int x) {
        last.next = new IntNode(x, null);
        last = last.next;
        size += 1;
    }

    public int getLast() {
        return last.item;
    }
    ...
}
```

**Problem：** `addLast` and `getLast` will be fast, but `removeLast` will still be slow because `last` has to point second last item.

**Solution:** Add a pointer points at previous node. This yields a "Double Linked List" or `DLList`.

```java
public class DLList {

    private static class IntNode { 
        public IntNode prev;
        public int item;
        public IntNode next;
    
        public IntNode (IntNode p, int i, IntNode n) {
            prev = p;
            item = i;
            next = n;
        }
    }

    private IntNode sentinel; //outside class have no access to first;
    private int size;
    private IntNode last;

    /*Empty list */
    public DLList () {
        sentinel = new IntNode(null,999, null);
        size = 0;
    }

    public D  LList(int x) {
        sentinel = new IntNode(null,999, null);
        sentinel.next = new IntNode(sentinel,x,null);
        last = sentinel.next;
        size = 1;
    }

    public void addFirst(int x) {
        IntNode f = new IntNode(sentinel, x, sentinel.next);
        sentinel.next.prev = f;
        sentinel.next = f;
        size ++;
    }

    public int getFirst() {
        return sentinel.next.item;
    }

    public void addLast(int x) {
        last.next = new IntNode(last, x, null);
        last = last.next;
        size ++;
    }

    public int getLast() {
        return last.item;
    }

    public void removeLast() {
        last = last.prev;
        last.next = null;
        size --;
    }


    public int size() {
        return this.size;
    }
    public static void main(String[] args) {
        DLList L = new DLList(10);
        L.addFirst(5);
        L.addLast(15);
        L.removeLast();
        L.removeLast();
        //L.removeLast() After running this method, last will point at sentinel. We want to avoid that.
        System.out.println(L.getLast());
    }
}

```
**Problem:**As the `main` method above, remove all last items will cause `last` pointing at `sentinel`.

**Solution 1:** Add a back sentinel;

![](https://joshhug.gitbooks.io/hug61b/content/chap2/fig23/dllist_double_sentinel_size_0.png)


**Solution 2:** Make linked list circular with a single sentinel in middle.

![circualr](https://joshhug.gitbooks.io/hug61b/content/chap2/fig23/dllist_circular_sentinel_size_0.png)
![](https://joshhug.gitbooks.io/hug61b/content/chap2/fig23/dllist_circular_sentinel_size_2.png)

In a circular `DLList`, last is `sentinel`'s previous node. And the last's next item is `sentinel`.

```java
public class DLList {
    private static class IntNode { 
        public IntNode prev;
        public int item;
        public IntNode next;
    
        public IntNode (IntNode p, int i, IntNode n) {
            prev = p;
            item = i;
            next = n;
        }
    }

    private IntNode sentinel; //outside class have no access to first;
    private int size;

    /*Empty list */
    public DLList () {
        sentinel = new IntNode(null,999, null);
        sentinel.prev = sentinel;
        sentinel.next = sentinel;
        size = 0;
    }

    public DLList(int x) {
        sentinel = new IntNode(null,999, null);
        sentinel.next = sentinel;
        sentinel.next = new IntNode(sentinel,x,sentinel);
        sentinel.prev = sentinel.next;
        size = 1;
    }

    public void addFirst(int x) {
        IntNode f = new IntNode(sentinel, x, sentinel.next);
        sentinel.next.prev = f;
        sentinel.next = f;
        size ++;
    }

    public int getFirst() {
        return sentinel.next.item;
    }

    public void addLast(int x) {
        //sentinel.prev is last
        sentinel.prev.next = new IntNode(sentinel.prev, x, sentinel);
        sentinel.prev = sentinel.prev.next;
        size ++;
    } 

    public int getLast() {
        return sentinel.prev.item;
    }

    public void removeLast() {
        sentinel.prev = sentinel.prev.prev;
        sentinel.prev.next = null;
        size --;
    }
    public int size() {
        return this.size;
    }

    public static void main(String[] args) {
        DLList L = new DLList();
        L.addFirst(5);
        L.addLast(10);
        L.addFirst(1);
        L.addLast(15);
        L.removeLast();

        System.out.println(L.getLast());
    
    }
}

```

### Generic lists
- When implementing data structure, specify "generic type" only once at the very top of the file.
- When using data structure, specify desired type once:
    - Write out desired type during declaration
    - use <> during instantiation.

```java
public class DLList<TypeHolder> {
    private static class IntNode { 
        public IntNode prev;
        public TypeHolder item;
        public IntNode next;
    
        public IntNode (IntNode p, TypeHolder i, IntNode n) {
            prev = p;
            item = i;
            next = n;
        }
    }
    ...
}

//Instantiation of a new DDList, only 
DLList<String> s1 = new DLList<>("first");
```