# Lecture 4. References, Recursion, and Lists.
## The golden rule of equals (pass by value)
y = x copies all the ***bits*** from x into y

## Declaring a variable
- computer set asiade exactly enough bits to hold a thing of that type. E.g. an int set aside a box of 32 bits,double 64 bits;

int x; ------> x |________________

double y; ---->y |_________________________________

### Assignment
#### Primitive type
Fill the bits with value.

x = -1431195969;

x |<u>10101010110001101011101011111 </u>

To simplify: x | <u>-1431195969 </u>

```java
int x = 5;
int y;
y = x;
x = 2;
y == 5; //True
```

#### Reference type
Declaring a variable of any reference type allocates a box of 64bits. Bits can either be set to **Null** or **address** of a specific instance (returned by **new** keyword)
1. allocates a box of bits for each instance variables (attributes) filled with the default value (0 or null)
2. Then constructor fills every box with other value
3. **new** keyword returns where the instance is stored (address)
   
To simplify:  box and pointer

```java
Walrus a;
a = new Walrus(1000,8.3); 
Walrus b;
b = a; //copies all bits from a into b; a & b point to the same instance
```

#### Parameter passing
Copy the bits to new scope

```java
public static void main(String[] args) {
    Walrus walrus = new Walrus(3500,10.5);
    int x = 9;
    doStuff(walrus, x);
    //walrus.weight == 3400; True
    //x == 4 False, x == 9 True
}
public static void doStuff(Walrus w, int x){
    w.weight -= 100;
    x = x - 5;
}
```
