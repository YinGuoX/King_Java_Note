# Java集合问答

- 主要参考自：https://snailclimb.gitee.io/javaguide-interview/#/./docs/b-2Java%E9%9B%86%E5%90%88

## 1. List、Set、Map的区别

- List：主要处理顺序元素：存储的元素是有序、可重复的
- Set：主要处理独一无二元素：存储的元素是无序、不可重复的
- Map：主要处理Key搜索：使用键值对存储，key是无序、不可重复的，value是无序，可重复的，一个键最多映射到一个值

## 2. ArrayList、LinkedList区别

- 线程安全：
  - ArrayList和LinkedList都不是同步的，都不保证线程安全
- 底层数据结构：
  - ArrayList底层使用的是Object数组
  - LinkedList底层使用的是双向链表
    - JDK1.6之前是循环链表
- 插入、删除是否受元素位置影响：
  - ArrayList采用数组存储，插入和删除元素的时间复杂度受元素位置的影响
    - 执行`add(E e)`方法的时候， `ArrayList` 会默认在将指定的元素追加到此列表的末尾，这种情况时间复杂度就是 O(1)。但是如果要在指定位置 i 插入和删除元素的话（`add(int index, E element)`）时间复杂度就为 O(n-i)。因为在进行上述操作的时候集合中第 i 和第 i 个元素之后的(n-i)个元素都要执行向后位/向前移一位的操作。
  - LinkedList采用链表存储，插入和删除元素的时间复杂度不受元素位置的影响
    - **对于`add(E e)`方法的插入，删除元素时间复杂度不受元素位置的影响，近似 O(1)，如果是要在指定位置`i`插入和删除元素的话（`(add(int index, E element)`） 时间复杂度近似为`o(n))`因为需要先移动到指定位置再插入。**
- 快速随机访问：
  - ArrayList支持，能够通过元素的序号快速获取元素对象
  - LinkedList不支持
- 内存空间占用：
  - ArrayList的空间浪费：主要是在list列表结尾会预留一定的容量空间
  - LinkedList的空间浪费：每个元素都需要消耗比ArrayList更多的空间
- **双向链表：** 包含两个指针，一个 prev 指向前一个节点，一个 next 指向后一个节点。
- **双向循环链表：** 最后一个节点的 next 指向 head，而 head 的 prev 指向最后一个节点，构成一个环。
- RandomAccess接口：
  - 在我看来 `RandomAccess` 接口不过是一个标识。标识实现这个接口的类具有随机访问功能。
  - 在 `binarySearch（)` 方法中，它要判断传入的 list 是否 `RamdomAccess` 的实例，如果是，调用`indexedBinarySearch()`方法，如果不是，那么调用`iteratorBinarySearch()`方法
  - `ArrayList` 实现了 `RandomAccess` 接口，表明了具有快速随机访问功能。
  - `LinkedList` 没有实现`RandomAccess`接口

## 3. Vector、ArrayList区别

- ArrayList：是List的主要实现类，底层使用Object[] 存储，适合用于频繁的查找工作，线程不安全
- Vector：是List的古老实现类，底层使用Object[]存储，线程安全

## 4. ArrayList扩容机制

- https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ArrayList%E6%BA%90%E7%A0%81+%E6%89%A9%E5%AE%B9%E6%9C%BA%E5%88%B6%E5%88%86%E6%9E%90?id=_2-arraylist-%e6%a0%b8%e5%bf%83%e6%ba%90%e7%a0%81%e8%a7%a3%e8%af%bb

### 4.1 ArrayList构造函数

```java
   /**
     * 默认初始容量大小
     */
    private static final int DEFAULT_CAPACITY = 10;


    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    /**
     *默认构造函数，使用初始容量10构造一个空列表(无参数构造)
     */
    public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }

    /**
     * 带初始容量参数的构造函数。（用户自己指定容量）
     */
    public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {//初始容量大于0
            //创建initialCapacity大小的数组
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {//初始容量等于0
            //创建空数组
            this.elementData = EMPTY_ELEMENTDATA;
        } else {//初始容量小于0，抛出异常
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }


   /**
    *构造包含指定collection元素的列表，这些元素利用该集合的迭代器按顺序返回
    *如果指定的集合为null，throws NullPointerException。
    */
     public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }

```

- **以无参数构造方法创建 `ArrayList` 时，实际上初始化赋值的是一个空数组。当真正对数组进行添加元素操作时，才真正分配容量。即向数组中添加第一个元素时，数组容量扩为 10。**

- > JDK6 new 无参构造的 `ArrayList` 对象时，直接创建了长度是 10 的 `Object[]` 数组 elementData 

### 4.2 ArrayList扩容机制

```java
// 1.add方法
    /**
     * 将指定的元素追加到此列表的末尾。
     */
    public boolean add(E e) {
   //添加元素之前，先调用ensureCapacityInternal方法
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        //这里看到ArrayList添加元素的实质就相当于为数组赋值
        elementData[size++] = e;
        return true;
    }

// 2.ensureCapacityInternal方法
// 当 要 add 进第 1 个元素时，minCapacity 为 1，在 Math.max()方法比较后，minCapacity 为 10。
   //得到最小扩容量
    private void ensureCapacityInternal(int minCapacity) {
        if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
              // 获取默认的容量和传入参数的较大值
            minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
        }

        ensureExplicitCapacity(minCapacity);
    }

// 3. ensureExplicitCapacity方法
// 当add进第1个元素时，elementData.lengt=0,此时minCapacity=10;
// 所以会进入if判断里面，执行grow(10)方法

// 当 add 第 2 个元素时，minCapacity 为 2，此时 e lementData.length(容量)在添加第一个元素后扩容成 10 了。此时，minCapacity - elementData.length > 0 不成立
// 所以不会进入 （执行）grow(minCapacity) 方法。

// 直到添加第 11 个元素，minCapacity(为 11)比 elementData.length（为 10）要大。进入 grow 方法进行扩容。

  //判断是否需要扩容
    private void ensureExplicitCapacity(int minCapacity) {
        modCount++;

        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            //调用grow方法进行扩容，调用此方法代表已经开始扩容了
            grow(minCapacity);
    }


// 4.grow方法
// 当 add 第 1 个元素时，oldCapacity 为 0，经比较后第一个 if 判断成立，newCapacity = minCapacity(为 10)。但是第二个 if 判断不会成立，即 newCapacity 不比 MAX_ARRAY_SIZE 大，则不会进入 hugeCapacity 方法。数组容量为 10，add 方法中 return true,size 增为 1。
// 当 add 第 11 个元素进入 grow 方法时，newCapacity 为 15，比 minCapacity（为 11）大，第一个 if 判断不成立。新容量没有大于数组最大 size，不会进入 hugeCapacity 方法。数组容量扩为 15，add 方法中 return true,size 增为 11。

    /**
     * 要分配的最大数组大小
     */
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;

    /**
     * ArrayList扩容的核心方法。
     */
    private void grow(int minCapacity) {
        // oldCapacity为旧容量，newCapacity为新容量
        int oldCapacity = elementData.length;
        //将oldCapacity 右移一位，其效果相当于oldCapacity /2，
        //我们知道位运算的速度远远快于整除运算，整句运算式的结果就是将新容量更新为旧容量的1.5倍，
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        //然后检查新容量是否大于最小需要容量，若还是小于最小需要容量，那么就把最小需要容量当作数组的新容量，
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
       // 如果新容量大于 MAX_ARRAY_SIZE,进入(执行) `hugeCapacity()` 方法来比较 minCapacity 和 MAX_ARRAY_SIZE，
       //如果minCapacity大于最大容量，则新容量则为`Integer.MAX_VALUE`，否则，新容量大小则为 MAX_ARRAY_SIZE 即为 `Integer.MAX_VALUE - 8`。
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

// 5.hugeCapacity方法
//从上面 grow() 方法源码我们知道： 如果新容量大于 MAX_ARRAY_SIZE,进入(执行) hugeCapacity() 方法来比较 minCapacity 和 MAX_ARRAY_SIZE，如果 minCapacity 大于最大容量，则新容量则为Integer.MAX_VALUE，否则，新容量大小则为 MAX_ARRAY_SIZE 即为 Integer.MAX_VALUE - 8

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        //对minCapacity和MAX_ARRAY_SIZE进行比较
        //若minCapacity大，将Integer.MAX_VALUE作为新数组的大小
        //若MAX_ARRAY_SIZE大，将MAX_ARRAY_SIZE作为新数组的大小
        //MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }


// 6.System.arraycopy方法
    // 我们发现 arraycopy 是一个 native 方法,接下来我们解释一下各个参数的具体意义
    /**
    *   复制数组
    * @param src 源数组
    * @param srcPos 源数组中的起始位置
    * @param dest 目标数组
    * @param destPos 目标数组中的起始位置
    * @param length 要复制的数组元素的数量
    */
    public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);

// 7. add(index,element)方法
    /**
     * 在此列表中的指定位置插入指定的元素。
     *先调用 rangeCheckForAdd 对index进行界限检查；然后调用 ensureCapacityInternal 方法保证capacity足够大；
     *再将从index开始之后的所有成员后移一个位置；将element插入index位置；最后size加1。
     */
    public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // Increments modCount!!
        //arraycopy()方法实现数组自己复制自己
        //elementData:源数组;index:源数组中的起始位置;elementData：目标数组；index + 1：目标数组中的起始位置； size - index：要复制的数组元素的数量；
        System.arraycopy(elementData, index, elementData, index + 1, size - index);
        elementData[index] = element;
        size++;
    }

// 测试System.arraycopy
public class ArraycopyTest {

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        int[] a = new int[10];
        a[0] = 0;
        a[1] = 1;
        a[2] = 2;
        a[3] = 3;
        System.arraycopy(a, 2, a, 3, 3);
        a[2]=99;
        for (int i = 0; i < a.length; i++) {
            System.out.print(a[i] + " ");
        }
    }

}

// 8.Arrays.copyOf()方法
    public static int[] copyOf(int[] original, int newLength) {
        // 申请一个新的数组
        int[] copy = new int[newLength];
    // 调用System.arraycopy,将源数组中的数据进行拷贝,并返回新的数组
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }

// 9. toArray方法
   /**
     以正确的顺序返回一个包含此列表中所有元素的数组（从第一个到最后一个元素）; 返回的数组的运行时类型是指定数组的运行时类型。
     */
    public Object[] toArray() {
    //elementData：要复制的数组；size：要复制的长度
        return Arrays.copyOf(elementData, size);
    }

// 个人觉得使用 Arrays.copyOf()方法主要是为了给原有数组扩容
// 测试
public class ArrayscopyOfTest {

    public static void main(String[] args) {
        int[] a = new int[3];
        a[0] = 0;
        a[1] = 1;
        a[2] = 2;
        int[] b = Arrays.copyOf(a, 10);
        System.out.println("b.length"+b.length);
    }
}
```

- Java 中的 `length`属性是针对数组说的,比如说你声明了一个数组,想知道这个数组的长度则用到了 length 这个属性.
- java 中的 `length()` 方法是针对字符串说的,如果想看这个字符串的长度则用到 `length()` 这个方法.
- java 中的 `size()` 方法是针对泛型集合说的,如果想看这个泛型有多少个元素,就调用此方法来查看!
- System.arraycopy和Arrays.copyOf的联系和区别
  - **联系：**看两者源代码可以发现 `copyOf()`内部实际调用了 `System.arraycopy()` 方法
  - **区别：**`arraycopy()` 需要目标数组，将原数组拷贝到你自己定义的数组里或者原数组，而且可以选择拷贝的起点和长度以及放入新数组中的位置 ，`copyOf()` 是系统自动在内部新建一个数组，并返回该数组

```java
// ArrayList 源码中有一个 ensureCapacity 方法，这个方法 ArrayList 内部没有被调用过，所以很显然是提供给用户调用的，那么这个方法有什么作用呢

    /**
    如有必要，增加此 ArrayList 实例的容量，以确保它至少可以容纳由minimum capacity参数指定的元素数。
    
     *
     * @param   minCapacity   所需的最小容量
     */
    public void ensureCapacity(int minCapacity) {
        int minExpand = (elementData != DEFAULTCAPACITY_EMPTY_ELEMENTDATA)
            // any size if not default element table
            ? 0
            // larger than default for default empty table. It's already
            // supposed to be at default size.
            : DEFAULT_CAPACITY;

        if (minCapacity > minExpand) {
            ensureExplicitCapacity(minCapacity);
        }
    }

// 最好在 add 大量元素之前用 ensureCapacity 方法，以减少增量重新分配的次数
// 测试方法
public class EnsureCapacityTest {
    public static void main(String[] args) {
        ArrayList<Object> list = new ArrayList<Object>();
        final int N = 10000000;
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < N; i++) {
            list.add(i);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("使用ensureCapacity方法前："+(endTime - startTime));

    }
}
使用ensureCapacity方法前：2158

public class EnsureCapacityTest {
    public static void main(String[] args) {
        ArrayList<Object> list = new ArrayList<Object>();
        final int N = 10000000;
        list = new ArrayList<Object>();
        long startTime1 = System.currentTimeMillis();
        list.ensureCapacity(N);
        for (int i = 0; i < N; i++) {
            list.add(i);
        }
        long endTime1 = System.currentTimeMillis();
        System.out.println("使用ensureCapacity方法后："+(endTime1 - startTime1));
    }
}
使用ensureCapacity方法后：1773
```

## 5. HashMap、HashTable区别

- 线程安全：
  - HashMap：是线程不安全的=》可以使用ConcurrentHashMap，保证线程安全
  - HashTable：是线程安全的，内部的方法基本都使用了synchronized修饰
- 效率：
  - HashMap：因为不是线程安全的，因此HashMap的效率更高一点
  - HashTable：已经基本被淘汰了，
- null key和null value：
  - HashMap：可以存储null的key和value，null作为key只能有一个，null作为值可以有多个
  - HashTable：不允许有null的key和value，否则会抛出NullPointerException
- 初始容量和每次扩容大小：
  - HashMap：默认初始化大小为16，每次扩充为原来的2n，如果给定大小，会扩充为2的幂次方大小(tableSizeFor())
  - HashTable：默认初始化大小为11，每次扩容为原来的2n+1，如果给定大小就是给定大小
- 底层数据结构：
  - HashMap：为了更好的解决哈希冲突，当链表长度大于阈值（默认为8）时，将链表转化为红黑树，以减少搜索时间
    - 将链表转换为红黑树之前会判断，如果数组长度小于64，则会进行数组扩容，而不是转化为红黑树
  - HashTable：数组+链表

## 6. HashMap、HashSet区别

- `HashSet` 底层就是基于 `HashMap` 实现的。（`HashSet` 的源码非常非常少，因为除了 `clone()`、`writeObject()`、`readObject()`是 `HashSet` 自己不得不实现之外，其他方法都是直接调用 `HashMap` 中的方法。

  | `HashMap`                              | `HashSet`                                                    |
  | -------------------------------------- | ------------------------------------------------------------ |
  | 实现了 `Map` 接口                      | 实现 `Set` 接口                                              |
  | 存储键值对                             | 仅存储对象                                                   |
  | 调用 `put()`向 map 中添加元素          | 调用 `add()`方法向 `Set` 中添加元素                          |
  | `HashMap` 使用键（Key）计算 `hashcode` | `HashSet` 使用成员对象来计算 `hashcode` 值，对于两个对象来说 `hashcode` 可能相同，所以` equals()`方法用来判断对象的相等性 |

## 7. HashSet如何检查重复

- 当把对象add进入HashSet时：

  - HashSet先计算对象的hashcode值来判断对象加入的位置，同时也会与其他加入的对象的hashcode对比
    - 如果没有相等的hashcode：HashSet就会认为该对象没有重复出现，可以放入对应的位置
    - 如果有相等的hashcode，就会调用equals()方法来检查hashcode相等的对象是否真的相同
      - 如果两者对象相同，则不会让其add操作成功

- hashCode()和equals()：

  - 如果两个对象相等，则hashcode一定是相同的
  - 两个对象相等，则equals()返回ture;
  - 两个对象的hashcode相等，但是其内容不一定相等
  - 因此一个类的equals()方法被覆盖过，则hashCode()方法也要被覆盖

- 为什么equals()方法被覆盖，hahsCode()方法也要被覆盖？

  - 因为即使两个对象的内容相等(equals为ture)，但是因为没有重写hahsCode()方法，所以Hash Set是调用类继承的Object的hashCode()方法，导致两个对象内容相同的对象有不同的hashCode()，进而导致相同对象都可以插入到HashSet中

  - ```java
    import java.util.HashSet;
    
    public class People {
        String idCard;
    
        public People(String idCard) {
            this.idCard = idCard;
        }
    
        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            People people = (People) o;
            return idCard.equals(people.idCard);
        }
    
        public static void main(String[] args) {
            People a = new People("a");
            People a1 = new People("a");
            // output: true
            System.out.println(a.equals(a1));
    
            HashSet<People> set = new HashSet<>();
            set.add(a);
            set.add(a1);
            // output: 2
            System.out.println(set.size());
        }
    }
    ```

- ==和equals()：

  - 对于基本数据类型来说：
    - ==是比较值是否相等
  - 对于引用类型来说：
    - ==是比较两个引用是否指向同一个对象地址
    - equals()：
      - 如果没有被重写，也是比较地址是否相等
      - 如果被重写，如String，则是比较对象的具体内容是否相等

## 8. HashMap的底层实现

### 8.1 JDK1.8之前

- JDK1.8之前：

  - HashMap底层是数组+链表结合在一起使用（链表散列）
    - HashMap通过key的hashCode经过扰动函数处理=》获取hash值
    - 再通过(n-1)&hash判断当前元素的存放位置(其中n是数组长度)
      - 如果当前位置存在元素的话，就判断该元素与要存入的元素的hash和key是否相同
        - 如果相同，则直接覆盖
        - 如果不同，则用拉链法解决冲突
  - 扰动函数=》即使HashMap的hash方法：
    - 使用hash方法可以为了防止一些实现比较差的hashCode()，也就是减少碰撞

- ```java
  // JDK1.8 源码
      static final int hash(Object key) {
        int h;
        // key.hashCode()：返回散列值也就是hashcode
        // ^ ：按位异或
        // >>>:无符号右移，忽略符号位，空位都以0补齐
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
  
  // JDK1.7源码
  // JDK 1.7 的 hash 方法的性能会稍差一点点，因为毕竟扰动了 4 次
  static int hash(int h) {
      // This function ensures that hashCodes that differ only by
      // constant multiples at each bit position have a bounded
      // number of collisions (approximately 8 at default load factor).
  
      h ^= (h >>> 20) ^ (h >>> 12);
      return h ^ (h >>> 7) ^ (h >>> 4);
  }
  ```

### 8.2 JDK1.8及之后

- JDK1.8及之后：当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间。
  - ![jdk1.8之后的内部结构-HashMap](Java集合问答.assets/jdk1.8之后的内部结构-HashMap.png)
- 为什么要转换为红黑树？
  - 解决二叉查找树的缺陷，避免在最极端的情况下链表变得很长很长，在查询的时候，效率会非常慢。
  - 红黑树查询：访问性能近似于折半查找，时间复杂度 O(logn)；
  - 链表查询：这种情况下，需要遍历全部元素才行，时间复杂度 O(n)

## 9. HashMap的长度为什么都是2的幂次方

- 为什么？

  - 为了能让 HashMap 存取高效，尽量较少碰撞，也就是要尽量把数据分配均匀。
  - Hash  值的范围值-2147483648到2147483647，前后加起来大概40亿的映射空间，只要哈希函数映射得比较均匀松散，一般应用是很难出现碰撞的。
  - 但问题是一个40亿长度的数组，内存是放不下的。所以这个散列值是不能直接拿来用的。用之前还要先做对数组的长度取模运算，得到的余数才能用来要存放的位置也就是对应的数组下标。这个数组下标的计算方法是“ `(n - 1) & hash`”。（n代表数组长度）。这也就解释了 HashMap 的长度为什么是2的幂次方。

- 如何实现？为什么不直接hash%n?

  - 取余(%)操作中如果除数是2的幂次 等价于 hash&(n-1)，其中n是数组长度，并且为2的幂次
  - 并且相对于%，&操作能够提高运算效率

- 再次解释为什么要是2的幂次方？

  - ```java
    // JDK1.8 源码
        static final int hash(Object key) {
          int h;
          // key.hashCode()：返回散列值也就是hashcode
          // ^ ：按位异或
          // >>>:无符号右移，忽略符号位，空位都以0补齐
          return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
      }
    ```

  - hash能够让key散列的更加均匀，随机化

  - hash&(n-1)可以恰好得到底为掩码，导致hash的低位才会参与计算索引，高位都被底位掩码给截取了，如果传进去的实体的hashCode设计的不好，低位基本差不多的话那么hash碰撞就会大概率的发生。所有右移hash值的一半n-1位，把高位与低位异或下，尽可能的消除这种影响

  - 总结：

    - 减少哈希冲突
    - 加快哈希计算
    - 完美扩容转移：在扩容时，利用扩容后的大小也是2的倍数，将已经产生hash碰撞的元素完美的转移到新的table中去，这种方法完美的避免了再次去进行哈希碰撞，仅只用了O（n）的时间复杂度，就将数据完成了转移

- 如何保证大小是2的幂次方？

  - 在初始化的时候，会调用tableSizeFor()来确保数组大小是2的幂次方

## 10. HashMap多线程的问题

- 重点可以看这个：https://coolshell.cn/articles/9606.html
- 主要原因：并发下的rehash会造成元素之间形成一个循环链表。jdk1.8之后解决了这个问题，但是还是不建议再多线程下使用HashMap。因此还可能存在其他问题：数据丢失。。。
- 并发环境下推荐使用JUC的ConcurrentHashMap

## 11. ConcurrentHashMap、Hashtable的区别

- 底层数据结构：
  - jdk1.7的ConcurrentHashMap底层采用分段数组+链表
  - jdk1.8采用的和HashMap一样，数组+链表+红黑树
  - Hashtable采用的是数组+链表
- 线程安全方面：
  - **在 JDK1.7 的时候，`ConcurrentHashMap`（分段锁）** 对整个桶数组进行了分割分段(`Segment`)，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。总的来说就是：**Segment 数组 + HashEntry 数组 + 链表**
  - **JDK1.8 的时候已经摒弃了 `Segment` 的概念，而是直接用 `Node` 数组+链表+红黑树的数据结构来实现，并发控制使用 `synchronized` 和 CAS 来操作。**总的来说就是：**Node 数组 + 链表 / 红黑树**
  - **`Hashtable`(同一把锁)** :使用 `synchronized` 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。
  - ![HashTable全表锁](Java集合问答.assets/HashTable全表锁.png)
  - ![JDK1.7的ConcurrentHashMap](Java集合问答.assets/ConcurrentHashMap分段锁.jpg)
  - ![Java8 ConcurrentHashMap 存储结构（图片来自 javadoop）](Java集合问答.assets/java8_concurrenthashmap.png)

## 12. ConcurrentHashMap线程安全的底层实现

### 12.1 JDK1.7

- 将数据分为一段一段的存储，然后给每一段数据配一把锁，当线程占用锁访问其中一个段数据时，其他段的数据也能被其他线程访问
- **`ConcurrentHashMap` 是由 `Segment` 数组结构和 `HashEntry` 数组结构组成**。
- Segement实现了ReentrantLock，所以Segment是一种可重入锁，HahEntry用于存储键值对数据
- 一个 `ConcurrentHashMap` 里包含一个 `Segment` 数组。`Segment` 的结构和 `HashMap` 类似，是一种数组和链表结构，一个 `Segment` 包含一个 `HashEntry` 数组，每个 `HashEntry` 是一个链表结构的元素，每个 `Segment` 守护着一个 `HashEntry` 数组里的元素，当对 `HashEntry` 数组的数据进行修改时，必须首先获得对应的 `Segment` 的锁

### 12.2 JDK1.8

- `ConcurrentHashMap` 取消了 `Segment` 分段锁，采用 CAS 和 `synchronized` 来保证并发安全。数据结构跟 HashMap1.8 的结构类似，数组+链表/红黑二叉树。Java 8 在链表长度超过一定阈值（8）时将链表（寻址时间复杂度为 O(N)）转换为红黑树（寻址时间复杂度为 O(log(N))）
- **`synchronized` 只锁定当前链表或红黑二叉树的首节点**，这样只要 hash 不冲突，就不会产生并发，效率又提升 N 倍。

## 13. HashSet、LinkedHashSet、TreeSet

- HashSet：是Set接口的主要实现类，HashSet的底层是HashMap，线程不安全，可以存储null值
- LinkedHashSet：是HashSet的子类，能够按照添加顺序遍历元素
- TreeSet：底层使用红黑树，能够按照添加元素的顺序继续遍历，排序方式可以自然排序和定制排序

## 14. Collection框架底层数据结构

### 14.1 List

- ArrayList：Object[]数组
- Vector：Object[]数组
- LinkedList：双向链表（jdk1.6之前是循环双向链表，之后是双向链表）

### 14.2 Set

- HashSet(无序，唯一)：基于HashMap实现，底层采用HashMap保存元素
- LinkedHashSet：是HashSet的子类，内部是通过LinkedHashMap来实现的，
- TreeSet(有序，唯一)：红黑树

### 14.3 Map

- HashMap：
  - JDK1.8 之前 `HashMap` 由数组+链表组成的，数组是 `HashMap` 的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）
  - JDK1.8 以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）时，将链表转化为红黑树，以减少搜索时间
- LinkedHashMap：
  - `LinkedHashMap` 继承自 `HashMap`，所以它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。
  - `LinkedHashMap` 在上面结构的基础上，增加了一条双向链表，使得上面的结构可以保持键值对的插入顺序。同时通过对链表进行相应的操作，实现了访问顺序相关逻辑。可以看：https://www.imooc.com/article/22931
- Hashtable：
  - 数组+链表组成的，数组是 `HashMap` 的主体，链表则是主要为了解决哈希冲突而存在的
- TreeMap：红黑树

## 15. 如何选用集合

- 主要根据集合的特点选择：
- 如果需要根据键值来获取元素值：Map接口下的集合
  - 如果需要排序：TreeMap
  - 如果不需要排序：HashMap
  - 如果要保证线程安全：ConcurrentHashMap
- 如果只需要存放元素：Collection接口下的集合
  - 如果要保证元素唯一：Set接口下的集合：TreeSet、HashSet
  - 如果不需要保证元素唯一：List接口下的集合：ArrayList、LinkedList

