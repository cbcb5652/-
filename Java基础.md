# Java基础

## 数据类型

**基本数据类型**

- byte	/	8
- char    /    16
- short   /    16
- int       /     32
- float   /      32
- long   /      64
- double /   64
- boolean /  ~

> boolean 只有两个值:true false  可以使用1 bit来存储,但是具体大小没有明确规定  JVM会在编译时期将boolean类型的数据转换
>
> ​									为int,1表示true  0表示false     JVM支持boolean数组,通过byte来实现的

**缓存池**

new Integer(123) 与 Integer.valueOf(123) 的区别在于：

- new Integer(123) 每次都会新建一个对象;
- Integer.valueOf(123) 会使用缓存池中的对象,多次调用会取得同一个对象的引用

```java
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y);    // false
Integer z = Integer.valueOf(123);
Integer k = Integer.valueOf(123);
System.out.println(z == k);   // true
```

valueOf() 方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

Integer缓存池的大小默认为-128~127

> 编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。

```java
Integer m = 123;
Integer n = 123;
System.out.println(m == n); // true
```

jdk1.8所有的数值类缓冲池中,Integer的缓冲池IntegerCache很特殊,-128~127 但是可以在启动jvm的时候调整

过 -XX:AutoBoxCacheMax=<size> 来指定这个缓冲池的大小该选项在 JVM 初始化的时候会设定一个名为java.lang.IntegerCache.high 系统属性，然后 IntegerCache 初始化的时候就会读取该系统属性来决定上界。

## String

String 被声明为 final，因此它不可被继承。(Integer 等包装类也不能被继承）

在 Java 8 中，String 内部使用 char 数组存储数据。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
}
```

在 Java 9 之后，String 类的实现改用 byte 数组存储字符串，同时使用 `coder` 来标识使用了哪种编码。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final byte[] value;

    /** The identifier of the encoding used to encode the bytes in {@code value}. */
    private final byte coder;
}
```

value 数组被声明为 final，这意味着 value 数组初始化之后就不能再引用其它数组。并且 String 内部没有改变 value 数组的方法，因此可以保证 String 不可变。

### 不可变好处

**1.可以缓存hash值**

因为 String 的 hash 值经常被使用，例如 String 用做 HashMap 的 key。不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算。

**2.String Pool的需要**

如果一个 String 对象已经被创建过了，那么就会从 String Pool 中取得引用。只有 String 是不可变的，才可能使用 String Pool。

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/image-20191210004132894.png)**3.安全性**

String 经常作为参数，String 不可变性可以保证参数不可变。例如在作为网络连接参数的情况下如果 String 是可变的，那么在网络连接过程中，String 被改变，改变 String 的那一方以为现在连接的是其它主机，而实际情况却不一定是。

**4.线程安全性**

String 不可变性天生具备线程安全，可以在多个线程中安全地使用。

### String StringBuffer and StringBuilder

**1.可变性**

- String 不可变
- StringBuffer 和 StringBuilder 可变

**2.线程安全**

- String 不可变，因此是线程安全的
- StringBuilder 不是线程安全的
- StringBuffer 是线程安全的，内部使用 synchronized 进行同步

### String Pool

```java
String s1 = new String("aaa");
String s2 = new String("aaa");
System.out.println(s1 == s2);           // false
String s3 = s1.intern();
String s4 = s1.intern();
System.out.println(s3 == s4);           // true
```

如果是采用 "bbb" 这种字面量的形式创建字符串，会自动地将字符串放入 String Pool 中。

```java
String s5 = "bbb";
String s6 = "bbb";
System.out.println(s5 == s6);  // true
```

> new String("abc")

使用这种方式一共会创建两个字符串对象（前提是 String Pool 中还没有 "abc" 字符串对象）。

- "abc" 属于字符串字面量，因此编译时期会在 String Pool 中创建一个字符串对象，指向这个 "abc" 字符串字面量；
- 而使用 new 的方式会在堆中创建一个字符串对象。

在将一个字符串对象作为另一个字符串对象的构造参数时,并不会完全复制value数组内容,而是都会指向同一个value数组

```java
public String(String original){
    this.value = value;
    this.has = orignal.hash;
}
```

> 传递参数

在方法里和方法外的两个指针指向了不同的对象,在一个指针改变其所指向对象的内容对另一个指针所指向的对象没有影响

```java
public class PassByValueExample {
    public static void main(String[] args) {
        Dog dog = new Dog("A");
        System.out.println(dog.getObjectAddress()); // Dog@4554617c
        func(dog);
        System.out.println(dog.getObjectAddress()); // Dog@4554617c
        System.out.println(dog.getName());          // A
    }

    private static void func(Dog dog) {
        System.out.println(dog.getObjectAddress()); // Dog@4554617c
        dog = new Dog("B");
        System.out.println(dog.getObjectAddress()); // Dog@74a14482
        System.out.println(dog.getName());          // B
    }
}
```

> 初始化顺序

静态变量和静态语句块优先于实例变量和普通语句块,静态变量和静态语句块的初始化顺序取决于它们在代码中的顺序

```java
public static String staticField = "静态变量";
```

```java
static{
    System.out.println("静态语句块");
}
```

```java
public String field = "实例变量";
```

```java
{
    System.out.println("普通语句块");
}
```

最后彩色构造函数的初始化

```java
public InitialOrderTest() {
    System.out.println("构造函数");
}
```

==存在的继承情况下,初始化顺序为:==

- 父类(静态变量,静态语句块)
- 子类 (静态变量,静态语句块)
- 父类(实例变量,普通语句块)
- 父类(构造函数)
- 子类(实例变量,普通语句块)
- 子类(构造函数)

## Object方法

> equals() 方法

#### 等价关系

**1. 自反性**

```java
x.equals(x);		// true
```

**2. 对称性 **

```java
x.equals(y) == y.equals(x);		//true
```

**3. 传递性**

```java
if (x.equals(y) && y.equals(z))
    x.equals(z); // true;
```

**4. 一致性**

多次调用equals()方法结果不变

```java
x.equals(y) == x.equals(y);		// true
```

**5. 与null的比较**

对任何不是null的对象x调用x.equals(null) 结果都为false

```java
x.equals(null);		//false
```

**实现equals方法**

- 检查是否为同一个对象的引用,如果是直接返回true
- 检查是否是同一个类型,如果不是,直接返回false
- 将Object对象进行转型
- 判断每个关键域是否相等

```java
package Interview;

import java.util.Objects;

/**
 * @author chenbin
 * @datetime 2020-08-03 9:39
 */

public class EqualExample {

    private int x;
    private int y;
    private int z;

    public EqualExample(int x, int y, int z) {
        this.x = x;
        this.y = y;
        this.z = z;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        EqualExample that = (EqualExample) o;
        return x == that.x &&
                y == that.y &&
                z == that.z;
    }

     // 必须重写hashCode方法，不然到时候，调用Hashset的时候，会显示是同一个
    @Override
    public int hashCode() {
        return Objects.hash(x, y, z);
    }
}
```

**HashCode()**

 等价的两个对象散列值一定相同,但是散列值相同的两个对象不一定相等.因为计算哈希值具有随机性,两个值不同的对象可能计算出相同的哈希值

**clone()** 

> clone()是Object的protected方法 它不是public 一个类不显示的去重写clone() 其他类就不能去调用该类实例的clone方法
>
> 而且重写clone方法的时候需要去实现Cloneable接口

```java
public class CloneExample implements Cloneable {
    private int a;
    private int b;

    @Override
    public Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

==如果重写clone方法的类没有实现Cloneable接口,调用的时候会出现CloneNotSupportedException错误==

- **浅拷贝**
  - 拷贝对象和原始对象引用类型引用同一个对象
- **深拷贝**
  - 拷贝对象和原始对象的引用类型引用不同的对象

## 重写与重载

**1.重写**

为了满足里氏替换原则,重写必须满足以下条件

- 子类方法的访问权限必须大于等于父类方法；
- 子类方法的返回类型必须是父类方法返回类型或为其子类型。
- 子类方法抛出的异常类型必须是父类抛出异常类型或为其子类型。

==使用 @Override 注解，可以让编译器帮忙检查是否满足上面的三个限制条件。==

**2.重载**

存在于同一个类中，指一个方法与已经存在的方法名称上相同，但是参数类型、个数、顺序至少有一个不同。

应该注意的是，返回值不同，其它都相同不算是重载。

# Java容器

> 容器主要包括Collection和Map两种，Collection存储着对象的集合，而Map存储着键值对的映射表

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/image-20191208220948084.png)

## Set

- **TreeSet**:  基于红黑树实现的，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如HashSet，HashSet的时间复杂度为O(1),TreeSet为O(logN)
- **HashSet:** 基于哈希表实现，支持快速查找，但是不支持有序性操作。
- **LinkedHashSet:** 具有HashSet的查找效率，并且内部使用双向链表维护元素的插入顺序

## List

- **ArrayList：**基于动态数组实现，支持随机访问
- **Vector：**和ArrayList类似，但是它是线程安全的(访问效率慢)
- **LinkedList: **基于双向链表实现，只能顺序访问，但是可以快速的在链表中插入和删除元素。不仅如此，LinkedList还可以用作栈，队列和双向链表

```java
package Interview;

import java.util.*;

/**
 * @author chenbin
 * @datetime 2020-08-05 15:05
 */

public class Collection {

    public static void main(String[] args) {
        /**
         * Set
         * SortedSet  ----> interface
         * HashSet
         * LinkedHashSet
         */
        System.out.println("----------------HashSet(无序)-------------------------");
        HashSet<Integer> hashset = new HashSet<>();
        hashset.add(4);
        hashset.add(1);
        hashset.add(3);
        printSet(hashset);
        System.out.println("----------------LinkedHashSet(有序)-------------------------");
        LinkedHashSet<Integer> linkset = new LinkedHashSet<>();
        linkset.add(4);
        linkset.add(1);
        linkset.add(3);
        printSet(linkset);
        System.out.println("----------------ArrayList(有序)-------------------------");
        ArrayList<Integer> arrayList = new ArrayList<>();
        arrayList.add(4);
        arrayList.add(1);
        arrayList.add(3);
        printList(arrayList);
        System.out.println("----------------Vector(有序)-------------------------");
        Vector<Integer> vector = new Vector<>();
        vector.add(4);
        vector.add(1);
        vector.add(3);
        printList(vector);
        System.out.println("----------------LinkedList(有序)-------------------------");
        System.out.println("----------------Queue(有序)-------------------------");
        LinkedList<Integer> linkedList = new LinkedList<>();
        linkedList.add(4);
        linkedList.add(1);
        linkedList.add(3);
        printList(linkedList);
        System.out.println("----------------PriorityQueue(无序)-------------------------");
        PriorityQueue<Integer> priorityQueue = new PriorityQueue<>();
        priorityQueue.add(4);
        priorityQueue.add(1);
        priorityQueue.add(3);
        priorityQueue.add(2);
        priorityQueue.forEach(x -> {
            System.out.print(x+"  ");
        });

    }

    public static void printSet(Set<Integer> set){
        set.forEach(x->{
            System.out.print(x+"  ");
        });
        System.out.println();
    }

    public static void printList(List<Integer> list){
        list.forEach(x -> {
            System.out.print(x+"  ");
        });
        System.out.println();
    }
}
```

## Map

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/image-20191208224757855.png)

- **TreeMap:** 基于红黑树实现
- **HashMap:** 基于哈希表实现
- **HashTable:**  和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程同时写入 HashTable 不会导致数据不一致。它是遗留类，不应该去使用它，而是使用 ConcurrentHashMap 来支持线程安全，ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁。
- **LinkedHashMap:**  使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用(LRU)顺序。

```java
package Interview.集合;

import java.util.*;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentMap;

/**
 * @author chenbin
 * @datetime 2020-08-05 16:36
 */

public class MyMap {
    public static void main(String[] args) {
        System.out.println("------------------------TreeMap(无序)----------------------------");
        TreeMap treeMap = new TreeMap();
        treeMap.put("4",44);
        treeMap.put("1",11);
        treeMap.put("3",33);
        printMap(treeMap);
        System.out.println("------------------------HashMap(无序)----------------------------");
        HashMap hashMap = new HashMap();
        hashMap.put("4",44);
        hashMap.put("1",11);
        hashMap.put("3",33);
        printMap(hashMap);
        System.out.println("------------------------concurrentHashMap(无序)----------------------------");
        ConcurrentHashMap concurrentHashMap = new ConcurrentHashMap();
        concurrentHashMap.put("4",44);
        concurrentHashMap.put("1",11);
        concurrentHashMap.put("3",33);
        printMap(concurrentHashMap);
        System.out.println("------------------------LinkedHashMap(有序)----------------------------");
        LinkedHashMap linkedHashMap = new LinkedHashMap();
        linkedHashMap.put("4",44);
        linkedHashMap.put("1",11);
        linkedHashMap.put("3",33);
        printMap(linkedHashMap);
    }

    public static void printMap(Map map){
        map.forEach((k,v) -> {
            System.out.println("key:"+k+"--> value:"+v);
        });
    }
}
```

## 容器的设计模式

**迭代器模式**

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/image-20191208225301973.png)

> Collection继承了Iterable接口，其中的iterator() 方法能够产生一个Iterato对象，通过这个对象就可以迭代遍历Collection中的元素。

**适配器模式**

java.util.Arrays.asList() 可以把数组类型转换为List类型。

 ```java
public static <T> List<T> asList(T... a)
 ```

应该注意的是 asList() 的参数为泛型的变长参数，不能使用基本类型数组作为参数，只能使用相应的包装类型数组。

```java
Integer[] arr = {1,2,3};
List list = Arrrays.asList(arr);
```

也可以使用以下方式

```java
List list = Arrays.asList(1,2,3);
```

## 源码分析

### ArrayList

> 概述：ArrayList 是基于数组实现的，所以支持快速随机访问。 RandomAccess 接口标识着改类支持快速随机访问
>
> ​			数组的默认大小为10

```java
public class ArrayList<E> extends AbstractList<E> 
    	implements List<E> ,RandomAccess,Cloneable,java.io.Serializable
```

```java
private static final int DEFAULT_CAPACITY = 10;
```

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/image-20191208232221265.png)

**1. 扩容**

> 添加元素时使用ensureCapacityInternal()方法来保证容量足够，如果不够时，需要使用grow() 方法进行扩容，新容量的大小为
>
> `oldCapacity +(oldCapacity  >> 1)` ，也就是旧容量的1.5倍。

扩容时需要调用`Arrays.copyOf()` 把数组复制到一个新的数组中，代价很高，因此创建数组的时候就需要确定大小，减少扩容次数。

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}

private void ensureCapacityInternal(int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    ensureExplicitCapacity(minCapacity);
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;
    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

**2. 删除元素**

> 需要调用System.arraycopy() 将index+1后面的元素都复制到index位置上，该操作的时间复杂度为O(N)，非常消耗

```javascript
public E remove(int index) {
    rangeCheck(index);
    modCount++;
    E oldValue = elementData(index);
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved);
    elementData[--size] = null; // clear to let GC do its work
    return oldValue;
}
```

### Vector

**1. 同步**

> 实现与ArrayList类似，但是使用了synchronized进行同步

```java
public synchronized boolean add(E e) {
    modCount++;
    ensureCapacityHelper(elementCount + 1);
    elementData[elementCount++] = e;
    return true;
}

public synchronized E get(int index) {
    if (index >= elementCount)
        throw new ArrayIndexOutOfBoundsException(index);

    return elementData(index);
}
```

**2. 扩容**

Vector的构造函数可以传入capacityIncrement参数。扩容时增长capacityIncrement。如果这个值小于等于0 扩容时每次都扩大两倍(==默认情况下 会翻倍增长==)

```java
public Vector(int initialCapacity, int capacityIncrement) {
    super();
    if (initialCapacity < 0)
        throw new IllegalArgumentException("Illegal Capacity: "+
                                           initialCapacity);
    this.elementData = new Object[initialCapacity];
    this.capacityIncrement = capacityIncrement;
}
```

```java
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                     capacityIncrement : oldCapacity);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

**3. 与ArrayList的比较**

- Vector是同步的，因此开销旧比ArrayList要大，访问速度更慢。最好使用ArrayList而不是Vector，因为同步操作完全可以由程序员自己控制。
- Vector每次扩容请求其大小的2倍(可以通过构造函数设置增长的容量)，而ArrayList是1.5倍
- **4. 替代方案**

可以使用 `Collections.synchronizedList();` 得到一个安全的ArrayList

```java
List<String> list = new ArrayList();
List<String> synList = Collections.synchronizedList(list);
```

也可以使用concurrent并发包下的CopyOnWriterArrayList类

```java
List<String> list = new CopyOnWriteArrayList();
```

### CopyOnWriteArrayList

**1. 读写分离**

- 写操作复制在一个数组上进行，读操作还是在原数组中进行，读写分离，互不影响
-  写操作需要加锁，防止并发写入时导致写入数据丢失
- 写入操作结束之后，需要把原始数组指向新的复制数组

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}

final void setArray(Object[] a) {
    array = a;
}
```

> CopyOnWriteArrayList适用于读多写少的场景。
>
> 但是CopyOnWriteArrayList有其缺陷
>
> - 内存占用：在写操作时需要复制一个新的数组，使得内存占用为原来的两倍左右
> - 数据不一致：读操作不能读到实时性的数据，因为部分写操作的数据还未同步到读数组中
>
> ==不适合内存敏感及对实时性要求很高的场景。==

### LinkedList

> 基于双向链表实现，使用Node存储链表节点信息

```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
}
```

每个链表都存储了first和last指针

```java
transient Node<E> first;
transient Node<E> last;
```

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/image-20191208233940066.png)

**与ArraList的比较**

ArrayList基于动态数组实现的。LinkedList基于双向链表。可以归结为链表和数组的区别

- 数组支持随机访问，但插入删除的代价很高，需要移动大量的元素
- 链表不支持随机访问，但是插入和删除只需改变指针位置

### HashMap

> - 大多数情况下可以直接定位到值，具有很快的访问速度。
> - 非线程安全。(ConcurrentHashMap)

![image-20200805215539341](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200805215539341.png)



![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/image-20191208234948205.png)

```java
transient Entry[] table;
```

```java
static class Entry<K,V> implements Map.Entry<K,V> {
    final K key;
    V value;
    Entry<K,V> next;
    int hash;

    Entry(int h, K k, V v, Entry<K,V> n) {
        value = v;
        next = n;
        key = k;
        hash = h;
    }

    public final K getKey() {
        return key;
    }

    public final V getValue() {
        return value;
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }

    public final boolean equals(Object o) {
        if (!(o instanceof Map.Entry))
            return false;
        Map.Entry e = (Map.Entry)o;
        Object k1 = getKey();
        Object k2 = e.getKey();
        if (k1 == k2 || (k1 != null && k1.equals(k2))) {
            Object v1 = getValue();
            Object v2 = e.getValue();
            if (v1 == v2 || (v1 != null && v1.equals(v2)))
                return true;
        }
        return false;
    }

    public final int hashCode() {
        return Objects.hashCode(getKey()) ^ Objects.hashCode(getValue());
    }

    public final String toString() {
        return getKey() + "=" + getValue();
    }
}
```

==应该注意到链表的插入是以头插法方式进行的使用链表的头插法，也就是新的键值对插在链表的头部，而不是链表的尾部。==

**扩容基本原理**

设 HashMap 的 table 长度为 M，需要存储的键值对数量为 N，如果哈希函数满足均匀性的要求，那么每条链表的长度大约为 N/M，因此查找的复杂度为 O(N/M)。

为了让查找的成本降低，应该使 N/M 尽可能小，因此需要保证 M 尽可能大

| 参数       | 含义                                                         |
| ---------- | ------------------------------------------------------------ |
| capacity   | table 的容量大小，默认为 16。需要注意的是 capacity 必须保证为 2 的 n 次方。 |
| size       | 键值对数量。                                                 |
| threshold  | size 的临界值，当 size 大于等于 threshold 就必须进行扩容操作。 |
| loadFactor | 装载因子，table 能够使用的比例，threshold = (int)(capacity* loadFactor)。  0.75 |

扩容操作同样需要把 oldTable 的所有键值对重新插入 newTable 中，因此这一步是很费时的。

> 从JDK1.8开始，一个桶储存的链表长度大于等于8时会将链表转换为红黑树

### ConcurrentHashMap

> ConcurrentHashMap是一个Segement数组，Segement数组通过继承ReentrantLock来进行加锁，所以每次加锁的操作锁住是一个segement，这样只要保证每个Segement是线程安全的，也就实现了全局的线程安全。
>
> ==segement一旦初始化之后不可以扩容==

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/image-20191209001038024.png)

ConcurrentHashMap 和 HashMap 实现上类似，最主要的差别是 ConcurrentHashMap 采用了分段锁（Segment），每个分段锁维护着几个桶（HashEntry），多个线程可以同时访问不同分段锁上的桶，从而使其并发度更高（并发度就是 Segment 的个数）。

默认的并发级别为16，也就是说默认创建16个Segment(主要保证16个线程在不同的segement就可以) ----> 不可用扩容





### LinkedHashMap

**存储结构**

继承自HashMap，因此具有和HashMap一样的快速查找特性。

```java
public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V>
```

内部维护了一个双向链表，用来维护插入顺序或者LRU顺序

```java
/**
 * The head (eldest) of the doubly linked list.
 */
transient LinkedHashMap.Entry<K,V> head;

/**
 * The tail (youngest) of the doubly linked list.
 */
transient LinkedHashMap.Entry<K,V> tail;
```

# 并发

> 有三种使用线程的方法
>
> - 实现Runnable接口
> - 实现callable接口
> - 继承Thread类

实现 Runnable 和 Callable 接口的类只能当做一个可以在线程中运行的任务，不是真正意义上的线程，因此最后还需要通过 Thread 来调用。可以理解为任务是通过线程驱动从而执行的。

## 使用线程

### 实现Runnable接口

> 需要实现接口中的run() 方法。

```java
public class MyRunnable implements Runnable{
    @Override
    public void run() {
        System.out.println("执行了run。。。");
    }
}
```

```java
public class RunTest {
    public static void main(String[] args) {
        MyRunnable instance = new MyRunnable();
        Thread thread = new Thread(instance);
        thread.start();
    }
}
```

### 实现Callable接口

> 与Runnable相比，Callable可以有返回值，返回值通过FutureTask进行封装

```java
public class MyCallable implements Callable<Integer> {
    @Override
    public Integer call()  {
        return 123;
    }
}
```

```java
public class CallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        MyCallable myCallable = new MyCallable();
        FutureTask<Integer> futureTask = new FutureTask<>(myCallable);
        Thread thread = new Thread(futureTask);
        thread.start();
        System.out.println(futureTask.get());
    }
}
```

### 继承Thread类

> 同样需要实现run()方法，因为Thread也实现了Runable接口
>
> 当调用start() 方法启动一个线程时，虚拟机将该线程放入就绪队列中等待被调度，当一个线程被调度时会执行该线程的run()

```java
public class MyThread extends Thread{
    public void run(){
        while (true){
            try {
                Thread.sleep(2000);
                System.out.println("执行了run。。。");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```java
public class ThreadTest {
    public static void main(String[] args) {
        MyThread myThread = new MyThread();
        myThread.start();
        Scanner scanner = new Scanner(System.in);
        while (true){
            System.out.println(scanner.nextInt());
        }
    }
}
```

## 中断

>  interrupt: 中断线程，提前结束，但是不能中断I/O阻塞和synchonized 所阻塞
>
> interrupted: 可以在线程内部查看是否标记了中断(套while) 从而提前结束线程

```java
public class InterruptExample {

    private static class MyThread2 extends Thread{
        @Override
        public void run() {
            int i = 0;
            while (!interrupted()){
                System.out.println("我在输出:"+(i++));
            }
            System.out.println("Thread end");
        }
    }

    public static void main(String[] args) throws InterruptedException {
        MyThread2 myThread2 = new MyThread2();
        myThread2.start();
        Thread.sleep(2000);
        myThread2.interrupt();
    }

}
```

## 互斥同步

> Java提供了两种锁机制
>
> - JVM实现的synchronized
> - JDK实现的ReentranLock

### synchronized

> - synchronized两个线程分别调用synchronized的不同对象，并不会锁住==使用synchronized锁住方法也是一样的==
>
> - 可以锁住方法` synchronized (SynchronizedExample.class)` 这样多个线程调用多个对象也会同步-
>
> - 或者可以使用synchronized锁住一个静态方法**public synchronized static void fun() {}**

### ReentrantLock

> ReentrantLock 是 java.util.concurrent(J . U .C)包中的锁

```java
public class LockExample {

    private Lock lock = new ReentrantLock();

    public void func(){
        lock.lock();
        try{
            for (int i = 0; i < 10; i++) {
                System.out.print(i+" ");
            }
        }finally {
            lock.unlock();  // 确保释放锁，从而避免发生死锁
        }
    }

    public static void main(String[] args) {
        LockExample lockExample = new LockExample();
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(() -> lockExample.func());
        executorService.execute(()->lockExample.func());
    }
}
```

> 除非需要使用ReentrantLock的高级功能，否则优先使用synchronized
>
> - synchronized 是JVM实现的一种锁机制，JVM原生支持它，但是ReentrantLock不是支持所有的JDK版本
> - synchronized 不用担心没有释放锁而导致的死锁问题。因为JVM会确保锁的释放

### join

> 在线程中调用另一个线程的join()方法，会将当前线程挂起，而不是忙等待，直到目标线程结束==(相当于插队)==

```java
public class JoinExample {

    private class A extends Thread{
        @Override
        public void run() {
            System.out.println("A");
        }
    }

    private class B extends Thread{
        private A a;
        B(A a){
            this.a = a;
        }

        @Override
        public void run() {
            try{
                a.join();
            }catch (Exception e){
                e.printStackTrace();
            }
            System.out.println("B");
        }
    }

   public void test(){
        A a = new A();
        B b = new B(a);
        b.start();
        a.start();
   }

    public static void main(String[] args) {
        JoinExample example  = new JoinExample();
        example.test();
    }
}
```

###  wait

> wait() notify() notifyAll() : 通过调用wait使得线程等待。当其他线程调用notify/notifyAll 来唤醒线程。
>
> ==必须在同步代码块中使用==  ---> 使用wait期间线程会释放锁

**wait和sleep的区别**

- wait() 是Object的方法，而sleep是Thread的静态方法；
- wait() 会释放锁，sleep() 不会

### start与run区别

1. start() 方法来启动线程，真正实现了多线程运行。这是无序等待run方法体代码执行完毕，可以直接继续执行下面的代码
2. 通过调用Thread类的start() 方法来启动一个线程，这时此线程是处于就绪状态，并没有运行。
3. 方法run() 称为线程体，它包含了要执行的这个线程的内容，线程就进入了就绪状态，开始运行run函数当中的代码。Run方法运行结束，此线程终止。然后CPU再调度其他线程。

### await

> JUC提供了更加灵活的Condition类来实现线程协调。可以在Condition上调用await使线程等待。调用signal/signalAll 唤醒线程
>
> ==await相比wait更加灵活==

```java
public class AwaitSingalExample {
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    public void before() {
        lock.lock();
        try {
            System.out.println("before");
            condition.signalAll();
        } finally {
            lock.unlock();
        }
    }

    public void after() {
        lock.lock();
        try {
            condition.await();
            System.out.println("after");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) {
        ExecutorService executorService = Executors.newCachedThreadPool();
        AwaitSingalExample example = new AwaitSingalExample();
        executorService.execute(() -> example.after());
        executorService.execute(() -> example.before());
    }
}
```

| 进入方法                                 | 退出方法                                        |
| ---------------------------------------- | ----------------------------------------------- |
| Thread.sleep() 方法                      | 时间结束                                        |
| 设置了 Timeout 参数的 Object.wait() 方法 | 时间结束 / Object.notify() / Object.notifyAll() |
| 设置了 Timeout 参数的 Thread.join() 方法 | 时间结束 / 被调用的线程执行完毕                 |
| LockSupport.parkNanos() 方法             | LockSupport.unpark(Thread)                      |
| LockSupport.parkUntil() 方法             | LockSupport.unpark(Thread)                      |

### JUC-AQS

> JUC大大提高了并发性能，AQS被认为是JUC的核心

**CountDownLatch**

维护了一个计数器 cnt，每次调用 countDown() 方法会让计数器的值减 1，减到 0 的时候，那些因为调用 await() 方法而在等待的线程就会被唤醒。

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/ba078291-791e-4378-b6d1-ece76c2f0b14.png)

```java
public class CountdownLatchExample {
    public static void main(String[] args) throws InterruptedException {
        final int totalThread = 10;
        CountDownLatch countDownLatch = new CountDownLatch(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(() -> {
            int count = totalThread;
            while (true){
                try {
                    Thread.sleep(2000);
                    countDownLatch.countDown();
                    System.out.println("run...    "+count--);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        System.out.println("I`m waiting~~");
        countDownLatch.await();
        System.out.println("end..");
        executorService.shutdown();
    }
}
```

**CycBarrier**

> 用来控制多个线程互相等待，只有当多个线程都到达时，这些线程才会继续执行。
>
> - 和CountDownLatch一样都是通过计数器来实现的，调用await()之后计数器会减1
> - CycBarrier可以通过reset() 方法来归零，可以循环使用，所以才叫循环屏障
> - CyclicBarrier 有两个构造函数，其中 parties 指示计数器的初始值，barrierAction 在所有线程都到达屏障的时候会执行一次。

```java
public CyclicBarrier(int parties, Runnable barrierAction) {
    if (parties <= 0) throw new IllegalArgumentException();
    this.parties = parties;
    this.count = parties;
    this.barrierCommand = barrierAction;
}

public CyclicBarrier(int parties) {
    this(parties, null);
}
```

![img](https://cs-notes-1256109796.cos.ap-guangzhou.myqcloud.com/f71af66b-0d54-4399-a44b-f47b58321984.png)

```java
public class CyclicBarrierExample {
    public static void main(String[] args) throws InterruptedException {
        final int totalThread = 10;
        CyclicBarrier cyclicBarrier = new CyclicBarrier(totalThread);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalThread; i++) {
            int count = i;
            Thread.sleep(1000);
            executorService.execute(() -> {
                System.out.println("before.. 我是线程"+count);
                try {
                    cyclicBarrier.await();
                }catch (Exception e){
                    e.printStackTrace();
                }
                System.out.println("释放了--after..");
            });
        }
        executorService.shutdown();
    }
}
```

CountDownLatch 和 CyclicBarrier 都能够实现线程之间的等待，只不过它们侧重点不同；

- CountDownLatch： 用于某个线程A等待若干其他线程执行完成任务之后，它才执行。
- CyclicBarrier ： 一般用于一组线程互相等待至某个状态，然后这一组线程再同时执行。
- CountDownLatch是不能重用的，CyclicBarrier 可以重用

**Semaphore**

> 类似操作系统中的信号量，可以控制对互斥资源的访问线程数。

```java
public class SemaphoreExample {
    public static void main(String[] args) {
        final int clientCount = 3;
        final int totalRequestCount = 10;
        Semaphore semaphore = new Semaphore(clientCount);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < totalRequestCount; i++) {
            executorService.execute(() ->{
                try{
                    semaphore.acquire();
                    System.out.println(semaphore.availablePermits()+" ");
                }catch (Exception e){
                    e.printStackTrace();
                }finally {
                    semaphore.release();
                }
            });
        }
        executorService.shutdown();
    }
}
```

### JUC其他组件

#### FutureTask

> 可以有返回值，返回值通过Future封装。Future实现了RunnableFuture接口，该接口继承自Runnable和Future接口，
>
> 使得FutureTask 可以当作一个任务执行，也可以有返回值。

```java
public class FutureTask<V> implements RunnableFuture<V>
```

```java
public interface RunnableFuture<V> extends Runnable, Future<V>
```

==当一个任务需要执行很长时间可以使用FutureTask 封装结果，主线程完成自己任务之后再去获取结果==

```java
public class FutureTaskExample {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<Integer> futureTask = new FutureTask<>(new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                int result = 0;
                for (int i = 0; i < 100; i++) {
                    Thread.sleep(10);
                    result += i;
                }
                return  result;
            }
        });

        Thread computeThread = new Thread(futureTask);
        computeThread.start();

        Thread otherThread = new Thread(() ->{
           System.out.println("other task is running ....");
           try{
               Thread.sleep(1000);
           }catch (Exception e){
               e.printStackTrace();
           }
        });
        otherThread.start();
        System.out.println(futureTask.get());
    }
}
```

#### BlockingQueue

> java.util.concurrent.BlockingQueue 接口有以下阻塞队列的实现：
>
> - **FIFO队列**：LinkedBlockingQueue、ArrayBlockingQueue（固定长度）
> - **优先级队列**: PriorityBlockingQueue

提供了阻塞的 take() 和 put() 方法：如果队列为空 take() 将阻塞，直到队列中有内容；如果队列为满 put() 将阻塞，直到队列有空闲位置。

```java
public class ProducerConsumer  {

    // 初始化的时候必须有值
    private static BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);

    private static class Producer extends Thread{
        @Override
        public void run() {
            try{
                queue.put("product");
            }catch (Exception e){
                e.printStackTrace();
            }
            System.out.println("produce....");
        }
    }

    private static class Consumer extends Thread{
        @Override
        public void run() {
            try{
                String product = queue.take();
            }catch (Exception e){
                e.printStackTrace();
            }
            System.out.println("consume...");
        }
    }

    public static void main(String[] args) {
        for (int i = 0; i < 2; i++) {
            Producer producer = new Producer();
            producer.start();
        }

        for (int i = 0; i < 5; i++) {
            Consumer consumer = new Consumer();
            consumer.start();
        }

        for (int i = 0; i < 3; i++) {
            Producer producer = new Producer();
            producer.start();
        }
    }
}
```

![image-20200807165815410](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200807165815410.png)

#### ForkJoin

> 主要用于并行计算中，和MapReduce原理类似。都是把大任务拆分成小任务

```java
public class ForkJoinExample extends RecursiveTask<Integer> {

    private final int threshold = 5;
    private int first;
    private int last;

    public ForkJoinExample(int first, int last) {
        this.first = first;
        this.last = last;
    }

    @Override
    protected Integer compute() {
        int result = 0;
        if (last - first <= threshold) {
            // 任务足够小则直接计算
            for (int i = first; i <= last; i++) {
                result += i;
            }
        } else {
            // 拆分成小任务
            int middle = first + (last - first) / 2;
            ForkJoinExample leftTask = new ForkJoinExample(first, middle);
            ForkJoinExample rightTask = new ForkJoinExample(middle + 1, last);
            leftTask.fork();
            rightTask.fork();
            result = leftTask.join() + rightTask.join();
        }
        return result;
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ForkJoinExample example = new ForkJoinExample(1, 10000);
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        Future result = forkJoinPool.submit(example);
        System.out.println(result.get());
    }
}
```

### 非同步阻塞

**CAS**(比较并交换 - 乐观锁机制 - 锁自旋) Compare And Swap/Set

> 乐观锁需要操作和冲突检测这两个步骤具备原子性，这里不能使用互斥同步来保证了。只能靠硬件来完成。
>
> 硬件支持的原子性操作最典型的是：比较并交换(Compare-and-Swap , CAS)。
>
> CAS指令需要三个参数，分别是内存地址v，旧的预期值A和新值B。只有当V的值等于A时，才将V值更新为B

CAS操作是抱着乐观的态度进行的(乐观锁),它总是认为自己可以完成操作。当多个线程同时使用CAS操作一个变量时，只有一个会胜出，并且成功更新，其余均会失败。失败的线程不会被挂起，仅是被告知失败，并且允许再次尝试，当然也允许失败的线程放弃操作。

**AtomicInteger**

JUC包里面的整数原子类 AtomicInteger  调用了Unsafe的CAS操作。

以下代码使用了AtomicInteger执行了自增的操作。

```java
private AtomicInteger cnt = new AtomicInteger();

public void add() {
    cnt.incrementAndGet();
}
```

以下代码是 AtomicInteger执行自增的操作

```java
public final int incrementAndGet() {
    return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
}
```

以下代码是getAndAddInt() 源码，var1指示对象内存地址，var2指示该字段对象内存地址的偏移，var4指示操作需要加的数值，

这里为1.通过getIntVolatile(var1, var2) 得到旧的预期值。

通过调用compareAndSwapInt()  来进行CAS比较，如果该字段内存地址中的值等于var5，那么就更新内存地址为var1+var2的变量为var5+var4

可以看到 getAndAddInt() 在一个循环中进行，发生冲突的做法是不断的进行重试。

```java
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

    return var5;
}
```

**ABA问题**

> CAS会导致ABA问题，==CAS算法实现一个重要前提需要取出内存某时刻的数据，而在下时刻比较并替换，那么在这个时间差类会导致数据的变化。
>
> 例如：一个线程one从内存位置V中取出A，这个时候另一个线程two也从内存中取出A，并且two进行了一些操作把A变成了B，然后two又将V位置的数据变成A，这时候线程one进行CAS操作发现内存中的仍然是A，然后one操作成功。尽管线程one的CAS操作成功，但是不代表这个过程是没问题的。

乐观锁通过版本号的问题来解决ABA问题，每次执行数据修改操作时，都会带上一个版本号，一旦版本号和数据的版本号一致就可以操作并对版本号执行+1，否则操作失败。因为每次操作的版本号都会随之增加，所以不会出现ABA问题，因为版本号只会增加不会减少。

**栈封闭**

> 多个线程访问同一个方法的局部变量时，不会出现线程安全问题，因为局部变量存储在虚拟机栈中，属于线程私有的。

```java
public class StackClosedExample {
    public void add100() {
        int cnt = 0;
        for (int i = 0; i < 100; i++) {
            cnt++;
        }
        System.out.println(cnt);
    }
}
```

```java
public static void main(String[] args) {
    StackClosedExample example = new StackClosedExample();
    ExecutorService executorService = Executors.newCachedThreadPool();
    executorService.execute(() -> example.add100());
    executorService.execute(() -> example.add100());
    executorService.shutdown();
}
```

以上代码不会出现任何线程问题

**volatile**

> 用来确保变量的更新操作通知到其他线程。volatile变量具备了两种特性，变量不会被缓存在寄存器中或者对其他处理器不可见的地方，因此在读取 volatile 类型的变量时总会返回最新写入的值。
>
> - 变量可见性
>   - 这里的可见性是指，当一个线程修改了变量的值，那么新的值对于其他线程也是可见的
> - 禁止指令重排序‘

适用场景：一个变量被多个线程共享，线程直接给这个变量赋值



## Java锁

**乐观锁**

> 一种乐观思想，认为读多写少，遇到并发写的可能性低，每次去拿数据的时候都认为别人不会修改。不上锁。
>
> ==但是在更新的时候回判断一下再次期间别人有没有去更新这个数据，采取在写时先读出当前版本，然后加锁操作==

java中的乐观锁基本都是通过CAS操作实现的，CAS是一种更新的原子操作，比较当前值跟传入值是否一样，一样则更新，否则失败

**悲观锁**

> 悲观思想，认为写多，每次读写数据都会上锁。这样别人读写这个数据就会拿到block直到拿到锁。
>
> java中的悲观锁就算Synchronized，AQS框架下的锁则是先尝试cas乐观锁去获取锁，获取不到再转换为悲观锁。

**自旋锁**

优点：

- 减少了线程的阻塞，对于锁的竞争不激烈，只占用非常短的代码块->性能大幅提升(自旋的消耗会小于线程阻塞挂起再唤醒的操作的消耗，这些操作会导致线程发生两次上下文切换)
- 自旋锁一直占用cpu做无用功，同时有大量线程在竞争一个锁，会导致回去锁的时间很长，线程自旋的消耗大于线程阻塞挂起操作的消耗，其他需要cpu的线程又不能获取到cpu，造成cpu浪费。

**Synchronized同步锁**

> 可以把任意一个非null 的对象当作锁。它属于独占式的悲观锁，同时属于可重入锁

作用范围

1. 作用于方法时，锁住的是对象的实例(this)
2. 当作用于静态方法时，锁住的是Class实例，又因为Class的相关数据存储在永久代，永久代是全局共享的，因此静态方法锁相当于类的一个全局锁，会锁所有调用该方法的线程；
3. 当synchronized作用于一个对象实例时，锁住的是所有以该对象为锁的代码块。它有多个队列，当多个线程一起访问某个对象监视器的时候，对象监视器会将这些线程存储在不同的容器中。





## 使用多线程开发良好的实践

- 给线程起一个有意义的名字，这个可以方便找bug
- 缩小同步范围，从而减少锁征用。例如synchronized，应该尽量使用同步代码块而不是同步方法
- 多用同步工具少用wait() 和 notify()。 首先，CountDownLatch, CyclicBarrier, Semaphore 和 Exchanger 这些同步类简化了编码操作，而用 wait() 和 notify() 很难实现复杂控制流；
- 使用BlockingQueue实现生产者消费者问题
- 多用并发集合少用同步集合，例如应该使用ConcurrentHashMap 而不是Hashtable
- 使用本地变量和不可变类来保证线程安全
- 使用线程池而不是直接创建线程，这是因为创建线程代价很高，线程池可以有效的利用有限的线程来启动任务

**ThreadPoolExecutor**

```java
public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize, long keepAliveTime, 
TimeUnit unit, BlockingQueue<Runnable> workQueue) { 
 this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, 
 Executors.defaultThreadFactory(), defaultHandler); 
}
```

- corePoolSize: 指定了线程池中线程的数量
- maximumPoolSize： 指定了线程池中最大的线程数量
- keepAliveTime：当前线程池数量超过corePoolSize时，多余的空闲线程的存活时间，即多次时间时间内会被销毁
- unit：KeepAliveTime的单位
- workQueue：任务队列，被提交但尚未被执行的任务
- threadFactory：线程工厂，用于创建线程，一般用于默认的即可
- handler：拒绝策略，当任务太多来不及处理，如何拒绝任务

> 几种拒绝策略:
>
> 1. AbortPolicy ： 直接抛出异常，阻止系统正常运行。
> 2. CallerRunsPolicy: 只要线程池未关闭，该策略直接在调用者线程中，运行当前被丢弃的任务。
> 3. DiscardOldestPolicy: 丢弃最老的一个请求，也就是即将被执行的一个任务，并尝试再次提交当前任务。
> 4. DiscardPolicy: 该策略默默地丢弃无法处理的任务，不予任何处理。如果允许任务丢失，这是最好的一种方案。

## ConcurrentHashMap

**减小锁粒度**

> 缩小粒度是指缩小锁定对象的范围，从而减小锁冲突的可能性，从而提高并发能力。
>
> ConcurrentHashMap(高性能的HashMap)类的实现。
>
> Segment的大小也被称为ConcurrentHashMap的并发度。

**ConcurrentHashMap分段锁**

它内部细分了若干个小的HashMap，称之为段(Segment),默认情况下一个ConcurrentHashMap被进一步划分为16个段。(并发度)

ConcurrentHashMap 是由Segment 数组结构和HashEntry数组结构组成。(锁角色)。HashEntry用于存储键值对数据。

每个Segment守护一个HashEntry数组里的元素，当对HashEntry数组的数据进行修改时，必须首先获得它对应的Segment锁。

## java中的线程调度

### 抢占式调度

> 指每条线程执行时间，线程切换都由系统控制(每条线程都分同样的执行时间片)，==可能某些线程执行时间较长，得不到时间片==
>
> 这种机制下一个线程的阻塞不会导致整个进程堵塞

### 协同式调度

> 指某一线程执行完后主动通知系统切换到另一线程上执行，想接力赛一样一个人跑完给下一个人跑。
>
> 缺点: 如果一个线程编写有问题，可能导致整个系统奔溃



![image-20200810091445343](C:\Users\晨边\AppData\Roaming\Typora\typora-user-images\image-20200810091445343.png)

### JVM的线程调度实现

> java使用的线程调度使用抢占式，java线程会按优先级分配CPU时间片运行，==切优先级越高越优先执行，但优先级高并不代表能独占执行时间片==，可能是优先级越高得到越多得到的时间片，反之，优先级低的分到的执行时间少但是不到执行时间片。





















































