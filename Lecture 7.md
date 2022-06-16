## Alist and resizing

`get(int i)` slow for long LinkedList because have to scan to desired position.

Different track: using an `array` instead, no links.

**AList Invariants:**
- The position of the next item to be inserted is always `size`.
- `size` is always the number of items in the AList.
- The last item in the list is always in position `size - 1`.

![Alist](https://joshhug.gitbooks.io/hug61b/content/chap2/fig25/full_naive_alist.png)

```java
public class AList {
  private int[] items;    
  private int size;
 
  public AList() {
    items = new int[100];  
    size = 0;
  }
 
  public void addLast(int x) {
    items[size] = x;
    size += 1;
  }
 
  public int getLast() {
    return items[size - 1];
  }
 
  public int get(int i) {
    return items[i];
  }

  public int size() {
    return size;
  }

  public int removeLast() {
    int returnItem = items[size - 1];
    //items[size - 1] = 0; Setting deleted item to zero is not necessary.
    size -= 1;  	
  return returnItem;
}

}

```

### Resizing
#### Time efficiency
The process of creating a new array and copying items over is often referred to as "resizing".
We make a new one with bigger size and copy items over.

```java
private void resize(int capacty) {
    int[] a = new int[capacity]; //Create a new array
    System.arraycopy(items, 0, a, 0, size); //Copy items over
    items = a;
}

public void addLast(int x) {
    if (size == items.length) { 
        resize(size + 1); //Create a new array with longer length.
    }
    items[size] = x;
    size += 1;
}
```

![graph](https://joshhug.gitbooks.io/hug61b/content/chap2/fig25/insert_experiment.png)

For inserting 100,000 items,requires roughly (100+101+102+...) 5,000,000,000 new containers, we can roughly compute how much longer by computing the ratio of N^2/N. Inserting 100,000 items into our array based list takes (100,000^2)/100,000 or 100,000 times as long.

**Geometric resizing** is much faster
`resize(size * Rfactor)`;

#### Memory efficiency
"usage ration" R = size / items.length

Typical solution: Half array length when R < 0.25

tradeoffs between time and space efficiency for a variety of algorithms and data structures in later course.

### Generic ALists

Create an array of references to `SomeType`

`SomeType[] a = (SomeType[]) new Object[length];`