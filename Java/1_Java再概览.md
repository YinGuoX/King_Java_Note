



# Java再概览

- 主要参考自：http://www.cyc2018.xyz

# 1. Java基础

## 1.1 数据类型

- **基本数据类型：**

  - byte/8
  - char/16
  - short/16
  - int/32
  - float/32
  - long/64
  - double/64
  - boolean/~
  - boolean 只有两个值：true、false，可以使用 1 bit 来存储，但是具体大小没有明确规定。JVM 会在编译时期将 boolean  类型的数据转换为 int，使用 1 来表示 true，0 表示 false。JVM 支持 boolean 数组，但是是通过读写 byte  数组来实现的。

- **包装类型：**

  - 基本类型都有对应的包装类型，基本类型与其对应的包装类型之间的赋值使用自动装箱与拆箱完成

  - ```java
    Integer x = 2;     // 装箱 调用了 Integer.valueOf(2)
    int y = x;         // 拆箱 调用了 X.intValue()
    ```

- **缓存池：**

  - new Integer(123) 与 Integer.valueOf(123) 的区别在于：
    - new Integer(123) 每次都会新建一个对象；
    - Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。
  - 在 Java 8 中，Integer 缓存池的大小默认为 -128~127。
  - 编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。
  - 基本类型对应的缓冲池如下：
    - boolean values true and false
    - all byte values
    - short values between -128 and 127
    - int values between -128 and 127
    - char in the range \u0000 to \u007F
  - 在使用这些基本类型对应的包装类型时，如果该数值范围在缓冲池范围内，就可以直接使用缓冲池中的对象。
  - 在 jdk 1.8  所有的数值类缓冲池中，Integer 的缓冲池 IntegerCache 很特殊，这个缓冲池的下界是 - 128，上界默认是  127，但是这个上界是可调的，在启动 jvm 的时候，通过 -XX:AutoBoxCacheMax=<size>  来指定这个缓冲池的大小，该选项在 JVM 初始化的时候会设定一个名为 java.lang.IntegerCache.high 系统属性，然后  IntegerCache 初始化的时候就会读取该系统属性来决定上界。

## 1.2 String

- String 被声明为 final，因此它不可被继承。(Integer 等包装类也不能被继承）

  - 在 Java 8 中，String 内部使用 char 数组存储数据。
  - 在 Java 9 之后，String 类的实现改用 byte 数组存储字符串，同时使用 `coder` 来标识使用了哪种编码。
  - value 数组被声明为 final，这意味着 value 数组初始化之后就不能再引用其它数组。并且 String 内部没有改变 value 数组的方法，因此可以保证 String 不可变。

- String为什么要不可变？

  - **可以缓存hash值：**因为 String 的 hash 值经常被使用，例如 String 用做 HashMap 的 key。不可变的特性可以使得 hash 值也不可变，因此只需要进行一次计算。
  - **string pool的需要：**如果一个 String 对象已经被创建过了，那么就会从 String Pool 中取得引用。只有 String 是不可变的，才可能使用 String Pool。
  - **安全性：**String 经常作为参数，String 不可变性可以保证参数不可变。例如在作为网络连接参数的情况下如果 String 是可变的，那么在网络连接过程中，String 被改变，改变 String 的那一方以为现在连接的是其它主机，而实际情况却不一定是。
  - **线程安全**

- String？StringBuffer？StringBuilder？

  - 可变性：
    - String不可变
    - StringBuffer、StringBuilder可变
  - 线程安全：
    - String不可变，因此是线程安全的
    - StringBuilder不是线程安全的
    - StringBuffer是线程安全的，内部使用了synchronized进行同步

- String pool？

  - 字符串常量池（String Pool）保存着所有字符串字面量（literal strings），这些字面量在编译时期就确定。不仅如此，还可以使用 String 的 intern() 方法在运行过程将字符串添加到 String Pool 中。

  - 当一个字符串调用 intern() 方法时，如果 String Pool 中已经存在一个字符串和该字符串值相等（使用 equals()  方法进行确定），那么就会返回 String Pool 中字符串的引用；否则，就会在 String Pool  中添加一个新的字符串，并返回这个新字符串的引用。

  - 下面示例中，s1 和 s2 采用 new String()  的方式新建了两个不同字符串，而 s3 和 s4 是通过 s1.intern() 和 s2.intern()  方法取得同一个字符串引用。intern() 首先把 "aaa" 放到 String Pool 中，然后返回这个字符串引用，因此 s3 和 s4  引用的是同一个字符串。

  - ```java
    String s1 = new String("aaa");
    String s2 = new String("aaa");
    System.out.println(s1 == s2);           // false
    String s3 = s1.intern();
    String s4 = s2.intern();
    System.out.println(s3 == s4);           // true
    ```

  - 在 Java 7 之前，String Pool 被放在运行时常量池中，它属于永久代。而在 Java 7，String Pool 被移到堆中。这是因为永久代的空间有限，在大量使用字符串的场景下会导致 OutOfMemoryError 错误。

- new String("abc")的具体过程？

  - 使用这种方式一共会创建两个字符串对象（前提是 String Pool 中还没有 "abc" 字符串对象）。

    - "abc" 属于字符串字面量，因此编译时期会在 String Pool 中创建一个字符串对象，指向这个 "abc" 字符串字面量；
    - 而使用 new 的方式会在堆中创建一个字符串对象。

  - ```java
    public class NewStringTest {
        public static void main(String[] args) {
            String s = new String("abc");
        }
    }
    
    // 使用 javap -verbose 进行反编译，得到以下内容：
    // ...
    Constant pool:
    // ...
       #2 = Class              #18            // java/lang/String
       #3 = String             #19            // abc
    // ...
      #18 = Utf8               java/lang/String
      #19 = Utf8               abc
    // ...
    
      public static void main(java.lang.String[]);
        descriptor: ([Ljava/lang/String;)V
        flags: ACC_PUBLIC, ACC_STATIC
        Code:
          stack=3, locals=2, args_size=1
             0: new           #2                  // class java/lang/String
             3: dup
             4: ldc           #3                  // String abc
             6: invokespecial #4                  // Method java/lang/String."<init>":(Ljava/lang/String;)V
             9: astore_1
    // ...
    
    ```

  - 在 Constant Pool 中，#19 存储这字符串字面量 "abc"，#3 是 String Pool 的字符串对象，它指向 #19 这个字符串字面量。在 main 方法中，0: 行使用 new #2 在堆中创建一个字符串对象，并且使用 ldc #3 将 String  Pool 中的字符串对象作为 String 构造函数的参数。

  - 以下是 String 构造函数的源码，可以看到，在将一个字符串对象作为另一个字符串对象的构造函数参数时，并不会完全复制 value 数组内容，而是都会指向同一个 value 数组。

  - ```java
    public String(String original) {
        this.value = original.value;
        this.hash = original.hash;
    }
    ```

## 1.3 运算

- 参数传递：

  - Java 的参数是以值传递的形式传入方法中，而不是引用传递。
  - 实例：建议自行百度

- float和double：

  - Java 不能隐式执行向下转型，因为这会使得精度降低。

- 隐式类型转换：

  - 因为字面量 1 是 int 类型，它比 short 类型精度要高，因此不能隐式地将 int 类型向下转型为 short 类型。

  - 但是使用 += 或者 ++ 运算符会执行隐式类型转换。

  - ```java
    s1 += 1;
    s1++;
    // 上面的语句相当于将 s1 + 1 的计算结果进行了向下转型：
    s1 = (short) (s1 + 1);
    ```

- switch：
  - 从 Java 7 开始，可以在 switch 条件判断语句中使用 String 对象。
  - switch 不支持 long、float、double，是因为 switch 的设计初衷是对那些只有少数几个值的类型进行等值判断，如果值过于复杂，那么还是用 if 比较合适。

## 1.4 关键字

- final：

  - 修饰变量：声明数据为常量，可以是编译时常量，也可以是在运行时被初始化后不能被改变的常量。
    - 对于基本类型，final 使数值不变；
    - 对于引用类型，final 使引用不变，也就不能引用其它对象，但是被引用的对象本身是可以修改的。
  - 修饰方法：声明方法不能被子类重写。
    - private 方法隐式地被指定为 final，如果在子类中定义的方法和基类中的一个 private 方法签名相同，此时子类的方法不是重写基类方法，而是在子类中定义了一个新的方法。
  - 修饰类：声明类不允许被继承。

- static：

  - 修饰变量：

    - 静态变量：又称为类变量，也就是说这个变量属于类的，类所有的实例都共享静态变量，可以直接通过类名来访问它。静态变量在内存中只存在一份。

    - 实例变量：每创建一个实例就会产生一个实例变量，它与该实例同生共死。

    - ```java
      public class A {
      
          private int x;         // 实例变量
          private static int y;  // 静态变量
      
          public static void main(String[] args) {
              // int x = A.x;  // Non-static field 'x' cannot be referenced from a static context
              A a = new A();
              int x = a.x;
              int y = A.y;
          }
      }
      ```

  - 修饰方法：

    - 静态方法在类加载的时候就存在了，它不依赖于任何实例。所以静态方法必须有实现，也就是说它不能是抽象方法。
    - 只能访问所属类的静态字段和静态方法，方法中不能有 this 和 super 关键字，因为这两个关键字与具体对象关联。

  - 修饰语句块：

    - 静态语句块在类初始化时运行一次。

  - 修饰内部类：

    - 非静态内部类依赖于外部类的实例，也就是说需要先创建外部类实例，才能用这个实例去创建非静态内部类。而静态内部类不需要。

    - 静态内部类不能访问外部类的非静态的变量和方法。

    - ```java
      public class OuterClass {
      
          class InnerClass {
          }
      
          static class StaticInnerClass {
          }
      
          public static void main(String[] args) {
              // InnerClass innerClass = new InnerClass(); // 'OuterClass.this' cannot be referenced from a static context
              OuterClass outerClass = new OuterClass();
              InnerClass innerClass = outerClass.new InnerClass();
              StaticInnerClass staticInnerClass = new StaticInnerClass();
          }
      }
      ```

  - 静态导包

    - 在使用静态变量和方法时不用再指明 ClassName，从而简化代码，但可读性大大降低。

  - 初始化顺序：

    - 静态变量和静态语句块优先于实例变量和普通语句块，静态变量和静态语句块的初始化顺序取决于它们在代码中的顺序。
    - 存在继承的情况下，初始化顺序为：
      - 父类（静态变量、静态语句块）
      - 子类（静态变量、静态语句块）
      - 父类（实例变量、普通语句块）
      - 父类（构造函数）
      - 子类（实例变量、普通语句块）
      - 子类（构造函数）

## 1.5 Object类方法

- ```java
  public native int hashCode()
  
  public boolean equals(Object obj)
  
  protected native Object clone() throws CloneNotSupportedException
  
  public String toString()
  
  public final native Class<?> getClass()
  
  protected void finalize() throws Throwable {}
  
  public final native void notify()
  
  public final native void notifyAll()
  
  public final native void wait(long timeout) throws InterruptedException
  
  public final void wait(long timeout, int nanos) throws InterruptedException
  
  public final void wait() throws InterruptedException
  ```

- equals()：

  - 等价关系：

    - 需要满足：自反性、对称性、传递性、一致性、与null比较

    - ```java
      // 自反
      x.equals(x); // true
      // 对称
      x.equals(y) == y.equals(x); // true
      // 传递
      if (x.equals(y) && y.equals(z))
          x.equals(z); // true;
      // 一致
      x.equals(y) == x.equals(y); // true
      // 与null比较
      x.equals(null); // false;
      ```

  - 等价与相等(equals与==)

    - 对于基本类型，== 判断两个值是否相等，基本类型没有 equals() 方法。
    - 对于引用类型，
      - == 判断两个变量是否引用同一个对象，地址值相等
      - 而 equals() 判断引用的对象是否等价，内容相等

  - 具体代码实现：

    - 检查是否为同一个对象的引用

    - 检查是否是同一类型

    - 转型判断每个关键域是否相等

    - ```java
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
      
              if (x != that.x) return false;
              if (y != that.y) return false;
              return z == that.z;
          }
      }
      ```

- hashCode()：

  - hashCode() 返回哈希值，而 equals() 是用来判断两个对象是否等价。等价的两个对象散列值一定相同，但是散列值相同的两个对象不一定等价，这是因为计算哈希值具有随机性，两个值不同的对象可能计算出相同的哈希值。

  - 在覆盖 equals() 方法时应当总是覆盖 hashCode() 方法，保证等价的两个对象哈希值也相等。

  - HashSet  和 HashMap 等集合类使用了 hashCode()  方法来计算对象应该存储的位置，因此要将对象添加到这些集合类中，需要让对应的类实现 hashCode()  方法。

  - 理想的哈希函数应当具有均匀性，即不相等的对象应当均匀分布到所有可能的哈希值上。这就要求了哈希函数要把所有域的值都考虑进来。可以将每个域都当成 R 进制的某一位，然后组成一个 R 进制的整数。

    - R 一般取 31，因为它是一个奇素数，如果是偶数的话，当出现乘法溢出，信息就会丢失，因为与 2 相乘相当于向左移一位，最左边的位丢失。并且一个数与 31 相乘可以转换成移位和减法：`31*x == (x<<5)-x`，编译器会自动进行这个优化。

    - ```java
      @Override
      public int hashCode() {
          int result = 17;
          result = 31 * result + x;
          result = 31 * result + y;
          result = 31 * result + z;
          return result;
      }
      ```

- toString()：

  - 默认返回 ToStringExample@4554617c 这种形式，其中 @ 后面的数值为散列码的无符号十六进制表示。

- clone()：

  - clone() 是 Object 的 protected 方法，它不是 public，一个类不显式去重写 clone()，其它类就不能直接去调用该类实例的 clone() 方法。
  - clone() 方法并不是 Cloneable 接口的方法，而是 Object 的一个 protected 方法。Cloneable  接口只是规定，**如果一个类没有实现 Cloneable 接口又调用了 clone() 方法，就会抛出  CloneNotSupportedException**
  - 浅拷贝：拷贝对象和原始对象的引用类型引用同一个对象。直接在clone方法中复制
  - 深拷贝：拷贝对象和原始对象的引用类型引用不同对象。在clone方法中需要重新new，再赋值
  - clone()代替方案：
    - 使用 clone() 方法来拷贝一个对象即复杂又有风险，它会抛出异常，并且还需要类型转换。Effective Java 书上讲到，最好不要去使用 clone()，可以使用拷贝构造函数或者拷贝工厂来拷贝一个对象。

## 1.6 继承

- **访问权限：**

  - Java 中有三个访问权限修饰符：private、protected 以及 public，如果不加访问修饰符，表示包级可见。
  - 可以对类或类中的成员（字段和方法）加上访问修饰符。
    - 类可见表示其它类可以用这个类创建实例对象。
    - 成员可见表示其它类可以用这个类的实例对象访问到该成员；
  - protected 用于修饰成员，表示在继承体系中成员对于子类可见，但是这个访问修饰符对于类没有意义。
  - 设计良好的模块会隐藏所有的实现细节，把它的 API 与它的实现清晰地隔离开来。模块之间只通过它们的 API 进行通信，一个模块不需要知道其他模块的内部工作情况，这个概念被称为信息隐藏或封装。因此访问权限应当尽可能地使每个类或者成员不被外界访问。
  - 如果子类的方法重写了父类的方法，那么子类中该方法的访问级别不允许低于父类的访问级别。这是为了确保可以使用父类实例的地方都可以使用子类实例去代替，也就是确保满足里氏替换原则。
  - 字段决不能是公有的，因为这么做的话就失去了对这个字段修改行为的控制，客户端可以对其随意修改。例如下面的例子中，AccessExample 拥有 id 公有字段，如果在某个时刻，我们想要使用 int 存储 id 字段，那么就需要修改所有的客户端代码。
    - 可以使用公有的 getter 和 setter 方法来替换公有字段，这样的话就可以控制对字段的修改行为。
    - 但是也有例外，如果是包级私有的类或者私有的嵌套类，那么直接暴露成员不会有特别大的影响。

- **抽象类与接口？**

  - 抽象类：抽象类和抽象方法都使用 abstract 关键字进行声明。如果一个类中包含抽象方法，那么这个类必须声明为抽象类。
    - 抽象类和普通类最大的区别是，抽象类不能被实例化，只能被继承。
  - 接口：接口是抽象类的延伸，在 Java 8 之前，它可以看成是一个完全抽象的类，也就是说它不能有任何的方法实现。
    - 从 Java 8 开始，接口也可以拥有默认的方法实现，**这是因为不支持默认方法的接口的维护成本太高了。**在 Java 8 之前，如果一个接口想要添加新的方法，那么要修改所有实现了该接口的类，让它们都实现新增的方法。
    - 接口的成员（字段 + 方法）默认都是 public 的，并且不允许定义为 private 或者 protected。从 Java 9 开始，允许将方法定义为 private，这样就能定义某些复用的代码又不会把方法暴露出去。
      - 接口的字段默认都是 static 和 final 的。
  - 比较：
    - 从设计层面上看，抽象类提供了一种 IS-A 关系，需要满足里式替换原则，即子类对象必须能够替换掉所有父类对象。而接口更像是一种 LIKE-A 关系，它只是提供一种方法实现契约，并不要求接口和实现接口的类具有 IS-A 关系。
    - 从使用上来看，一个类可以实现多个接口，但是不能继承多个抽象类。
    - 接口的字段只能是 static 和 final 类型的，而抽象类的字段没有这种限制。
    - 接口的成员只能是 public 的，而抽象类的成员可以有多种访问权限。
  - 使用选择：
    - 使用接口：
      - 需要让不相关的类都实现一个方法，例如不相关的类都可以实现 Comparable 接口中的 compareTo() 方法；
      - 需要使用多重继承。
    - 使用抽象类：
      - 需要在几个相关的类中共享代码。
      - 需要能控制继承来的成员的访问权限，而不是都为 public。
      - 需要继承非静态和非常量字段。
    - 在很多情况下，接口优先于抽象类。因为接口没有抽象类严格的类层次结构要求，可以灵活地为一个类添加行为。并且从 Java 8 开始，接口也可以有默认的方法实现，使得修改接口的成本也变的很低。

- **supper：**

  - 访问父类的构造函数：可以使用 super()  函数访问父类的构造函数，从而委托父类完成一些初始化的工作。应该注意到，子类一定会调用父类的构造函数来完成初始化工作，一般是调用父类的默认构造函数，如果子类需要调用父类其它构造函数，那么就可以使用 super() 函数。
  - 访问父类的成员：如果子类重写了父类的某个方法，可以通过使用 super 关键字来引用父类的方法实现。

- **重载和重写？**

  - **重写(Override)：**存在于继承体系中，指子类实现了一个与父类在方法声明上完全相同的一个方法。

    - 为了满足里式替换原则，重写有以下三个限制：
      - 子类方法的**访问权限**必须大于等于父类方法；
      - 子类方法的**返回类型**必须是父类方法返回类型或为其子类型。
      - 子类方法**抛出的异常类型**必须是父类抛出异常类型或为其子类型。
    - 使用 @Override 注解，可以让编译器帮忙检查是否满足上面的三个限制条件。
    - 在调用一个方法时，先从本类中查找看是否有对应的方法，如果没有再到父类中查看，看是否从父类继承来。否则就要对参数进行转型，转成父类之后看是否有对应的方法。总的来说，方法调用的优先级为：
      - this.func(this)
      - super.func(this)
      - this.func(super)
      - super.func(super)

  - ```java
    /*
        A	
        |
        B
        |
        C
        |
        D
     */
    
    
    class A {
    
        public void show(A obj) {
            System.out.println("A.show(A)");
        }
    
        public void show(C obj) {
            System.out.println("A.show(C)");
        }
    }
    
    class B extends A {
    
        @Override
        public void show(A obj) {
            System.out.println("B.show(A)");
        }
    }
    
    class C extends B {
    }
    
    class D extends C {
    }
    
    public static void main(String[] args) {
    
        A a = new A();
        B b = new B();
        C c = new C();
        D d = new D();
    
        // 在 A 中存在 show(A obj)，直接调用
        a.show(a); // A.show(A)
        // 在 A 中不存在 show(B obj)，将 B 转型成其父类 A
        a.show(b); // A.show(A)
        // 在 B 中存在从 A 继承来的 show(C obj)，直接调用
        b.show(c); // A.show(C)
        // 在 B 中不存在 show(D obj)，但是存在从 A 继承来的 show(C obj)，将 D 转型成其父类 C
        b.show(d); // A.show(C)
    
        // 引用的还是 B 对象，所以 ba 和 b 的调用结果一样
        A ba = new B();
        ba.show(c); // A.show(C)
        ba.show(d); // A.show(C)
    }
    ```

  - **重载(Overload)：**存在于同一个类中，指一个方法与已经存在的方法名称上相同，但是参数类型、个数、顺序至少有一个不同。

    - 应该注意的是，返回值不同，其它都相同不算是重载。

## 1.7 反射

- 每个类都有一个   **Class**   对象，包含了与类有关的信息。当编译一个新类时，会产生一个同名的 .class 文件，该文件内容保存着 Class 对象。
- 类加载相当于 Class 对象的加载，类在第一次使用时才动态加载到 JVM 中。也可以使用 `Class.forName("com.mysql.jdbc.Driver")` 这种方式来控制类的加载，该方法会返回一个 Class 对象。
- 反射可以提供运行时的类信息，并且这个类可以在运行时才加载进来，甚至在编译时期该类的 .class 不存在也可以加载进来。
- Class 和 java.lang.reflect 一起对反射提供了支持，java.lang.reflect 类库主要包含了以下三个类：
  - **Field**  ：可以使用 get() 和 set() 方法读取和修改 Field 对象关联的字段；
  - **Method**  ：可以使用 invoke() 方法调用与 Method 对象关联的方法；
  - **Constructor**  ：可以用 Constructor 的 newInstance() 创建新的对象。
- **反射的优点：**
  - **可扩展性**   ：应用程序可以利用全限定名创建可扩展对象的实例，来使用来自外部的用户自定义类。
  - **类浏览器和可视化开发环境**   ：一个类浏览器需要可以枚举类的成员。可视化开发环境（如 IDE）可以从利用反射中可用的类型信息中受益，以帮助程序员编写正确的代码。
  - **调试器和测试工具**   ： 调试器需要能够检查一个类里的私有成员。测试工具可以利用反射来自动地调用类里定义的可被发现的 API 定义，以确保一组测试中有较高的代码覆盖率。
- **反射的缺点：**
  - 尽管反射非常强大，但也不能滥用。如果一个功能可以不用反射完成，那么最好就不用。在我们使用反射技术时，下面几条内容应该牢记于心。
  - **性能开销**   ：反射涉及了动态类型的解析，所以 JVM 无法对这些代码进行优化。因此，反射操作的效率要比那些非反射操作低得多。我们应该避免在经常被执行的代码或对性能要求很高的程序中使用反射。
  - **安全限制**   ：使用反射技术要求程序必须在一个没有安全限制的环境中运行。如果一个程序必须在有安全限制的环境中运行，如 Applet，那么这就是个问题了。
  - **内部暴露**   ：由于反射允许代码执行一些在正常情况下不被允许的操作（比如访问私有的属性和方法），所以使用反射可能会导致意料之外的副作用，这可能导致代码功能失调并破坏可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。

## 1.8 异常

- Throwable 可以用来表示任何可以作为异常抛出的类，分为两种：  **Error**   和 **Exception**。其中 Error 用来表示 JVM 无法处理的错误，Exception 分为两种：
  - **受检异常**  ：需要用 try...catch... 语句捕获并进行处理，并且可以从异常中恢复；
  - **非受检异常**  ：是程序运行时错误，例如除 0 会引发 Arithmetic Exception，此时程序崩溃并且无法恢复。
- ![img](1_Java再概览.assets/PPjwP.png)

## 1.9 泛型

- 建议参考：

  - [Java泛型详解](https://www.cnblogs.com/Blue-Keroro/p/8875898.html)
  - [10道java泛型面试题](https://cloud.tencent.com/developer/article/1033693)

- 什么是泛型？

  - **泛型，即“参数化类型”。**
  - 什么是参数化类型？
    - 顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。
  - **泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）**。也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。

- 为什么使用泛型？

  - 举例

  - ```java
    List arrayList = new ArrayList();
    arrayList.add("aaaa");
    arrayList.add(100);
    
    for(int i = 0; i< arrayList.size();i++){
        String item = (String)arrayList.get(i);
        Log.d("泛型测试","item = " + item);
    }
    
    //毫无疑问，程序的运行结果会以崩溃结束：
    java.lang.ClassCastException: java.lang.Integer cannot be cast to java.lang.String
        
    // ArrayList可以存放任意类型，
    // 例子中添加了一个String类型，添加了一个Integer类型，再使用时都以String的方式使用，因此程序崩溃了。
    // 为了解决类似这样的问题（在编译阶段就可以解决），泛型应运而生。
    
    // 我们将第一行声明初始化list的代码更改一下，编译器会在编译阶段就能够帮我们发现类似这样的问题。
    List<String> arrayList = new ArrayList<String>();
    ...
    //arrayList.add(100); 在编译阶段，编译器就会报错
    ```

- 泛型的特性？

  - **泛型类型在逻辑上看以看成是多个不同的类型，实际上都是相同的基本类型。**

  - 在编译之后程序会采取去泛型化的措施。也就是说Java中的泛型，只在编译阶段有效。在编译过程中，正确检验泛型结果后，会将泛型的相关信息擦出，并且在对象进入和离开方法的边界处添加类型检查和类型转换的方法。也就是说，泛型信息不会进入到运行时阶段。

  - ```java
    List<String> stringArrayList = new ArrayList<String>();
    List<Integer> integerArrayList = new ArrayList<Integer>();
    
    Class classStringArrayList = stringArrayList.getClass();
    Class classIntegerArrayList = integerArrayList.getClass();
    
    if(classStringArrayList.equals(classIntegerArrayList)){
        Log.d("泛型测试","类型相同");
    }
    // 输出结果：D/泛型测试: 类型相同。
    ```

- 泛型的使用？

  - 泛型有三种使用方式，分别为：泛型类、泛型接口、泛型方法

  - ```java
    //此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
    //在实例化泛型类时，必须指定T的具体类型
    public class Generic<T>{ 
        //key这个成员变量的类型为T,T的类型由外部指定  
        private T key;
    
        public Generic(T key) { //泛型构造方法形参key的类型也为T，T的类型由外部指定
            this.key = key;
        }
    
        public T getKey(){ //泛型方法getKey的返回值类型为T，T的类型由外部指定
            return key;
        }
    }
    
    //泛型的类型参数只能是类类型（包括自定义类），不能是简单类型
    //传入的实参类型需与泛型的类型参数类型相同，即为Integer.
    Generic<Integer> genericInteger = new Generic<Integer>(123456);
    
    //传入的实参类型需与泛型的类型参数类型相同，即为String.
    Generic<String> genericString = new Generic<String>("key_vlaue");
    Log.d("泛型测试","key is " + genericInteger.getKey());
    Log.d("泛型测试","key is " + genericString.getKey());
    
    // 定义的泛型类，就一定要传入泛型类型实参么？并不是这样，在使用泛型的时候如果传入泛型实参，则会根据传入的泛型实参做相应的限制，此时泛型才会起到本应起到的限制作用。如果不传入泛型类型实参的话，在泛型类中使用泛型的方法或成员变量定义的类型可以为任何的类型。
    
    Generic generic = new Generic("111111");
    Generic generic1 = new Generic(4444);
    Generic generic2 = new Generic(55.55);
    Generic generic3 = new Generic(false);
    
    Log.d("泛型测试","key is " + generic.getKey());
    Log.d("泛型测试","key is " + generic1.getKey());
    Log.d("泛型测试","key is " + generic2.getKey());
    Log.d("泛型测试","key is " + generic3.getKey());
    
    // 泛型的类型参数只能是类类型，不能是简单类型。
    // 不能对确切的泛型类型使用instanceof操作。如下面的操作是非法的，编译时会出错。
    ```

- 泛型总结？

  - 如果使用泛型方法将整个类泛型化，那么就应该使用泛型方法。另外对于一个static的方法而已，无法访问泛型类型的参数。所以如果static方法要使用泛型能力，就必须使其成为泛型方法。

## 1.10 注解

- Java 注解是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性的作用。

## 1.11 新特性

- **New highlights in Java SE 8**
  1. Lambda Expressions
  2. Pipelines and Streams
  3. Date and Time API
  4. Default Methods
  5. Type Annotations
  6. Nashhorn JavaScript Engine
  7. Concurrent Accumulators
  8. Parallel operations
  9. PermGen Error Removed
- **New highlights in Java SE 7**
  - Strings in Switch Statement
  - Type Inference for Generic Instance Creation
  - Multiple Exception Handling
  - Support for Dynamic Languages
  - Try with Resources
  - Java nio Package
  - Binary Literals, Underscore in literals
  - Diamond Syntax
- **Java 与 C++ 的区别**
  - Java 是纯粹的面向对象语言，所有的对象都继承自 java.lang.Object，C++ 为了兼容 C 即支持面向对象也支持面向过程。
  - Java 通过虚拟机从而实现跨平台特性，但是 C++ 依赖于特定的平台。
  - Java 没有指针，它的引用可以理解为安全指针，而 C++ 具有和 C 一样的指针。
  - Java 支持自动垃圾回收，而 C++ 需要手动回收。
  - Java 不支持多重继承，只能通过实现多个接口来达到相同目的，而 C++ 支持多重继承。
  - Java 不支持操作符重载，虽然可以对两个 String 对象执行加法运算，但是这是语言内置支持的操作，不属于操作符重载，而 C++ 可以。
  - Java 的 goto 是保留字，但是不可用，C++ 可以使用 goto。
- **JRE or JDK**
  - JRE：Java Runtime Environment，Java 运行环境的简称，为 Java 的运行提供了所需的环境。它是一个 JVM 程序，主要包括了 JVM 的标准实现和一些 Java 基本类库。
  - JDK：Java Development Kit，Java 开发工具包，提供了 Java 的开发及运行环境。JDK 是 Java 开发的核心，集成了 JRE 以及一些其它的工具，比如编译 Java 源码的编译器 javac 等。

# 2. Java容器

- Java如何操作数据！

## 2.1 概览

- 容器主要包括 Collection 和 Map 两种，
  - Collection 存储着对象的集合，
  - 而 Map 存储着键值对（两个对象）的映射表。
- **Collection：**
  - ![img](1_Java再概览.assets/image-20191208220948084.png)
  - Set
    - TreeSet：基于红黑树实现，支持有序性操作，例如根据一个范围查找元素的操作。但是查找效率不如 HashSet，HashSet 查找的时间复杂度为 O(1)，TreeSet 则为 O(logN)。
    - HashSet：基于哈希表实现，支持快速查找，但不支持有序性操作。并且失去了元素的插入顺序信息，也就是说使用 Iterator 遍历 HashSet 得到的结果是不确定的。
    - LinkedHashSet：具有 HashSet 的查找效率，并且内部使用双向链表维护元素的插入顺序。
  - List
    - ArrayList：基于动态数组实现，支持随机访问。
    - Vector：和 ArrayList 类似，但它是线程安全的。
    - LinkedList：基于双向链表实现，只能顺序访问，但是可以快速地在链表中间插入和删除元素。不仅如此，LinkedList 还可以用作栈、队列和双向队列。
  - Queue
    - LinkedList：可以用它来实现双向队列。
    - PriorityQueue：基于堆结构实现，可以用它来实现优先队列
- **Map：**
  - ![img](1_Java再概览.assets/image-20201101234335837.png)
  - TreeMap：基于红黑树实现。
  - HashMap：基于哈希表实现。
  - HashTable：和 HashMap 类似，但它是线程安全的，这意味着同一时刻多个线程同时写入 HashTable  不会导致数据不一致。它是遗留类，不应该去使用它，而是使用 ConcurrentHashMap 来支持线程安全，ConcurrentHashMap 的效率会更高，因为 ConcurrentHashMap 引入了分段锁。
  - LinkedHashMap：使用双向链表来维护元素的顺序，顺序为插入顺序或者最近最少使用（LRU）顺序。

## 2.2 容器中的设计模式

- **迭代器模式：**

  - 提供一种顺序访问聚合对象元素的方法，并且不暴露聚合对象的内部表示。
  - ![img](1_Java再概览.assets/image-20191208225301973.png)
  - Collection 继承了 Iterable 接口，其中的 iterator() 方法能够产生一个 Iterator 对象，通过这个对象就可以迭代遍历 Collection 中的元素。
  - 从 JDK 1.5 之后可以使用 foreach 方法来遍历实现了 Iterable 接口的聚合对象。

- **适配器模式：**

  - java.util.Arrays#asList() 可以把数组类型转换为 List 类型。

  - ```java
    @SafeVarargs
    public static <T> List<T> asList(T... a)
        
    // 应该注意的是 asList() 的参数为泛型的变长参数，不能使用基本类型数组作为参数，只能使用相应的包装类型数组。
    
    Integer[] arr = {1, 2, 3};
    List list = Arrays.asList(arr);
    
    List list = Arrays.asList(1, 2, 3);
    
    ```

## 2.3 源码分析

- 如果没有特别说明，以下源码分析基于 JDK 1.8。
- 在 IDEA 中 double shift 调出 Search EveryWhere，查找源码文件，找到之后就可以阅读源码。
- **主要了解：容器的增删改查即可！**

### 2.3.1 ArrayList

1. **概览**

   - 因为 ArrayList 是基于数组实现的，所以支持快速随机访问。RandomAccess 接口标识着该类支持快速随机访问。

   - ```java
     public class ArrayList<E> extends AbstractList<E>
             implements List<E>, RandomAccess, Cloneable, java.io.Serializable
     ```

   - 数组的默认大小为 10。

   - ```java
     private static final int DEFAULT_CAPACITY = 10;
     ```

2. **添加元素：**

   - 添加元素时使用 ensureCapacityInternal() 方法来保证容量足够，如果不够时，需要使用 grow() 方法进行扩容，新容量的大小为 `oldCapacity + (oldCapacity >> 1)`，即 oldCapacity+oldCapacity/2。其中 oldCapacity >> 1 需要取整，所以新容量大约是旧容量的 1.5 倍左右。（oldCapacity 为偶数就是 1.5 倍，为奇数就是 1.5 倍-0.5）

   - 扩容操作需要调用 `Arrays.copyOf()` 把原数组整个复制到新数组中，这个操作代价很高，因此最好在创建 ArrayList 对象时就指定大概的容量大小，减少扩容操作的次数。

   - ```java
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

3. **删除元素：**

   - 需要调用 System.arraycopy() 将 index+1 后面的元素都复制到 index 位置上，该操作的时间复杂度为 O(N)，可以看到 ArrayList 删除元素的代价是非常高的。

   - ```java
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

4. **序列化：**

   - ArrayList 基于数组实现，并且具有动态扩容特性，因此保存元素的数组不一定都会被使用，那么就没必要全部进行序列化。

   - 保存元素的数组 elementData 使用 transient 修饰，该关键字声明数组默认不会被序列化。

   - ```java
     transient Object[] elementData; // non-private to simplify nested class access
     ```

   - ArrayList 实现了 writeObject() 和 readObject() 来控制只序列化数组中有元素填充那部分内容。

     - ```java
       private void readObject(java.io.ObjectInputStream s)
           throws java.io.IOException, ClassNotFoundException {
           elementData = EMPTY_ELEMENTDATA;
       
           // Read in size, and any hidden stuff
           s.defaultReadObject();
       
           // Read in capacity
           s.readInt(); // ignored
       
           if (size > 0) {
               // be like clone(), allocate array based upon size not capacity
               ensureCapacityInternal(size);
       
               Object[] a = elementData;
               // Read in all elements in the proper order.
               for (int i=0; i<size; i++) {
                   a[i] = s.readObject();
               }
           }
       }
       
       private void writeObject(java.io.ObjectOutputStream s)
           throws java.io.IOException{
           // Write out element count, and any hidden stuff
           int expectedModCount = modCount;
           s.defaultWriteObject();
       
           // Write out size as capacity for behavioural compatibility with clone()
           s.writeInt(size);
       
           // Write out all elements in the proper order.
           for (int i=0; i<size; i++) {
               s.writeObject(elementData[i]);
           }
       
           if (modCount != expectedModCount) {
               throw new ConcurrentModificationException();
           }
       }
       
       ```

   - 序列化时需要使用 ObjectOutputStream 的 writeObject() 将对象转换为字节流并输出。而 writeObject() 方法在传入的对象存在 writeObject() 的时候会去反射调用该对象的 writeObject() 来实现序列化。反序列化使用的是  ObjectInputStream 的 readObject() 方法，原理类似。

   - ```java
     ArrayList list = new ArrayList();
     ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(file));
     oos.writeObject(list);
     ```

5. **Fail-Fast**

   - fail-fast 机制是Java集合(Collection)中的一种错误机制。当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。例如：当某一个线程A通过iterator去遍历某集合的过程中，若该集合的内容被其他线程所改变了；那么线程A访问集合时，就会抛出ConcurrentModificationException异常，产生fail-fast事件。

   - modCount 用来记录 ArrayList 结构发生变化的次数。结构发生变化是指添加或者删除至少一个元素的所有操作，或者是调整内部数组的大小，仅仅只是设置元素的值不算结构发生变化。
   - 在进行序列化或者迭代等操作时，需要比较操作前后 modCount 是否改变，如果改变了需要抛出 ConcurrentModificationException。代码参考上节序列化中的 writeObject() 方法。

### 2.3.2 Vector

1. **同步：**

   - 它的实现与 ArrayList 类似，但是使用了 synchronized 进行同步。

   - ```java
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

2. **扩容：**

   - Vector 的构造函数可以传入 capacityIncrement 参数，它的作用是在扩容时使容量 capacity 增长 capacityIncrement。如果这个参数的值小于等于 0，扩容时每次都令 capacity 为原来的两倍。

   - ```java
     public Vector(int initialCapacity, int capacityIncrement) {
         super();
         if (initialCapacity < 0)
             throw new IllegalArgumentException("Illegal Capacity: "+
                                                initialCapacity);
         this.elementData = new Object[initialCapacity];
         this.capacityIncrement = capacityIncrement;
     }
     
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

   - 调用没有 capacityIncrement 的构造函数时，capacityIncrement 值被设置为 0，也就是说默认情况下 Vector 每次扩容时容量都会翻倍。

   - ```java
     public Vector(int initialCapacity) {
         this(initialCapacity, 0);
     }
     
     public Vector() {
         this(10);
     }
     ```

3. **与ArrayList比较：**

   - Vector 是同步的，因此开销就比 ArrayList 要大，访问速度更慢。最好使用 ArrayList 而不是 Vector，因为同步操作完全可以由程序员自己来控制；
   - Vector 每次扩容请求其大小的 2 倍（也可以通过构造函数设置增长的容量），而 ArrayList 是 1.5 倍。

4. **代替方案：**

   - 可以使用 `Collections.synchronizedList();` 得到一个线程安全的 ArrayList。

   - ```java
     List<String> list = new ArrayList<>();
     List<String> synList = Collections.synchronizedList(list);
     ```

   - 也可以使用 concurrent 并发包下的 CopyOnWriteArrayList 类。

   - ```java
     List<String> list = new CopyOnWriteArrayList<>();
     ```

### 2.3.3 CopyOnWriteArrayList

1. **读写分离：**

   - 写操作在一个复制的数组上进行，读操作在原始数组进行，实现读写分离，互不影响

   - 写操作需要加锁，防止并发写入时导致写入数据丢失。

   - 写操作结束之后需要把原始数组指向新的复制数组。

   - ```java
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
     
     @SuppressWarnings("unchecked")
     private E get(Object[] a, int index) {
         return (E) a[index];
     }
     ```

2. **使用场景：**

   - CopyOnWriteArrayList 在写操作的同时允许读操作，大大提高了读操作的性能，因此很适合读多写少的应用场景。

     但是 CopyOnWriteArrayList 有其缺陷：

     - 内存占用：在写操作时需要复制一个新的数组，使得内存占用为原来的两倍左右；
     - 数据不一致：读操作不能读取实时性的数据，因为部分写操作的数据还未同步到读数组中。

   - 所以 CopyOnWriteArrayList 不适合内存敏感以及对实时性要求很高的场景。

### 2.3.4 LinkedList

1. **概览：**

   - 基于双向链表实现，使用 Node 存储链表节点信息。

   - ```java
     private static class Node<E> {
         E item;
         Node<E> next;
         Node<E> prev;
     }
     ```

   - 每个链表存储了 first 和 last 指针：

   - ```java
     transient Node<E> first;
     transient Node<E> last;
     ```

   - ![image-20211221132737452](1_Java再概览.assets/image-20211221132737452.png)

2. **与ArrayList比较：**

   - ArrayList 基于动态数组实现，LinkedList 基于双向链表实现。ArrayList 和 LinkedList 的区别可以归结为数组和链表的区别：
     - 数组支持随机访问，但插入删除的代价很高，需要移动大量元素；
     - 链表不支持随机访问，但插入删除只需要改变指针

### 2.3.5 HashMap

- **为了便于理解，以下源码分析以 JDK 1.7 为主。**

1. **存储结构：**

   - 内部包含了一个 Entry 类型的数组 table。Entry 存储着键值对。它包含了四个字段，从 next 字段我们可以看出 Entry  是一个链表。即数组中的每个位置被当成一个桶，一个桶存放一个链表。HashMap  使用拉链法来解决冲突，同一个链表中存放哈希值和散列桶取模运算结果相同的 Entry。

   - ```java
     transient Entry[] table;
     
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

   - ![img](1_Java再概览.assets/image-20191208234948205.png)

2. **拉链法原理：**

   - ```java
     HashMap<String, String> map = new HashMap<>();
     map.put("K1", "V1");
     map.put("K2", "V2");
     map.put("K3", "V3");
     ```

     - 新建一个 HashMap，默认大小为 16；
     - 插入 <K1,V1> 键值对，先计算 K1 的 hashCode 为 115，使用除留余数法得到所在的桶下标 115%16=3。
     - 插入 <K2,V2> 键值对，先计算 K2 的 hashCode 为 118，使用除留余数法得到所在的桶下标 118%16=6。
     - 插入 <K3,V3> 键值对，先计算 K3 的 hashCode 为 118，使用除留余数法得到所在的桶下标 118%16=6，插在 <K2,V2> 前面。

   - 应该注意到链表的插入是以头插法方式进行的，例如上面的 <K3,V3> 不是插在 <K2,V2> 后面，而是插入在链表头部。

   - 查找需要分成两步进行：

     - 计算键值对所在的桶；
     - 在链表上顺序查找，时间复杂度显然和链表的长度成正比。

   - ![img](1_Java再概览.assets/image-20191208235258643.png)

3. **put操作：**

   - ```java
     public V put(K key, V value) {
         if (table == EMPTY_TABLE) {
             inflateTable(threshold);
         }
         // 键为 null 单独处理
         if (key == null)
             return putForNullKey(value);
         int hash = hash(key);
         // 确定桶下标
         int i = indexFor(hash, table.length);
         // 先找出是否已经存在键为 key 的键值对，如果存在的话就更新这个键值对的值为 value
         for (Entry<K,V> e = table[i]; e != null; e = e.next) {
             Object k;
             if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                 V oldValue = e.value;
                 e.value = value;
                 e.recordAccess(this);
                 return oldValue;
             }
         }
     
         modCount++;
         // 插入新键值对
         addEntry(hash, key, value, i);
         return null;
     }
     ```

   - HashMap 允许插入键为 null 的键值对。但是因为无法调用 null 的 hashCode() 方法，也就无法确定该键值对的桶下标，只能通过强制指定一个桶下标来存放。HashMap 使用第 0 个桶存放键为 null 的键值对。

   - ```java
     private V putForNullKey(V value) {
         for (Entry<K,V> e = table[0]; e != null; e = e.next) {
             if (e.key == null) {
                 V oldValue = e.value;
                 e.value = value;
                 e.recordAccess(this);
                 return oldValue;
             }
         }
         modCount++;
         addEntry(0, null, value, 0);
         return null;
     }
     ```

   - 使用链表的头插法，也就是新的键值对插在链表的头部，而不是链表的尾部。

   - ```java
     void addEntry(int hash, K key, V value, int bucketIndex) {
         if ((size >= threshold) && (null != table[bucketIndex])) {
             resize(2 * table.length);
             hash = (null != key) ? hash(key) : 0;
             bucketIndex = indexFor(hash, table.length);
         }
     
         createEntry(hash, key, value, bucketIndex);
     }
     
     void createEntry(int hash, K key, V value, int bucketIndex) {
         Entry<K,V> e = table[bucketIndex];
         // 头插法，链表头部指向新的键值对
         table[bucketIndex] = new Entry<>(hash, key, value, e);
         size++;
     }
     ```

4. **确定桶下标：**

   - 很多操作都需要先确定一个键值对所在的桶下标。

   - 1、先计算hash值：

     - ```java
       final int hash(Object k) {
           int h = hashSeed;
           if (0 != h && k instanceof String) {
               return sun.misc.Hashing.stringHash32((String) k);
           }
       
           h ^= k.hashCode();
       
           // This function ensures that hashCodes that differ only by
           // constant multiples at each bit position have a bounded
           // number of collisions (approximately 8 at default load factor).
           h ^= (h >>> 20) ^ (h >>> 12);
           return h ^ (h >>> 7) ^ (h >>> 4);
       }
       
       public final int hashCode() {
           return Objects.hashCode(key) ^ Objects.hashCode(value);
       }
       
       ```

   - 2、取模：

     - 我们知道，位运算的代价比求模运算小的多，因此在进行这种计算时用位运算的话能带来更高的性能。

     - 确定桶下标的最后一步是将 key 的 hash 值对桶个数取模：hash%capacity，如果能保证 capacity 为 2 的 n 次方，那么就可以将这个操作转换为位运算。

     - ```java
       static int indexFor(int h, int length) {
           return h & (length-1);
       }
       ```

5. **扩容-基本原理：**

   - 设 HashMap 的 table 长度为 M，需要存储的键值对数量为 N，如果哈希函数满足均匀性的要求，那么每条链表的长度大约为 N/M，因此查找的复杂度为 O(N/M)。

   - 为了让查找的成本降低，应该使 N/M 尽可能小，因此需要保证 M 尽可能大，也就是说 table 要尽可能大。HashMap 采用动态扩容来根据当前的 N 值来调整 M 值，使得空间效率和时间效率都能得到保证。

   - 和扩容相关的参数主要有：capacity、size、threshold 和 load_factor。

   - |    参数    | 含义                                                         |
     | :--------: | :----------------------------------------------------------- |
     |  capacity  | table 的容量大小，默认为 16。需要注意的是 capacity 必须保证为 2 的 n 次方。 |
     |    size    | 键值对数量。                                                 |
     | threshold  | size 的临界值，当 size 大于等于 threshold 就必须进行扩容操作。 |
     | loadFactor | 装载因子，table 能够使用的比例，threshold = (int)(capacity* loadFactor)。 |

   - ```java
     static final int DEFAULT_INITIAL_CAPACITY = 16;
     
     static final int MAXIMUM_CAPACITY = 1 << 30;
     
     static final float DEFAULT_LOAD_FACTOR = 0.75f;
     
     transient Entry[] table;
     
     transient int size;
     
     int threshold;
     
     final float loadFactor;
     
     transient int modCount
     ```

   - 从下面的添加元素代码中可以看出，当需要扩容时，令 capacity 为原来的两倍。

     - ```java
       void addEntry(int hash, K key, V value, int bucketIndex) {
           Entry<K,V> e = table[bucketIndex];
           table[bucketIndex] = new Entry<>(hash, key, value, e);
           if (size++ >= threshold)
               resize(2 * table.length);
       }
       ```

   - 扩容使用 resize() 实现，需要注意的是，扩容操作同样需要把 oldTable 的所有键值对重新插入 newTable 中，因此这一步是很费时的。

     - ```java
       void resize(int newCapacity) {
           Entry[] oldTable = table;
           int oldCapacity = oldTable.length;
           if (oldCapacity == MAXIMUM_CAPACITY) {
               threshold = Integer.MAX_VALUE;
               return;
           }
           Entry[] newTable = new Entry[newCapacity];
           transfer(newTable);
           table = newTable;
           threshold = (int)(newCapacity * loadFactor);
       }
       
       void transfer(Entry[] newTable) {
           Entry[] src = table;
           int newCapacity = newTable.length;
           for (int j = 0; j < src.length; j++) {
               Entry<K,V> e = src[j];
               if (e != null) {
                   src[j] = null;
                   do {
                       Entry<K,V> next = e.next;
                       int i = indexFor(e.hash, newCapacity);
                       e.next = newTable[i];
                       newTable[i] = e;
                       e = next;
                   } while (e != null);
               }
           }
       }
       ```

6. **扩容-重新计算桶下标：**

   - 在进行扩容时，需要把键值对重新计算桶下标，从而放到对应的桶上。在前面提到，HashMap 使用 hash%capacity 来确定桶下标。HashMap capacity 为 2 的 n 次方这一特点能够极大降低重新计算桶下标操作的复杂度。

   - 假设原数组长度 capacity 为 16，扩容之后 new capacity 为 32：

     - ```html
       capacity     : 00010000
       new capacity : 00100000
       ```

     - 对于一个 Key，它的哈希值 hash 在第 5 位：

       - 为 0，那么 hash%00010000 = hash%00100000，桶位置和原来一致；
       - 为 1，hash%00010000 = hash%00100000 + 16，桶位置是原位置 + 16

7. **计算数组容量：**

   - HashMap 构造函数允许用户传入的容量不是 2 的 n 次方，因为它可以自动地将传入的容量转换为 2 的 n 次方。

   - 先考虑如何求一个数的掩码，对于 10010000，它的掩码为 11111111，可以使用以下方法得到：

     - ```text
       mask |= mask >> 1    11011000
       mask |= mask >> 2    11111110
       mask |= mask >> 4    11111111
       ```

     - mask+1 是大于原始数字的最小的 2 的 n 次方。

     - ```text
       num     10010000
       mask+1 100000000
       ```

   - ```java
     static final int tableSizeFor(int cap) {
         int n = cap - 1;
         n |= n >>> 1;
         n |= n >>> 2;
         n |= n >>> 4;
         n |= n >>> 8;
         n |= n >>> 16;
         return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
     }
     ```

8. **链表转红黑树：**

   - 从 JDK 1.8 开始，一个桶存储的链表长度大于等于 8 时会将链表转换为红黑树。

9. 与HashTable比较：

   - Hashtable 使用 synchronized 来进行同步。
   - HashMap 可以插入键为 null 的 Entry。
   - HashMap 的迭代器是 fail-fast 迭代器。
   - HashMap 不能保证随着时间的推移 Map 中的元素次序是不变的。

### 2.3.6 ConcurrentHashMap

- 也主要是以JDK1.7 版本更好理解

1. **存储结构：**

   - ```java
     static final class HashEntry<K,V> {
         final int hash;
         final K key;
         volatile V value;
         volatile HashEntry<K,V> next;
     }
     ```

   - ConcurrentHashMap 和 HashMap 实现上类似，

   - JDK1.7：最主要的差别是 ConcurrentHashMap  采用了分段锁（Segment），每个分段锁维护着几个桶（HashEntry），多个线程可以同时访问不同分段锁上的桶，从而使其并发度更高（并发度就是 Segment 的个数）

   - JDK1.8：CAS+Synchronized来确保并发安全，synchornized只锁定当前链表或者红黑树的首节点！

   - Segment 继承自 ReentrantLock。

   - ```java
     static final class Segment<K,V> extends ReentrantLock implements Serializable {
     
         private static final long serialVersionUID = 2249069246763182397L;
     
         static final int MAX_SCAN_RETRIES =
             Runtime.getRuntime().availableProcessors() > 1 ? 64 : 1;
     
         transient volatile HashEntry<K,V>[] table;
     
         transient int count;
     
         transient int modCount;
     
         transient int threshold;
     
         final float loadFactor;
     }
     
     final Segment<K,V>[] segments;
     static final int DEFAULT_CONCURRENCY_LEVEL = 16;
     
     ```

   - 默认的并发级别为 16，也就是说默认创建 16 个 Segment。

2. **size操作：**

   - 每个 Segment 维护了一个 count 变量来统计该 Segment 中的键值对个数。

   - ```java
     /**
      * The number of elements. Accessed only either within locks
      * or among other volatile reads that maintain visibility.
      */
     transient int count;
     ```

   - 在执行 size 操作时，需要遍历所有 Segment 然后把 count 累计起来。

   - ConcurrentHashMap 在执行 size 操作时先尝试不加锁，如果连续两次不加锁操作得到的结果一致，那么可以认为这个结果是正确的。

   - 尝试次数使用 RETRIES_BEFORE_LOCK 定义，该值为 2，retries 初始值为 -1，因此尝试次数为 3。

   - 如果尝试的次数超过 3 次，就需要对每个 Segment 加锁。

   - ```java
     /**
      * Number of unsynchronized retries in size and containsValue
      * methods before resorting to locking. This is used to avoid
      * unbounded retries if tables undergo continuous modification
      * which would make it impossible to obtain an accurate result.
      */
     static final int RETRIES_BEFORE_LOCK = 2;
     
     public int size() {
         // Try a few times to get accurate count. On failure due to
         // continuous async changes in table, resort to locking.
         final Segment<K,V>[] segments = this.segments;
         int size;
         boolean overflow; // true if size overflows 32 bits
         long sum;         // sum of modCounts
         long last = 0L;   // previous sum
         int retries = -1; // first iteration isn't retry
         try {
             for (;;) {
                 // 超过尝试次数，则对每个 Segment 加锁
                 if (retries++ == RETRIES_BEFORE_LOCK) {
                     for (int j = 0; j < segments.length; ++j)
                         ensureSegment(j).lock(); // force creation
                 }
                 sum = 0L;
                 size = 0;
                 overflow = false;
                 for (int j = 0; j < segments.length; ++j) {
                     Segment<K,V> seg = segmentAt(segments, j);
                     if (seg != null) {
                         sum += seg.modCount;
                         int c = seg.count;
                         if (c < 0 || (size += c) < 0)
                             overflow = true;
                     }
                 }
                 // 连续两次得到的结果一致，则认为这个结果是正确的
                 if (sum == last)
                     break;
                 last = sum;
             }
         } finally {
             if (retries > RETRIES_BEFORE_LOCK) {
                 for (int j = 0; j < segments.length; ++j)
                     segmentAt(segments, j).unlock();
             }
         }
         return overflow ? Integer.MAX_VALUE : size;
     }
     ```

3. **JDK1.8的改动：**

   - JDK 1.7 使用分段锁机制来实现并发更新操作，核心类为 Segment，它继承自重入锁 ReentrantLock，并发度与 Segment 数量相等。
   - JDK 1.8 使用了 CAS 操作来支持更高的并发度，在 CAS 操作失败时使用内置锁 synchronized。
   - 并且 JDK 1.8 的实现也在链表过长时会转换为红黑树。

### 2.3.7 LinkedHashMap

1. **存储结构：**

   - 继承自 HashMap，因此具有和 HashMap 一样的快速查找特性。

   - ```java
     public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V>
     ```

   - 内部维护了一个双向链表，用来维护插入顺序或者 LRU 顺序。

   - ```java
     /**
      * The head (eldest) of the doubly linked list.
      */
     transient LinkedHashMap.Entry<K,V> head;
     
     /**
      * The tail (youngest) of the doubly linked list.
      */
     transient LinkedHashMap.Entry<K,V> tail;
     ```

   - accessOrder 决定了顺序，默认为 false，此时维护的是插入顺序。

   - ```java
     final boolean accessOrder;
     ```

   - LinkedHashMap 最重要的是以下用于维护顺序的函数，它们会在 put、get 等方法中调用。

   - ```java
     void afterNodeAccess(Node<K,V> p) { }
     void afterNodeInsertion(boolean evict) { }
     ```

2. **afterNodeAccess()：**

   - 当一个节点被访问时，如果 accessOrder 为 true，则会将该节点移到链表尾部。也就是说指定为 LRU 顺序之后，在每次访问一个节点时，会将这个节点移到链表尾部，保证链表尾部是最近访问的节点，那么链表首部就是最近最久未使用的节点。

   - ```java
     void afterNodeAccess(Node<K,V> e) { // move node to last
         LinkedHashMap.Entry<K,V> last;
         if (accessOrder && (last = tail) != e) {
             LinkedHashMap.Entry<K,V> p =
                 (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
             p.after = null;
             if (b == null)
                 head = a;
             else
                 b.after = a;
             if (a != null)
                 a.before = b;
             else
                 last = b;
             if (last == null)
                 head = p;
             else {
                 p.before = last;
                 last.after = p;
             }
             tail = p;
             ++modCount;
         }
     }
     ```

3. **afterNodeInsertion()：**

   - 在 put 等操作之后执行，当 removeEldestEntry() 方法返回 true 时会移除最晚的节点，也就是链表首部节点 first。

   - evict 只有在构建 Map 的时候才为 false，在这里为 true。

   - ```java
     void afterNodeInsertion(boolean evict) { // possibly remove eldest
         LinkedHashMap.Entry<K,V> first;
         if (evict && (first = head) != null && removeEldestEntry(first)) {
             K key = first.key;
             removeNode(hash(key), key, null, false, true);
         }
     }
     ```

   - removeEldestEntry() 默认为 false，如果需要让它为 true，需要继承 LinkedHashMap  并且覆盖这个方法的实现，这在实现 LRU 的缓存中特别有用，通过移除最近最久未使用的节点，从而保证缓存空间足够，并且缓存的数据都是热点数据

   - ```java
     protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
         return false;
     }
     ```

4. **LRU缓存：**

   - 以下是使用 LinkedHashMap 实现的一个 LRU 缓存：

     - 设定最大缓存空间 MAX_ENTRIES  为 3；
     - 使用 LinkedHashMap 的构造函数将 accessOrder 设置为 true，开启 LRU 顺序；
     - 覆盖 removeEldestEntry() 方法实现，在节点多于 MAX_ENTRIES 就会将最近最久未使用的数据移除。

   - ```java
     class LRUCache<K,V> extends LinkedHashMap<K,V>{
     	private static final int MAX_ENTRIES = 3;
     	protected boolean removeEldestEntry(Map.Entry eldest){
             return size()>MAX_ENTRIES;
         }
         LRUCache(){
             super(MAX_ENTRIES,0.75f,true);
         }
     }
     
     public static void main(String[] args) {
         LRUCache<Integer, String> cache = new LRUCache<>();
         cache.put(1, "a");
         cache.put(2, "b");
         cache.put(3, "c");
         cache.get(1);
         cache.put(4, "d");
         System.out.println(cache.keySet());
     }
     
     ```

### 3.3.8 WeakHashMap

1. **存储结构：**

   - WeakHashMap 的 Entry 继承自 WeakReference，被 WeakReference 关联的对象在下一次垃圾回收时会被回收。

   - WeakHashMap 主要用来实现缓存，通过使用 WeakHashMap 来引用缓存对象，由 JVM 对这部分缓存进行回收。

   - ```java
     private static class Entry<K,V> extends WeakReference<Object> implements Map.Entry<K,V>
     ```

2. **ConcurrentCache：**

   - Tomcat 中的 ConcurrentCache 使用了 WeakHashMap 来实现缓存功能。

   - ConcurrentCache 采取的是分代缓存：

     - 经常使用的对象放入 eden 中，eden 使用 ConcurrentHashMap 实现，不用担心会被回收（伊甸园）；
     - 不常用的对象放入 longterm，longterm 使用 WeakHashMap 实现，这些老对象会被垃圾收集器回收。
     - 当调用  get() 方法时，会先从 eden 区获取，如果没有找到的话再到 longterm 获取，当从 longterm 获取到就把对象放入 eden 中，从而保证经常被访问的节点不容易被回收。
     - 当调用 put() 方法时，如果 eden 的大小超过了 size，那么就将 eden 中的所有对象都放入 longterm 中，利用虚拟机回收掉一部分不经常使用的对象。

   - ```java
     public final class ConcurrentCache<K, V> {
     
         private final int size;
     
         private final Map<K, V> eden;
     
         private final Map<K, V> longterm;
     
         public ConcurrentCache(int size) {
             this.size = size;
             this.eden = new ConcurrentHashMap<>(size);
             this.longterm = new WeakHashMap<>(size);
         }
     
         public V get(K k) {
             V v = this.eden.get(k);
             if (v == null) {
                 v = this.longterm.get(k);
                 if (v != null)
                     this.eden.put(k, v);
             }
             return v;
         }
     
         public void put(K k, V v) {
             if (this.eden.size() >= size) {
                 this.longterm.putAll(this.eden);
                 this.eden.clear();
             }
             this.eden.put(k, v);
         }
     }
     ```

# 3. Java并发

## 3.1 使用线程

- 有三种使用线程的方法：

  - 实现 Runnable 接口；
  - 实现 Callable 接口；
  - 继承 Thread 类。

- 实现 Runnable 和 Callable 接口的类只能当做一个可以在线程中运行的任务，不是真正意义上的线程，因此最后还需要通过 Thread 来调用。可以理解为任务是通过线程驱动从而执行的。

- **实现Runnable接口：**

  - 需要实现接口中的 run() 方法。

  - ```java
    public class MyRunnable implements Runnable {
        @Override
        public void run() {
            // ...
        }
    }
    ```

  - 使用 Runnable 实例再创建一个 Thread 实例，然后调用 Thread 实例的 start() 方法来启动线程。

  - ```java
    public static void main(String[] args) {
        MyRunnable instance = new MyRunnable();
        Thread thread = new Thread(instance);
        thread.start();
    }
    ```

- **实现Callable接口：**

  - 与 Runnable 相比，Callable 可以有返回值，返回值通过 FutureTask 进行封装。

  - ```java
    public class MyCallable implements Callable<Integer> {
        public Integer call() {
            return 123;
        }
    }
    ```

  - ```java
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        MyCallable mc = new MyCallable();
        FutureTask<Integer> ft = new FutureTask<>(mc);
        Thread thread = new Thread(ft);
        thread.start();
        System.out.println(ft.get());
    }
    ```

- **继承Thread类：**

  - 同样也是需要实现 run() 方法，因为 Thread 类也实现了 Runable 接口。

  - 当调用 start() 方法启动一个线程时，虚拟机会将该线程放入就绪队列中等待被调度，当一个线程被调度时会执行该线程的 run() 方法。

  - ```java
    public class MyThread extends Thread {
        public void run() {
            // ...
        }
    }
    ```

  - ```java
    public static void main(String[] args) {
        MyThread mt = new MyThread();
        mt.start();
    }
    ```

- **实现接口和继承Thread？**

  - 实现接口会更好一些，因为：
    - Java 不支持多重继承，因此继承了 Thread 类就无法继承其它类，但是可以实现多个接口；
    - 类可能只要求可执行就行，继承整个 Thread 类开销过大。

## 3.2 基础线程机制

- **Executors：**

  - Executors 管理多个异步任务的执行，而无需程序员显式地管理线程的生命周期。这里的异步是指多个任务的执行互不干扰，不需要进行同步操作。

  - 主要有三种 Executor：

    - CachedThreadPool：一个任务创建一个线程；
    - FixedThreadPool：所有任务只能使用固定大小的线程；
    - SingleThreadExecutor：相当于大小为 1 的 FixedThreadPool。

  - ```java
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < 5; i++) {
            executorService.execute(new MyRunnable());
        }
        executorService.shutdown();
    }
    ```

- **Daemon：**

  - 守护线程是程序运行时在后台提供服务的线程，不属于程序中不可或缺的部分。

  - 当所有非守护线程结束时，程序也就终止，同时会杀死所有守护线程。

  - main() 属于非守护线程。

  - 在线程启动之前使用 setDaemon() 方法可以将一个线程设置为守护线程。

  - ```java
    public static void main(String[] args) {
        Thread thread = new Thread(new MyRunnable());
        thread.setDaemon(true);
    }
    ```

- **sleep()：**

  - Thread.sleep(millisec) 方法会休眠当前正在执行的线程，millisec 单位为毫秒。

  - sleep() 可能会抛出 InterruptedException，因为异常不能跨线程传播回 main() 中，因此必须在本地进行处理。线程中抛出的其它异常也同样需要在本地进行处理。

  - ```java
    public void run() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    ```

- **yield()：**

  - 对静态方法 Thread.yield() 的调用声明了当前线程已经完成了生命周期中最重要的部分，可以切换给其它线程来执行。该方法只是对线程调度器的一个建议，而且也只是建议具有相同优先级的其它线程可以运行。

  - ```java
    public void run() {
        Thread.yield();
    }
    ```

## 3.3 中断

- 一个线程执行完毕之后会自动结束，如果在运行过程中发生异常也会提前结束。

- **InterruptedException：**

  - 通过调用一个线程的 interrupt() 来中断该线程，如果该线程处于阻塞、限期等待或者无限期等待状态，那么就会抛出 InterruptedException，从而提前结束该线程。但是不能中断 I/O 阻塞和 synchronized 锁阻塞。

  - 对于以下代码，在 main() 中启动一个线程之后再中断它，由于线程中调用了 Thread.sleep() 方法，因此会抛出一个 InterruptedException，从而提前结束线程，不执行之后的语句。

  - ```java
    public class InterruptExample {
    
        private static class MyThread1 extends Thread {
            @Override
            public void run() {
                try {
                    Thread.sleep(2000);
                    System.out.println("Thread run");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Thread thread1 = new MyThread1();
        thread1.start();
        thread1.interrupt();
        System.out.println("Main run");
    }
    
    ```

- **interrupted()：**

  - 如果一个线程的 run() 方法执行一个无限循环，并且没有执行 sleep() 等会抛出 InterruptedException 的操作，那么调用线程的 interrupt() 方法就无法使线程提前结束。

  - 但是调用 interrupt() 方法会设置线程的中断标记，此时**调用 interrupted() 方法会返回 true**。因此可以在循环体中使用 interrupted() 方法来判断线程是否处于中断状态，从而提前结束线程。

  - ```java
    public class InterruptExample {
    
        private static class MyThread2 extends Thread {
            @Override
            public void run() {
                while (!interrupted()) {
                    // ..
                }
                System.out.println("Thread end");
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Thread thread2 = new MyThread2();
        thread2.start();
        thread2.interrupt();
    }
    
    ```

- **Executor的中断操作：**

  - 调用 Executor 的 shutdown() 方法会等待线程都执行完毕之后再关闭，但是如果调用的是 shutdownNow() 方法，则相当于调用每个线程的 interrupt() 方法。

  - 以下使用 Lambda 创建线程，相当于创建了一个匿名内部线程。

  - ```java
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(() -> {
            try {
                Thread.sleep(2000);
                System.out.println("Thread run");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        executorService.shutdownNow();
        System.out.println("Main run");
    }
    ```

  - 如果只想中断 Executor 中的一个线程，可以通过使用 submit() 方法来提交一个线程，它会返回一个 Future<?> 对象，通过调用该对象的 cancel(true) 方法就可以中断线程。

  - ```java
    Future<?> future = executorService.submit(() -> {
        // ..
    });
    future.cancel(true);
    ```

## 3.4 互斥同步

- 为什么需要互斥同步？

  - 为了控制多个线程对共享资源的访问

- Java提供了哪些互斥同步的锁机制？

  - Java 提供了两种锁机制来控制多个线程对共享资源的互斥访问，
  - 第一个是 JVM 实现的 synchronized，
  - 另一个是 JDK 实现的 ReentrantLock。

- **synchronized：**

  - 同步代码块：锁定的是对象

    - ```java
      public void func() {
          synchronized (this) {
              // ...
          }
      }
      ```

  - 同步方法：锁的是对象

    - ```java
      public synchronized void func () {
          // ...
      }
      ```

  - 同步类：锁的是整个类

    - 作用于整个类，也就是说两个线程调用同一个类的不同对象上的这种同步语句，也会进行同步。

    - ```java
      public void func() {
          synchronized (SynchronizedExample.class) {
              // ...
          }
      }
      ```

  - 同步静态方法：锁的是整个类

    - ```java
      public synchronized static void fun() {
          // ...
      }
      ```

- **ReentrantLock：**

  - ReentrantLock 是 java.util.concurrent（J.U.C）包中的锁。

  - ```java
    public class LockExample {
    
        private Lock lock = new ReentrantLock();
    
        public void func() {
            lock.lock();
            try {
                for (int i = 0; i < 10; i++) {
                    System.out.print(i + " ");
                }
            } finally {
                lock.unlock(); // 确保释放锁，从而避免发生死锁。
            }
        }
        public static void main(String[] args) {
        LockExample lockExample = new LockExample();
        ExecutorService executorService = Executors.newCachedThreadPool();
        executorService.execute(() -> lockExample.func());
        executorService.execute(() -> lockExample.func());
    }
    
    }
    ```

- **synchronized和Reentrantlock比较：**
  - 锁的实现：
    - synchronized是JVM实现的
    - ReenttrantLock是JDK实现的，基于AQS框架实现
  - 性能：
    - 新版本的Java对synchronized进行了很多的优化，如自旋锁等，性能上已经和reentrantLock大致相同
  - 等待可中断：
    - 什么是等待可中断？
      - 当持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情。
    - ReentrankLock可中断、synchronized不可中断
  - 公平锁：
    - 公平锁是指多个线程在等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁。
    - synchronized 中的锁是非公平的，ReentrantLock 默认情况下也是非公平的，但是也可以是公平的。
  - 锁绑定多个条件：
    - 一个 ReentrantLock 可以同时绑定多个 Condition 对象
- 如何选择是使用synchronized和reentrantLock？
  - 除非需要使用 ReentrantLock 的高级功能，否则优先使用 synchronized。这是因为 synchronized 是 JVM  实现的一种锁机制，JVM 原生地支持它，而 ReentrantLock 不是所有的 JDK 版本都支持。
  - 并且使用 synchronized  不用担心没有释放锁而导致死锁问题，因为 JVM 会确保锁的释放。

## 3.5 线程间的协作

- 当多个线程可以一起工作去解决某个问题时，如果某些部分必须在其它部分之前完成，那么就需要对线程进行协调。

- **join()：**

  - 在线程中调用另一个线程的 join() 方法，会将当前线程挂起，而不是忙等待，直到目标线程结束。

  - 对于以下代码，虽然 b 线程先启动，但是因为在 b 线程中调用了 a 线程的 join() 方法，b 线程会等待 a 线程结束才继续执行，因此最后能够保证 a 线程的输出先于 b 线程的输出。

  - ```java
    public class JoinExample {
    
        private class A extends Thread {
            @Override
            public void run() {
                System.out.println("A");
            }
        }
    
        private class B extends Thread {
    
            private A a;
    
            B(A a) {
                this.a = a;
            }
    
            @Override
            public void run() {
                try {
                    a.join();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("B");
            }
        }
    
        public void test() {
            A a = new A();
            B b = new B(a);
            b.start();
            a.start();
        }
        
        public static void main(String[] args) {
        JoinExample example = new JoinExample();
        example.test();
    }
    
    }
    ```

- **wait()、notify()、notifyAll()：**

  - 调用 wait() 使得线程等待某个条件满足，线程在等待时会被挂起，当其他线程的运行使得这个条件满足时，其它线程会调用 notify() 或者 notifyAll() 来唤醒挂起的线程。

  - 它们都属于 Object 的一部分，而不属于 Thread。

  - 只能用在**同步方法或者同步控制块**中使用，否则会在运行时抛出 IllegalMonitorStateException。

  - 使用 wait() 挂起期间，线程会释放锁。这是因为，如果没有释放锁，那么其它线程就无法进入对象的同步方法或者同步控制块中，那么就无法执行 notify() 或者 notifyAll() 来唤醒挂起的线程，造成死锁。

  - ```java
    public class WaitNotifyExample {
    
        public synchronized void before() {
            System.out.println("before");
            notifyAll();
        }
    
        public synchronized void after() {
            try {
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("after");
        }
        public static void main(String[] args) {
            ExecutorService executorService = Executors.newCachedThreadPool();
            WaitNotifyExample example = new WaitNotifyExample();
            executorService.execute(() -> example.after());
            executorService.execute(() -> example.before());
    }
    
    }
    ```

- **wait()和sleep()的区别？**

  - wait() 是 Object 的方法，而 sleep() 是 Thread 的静态方法；
  - wait() 会释放锁，sleep() 不会。

- **await()、singnal()、singnalAll()：**

  - java.util.concurrent 类库中提供了 Condition 类来实现线程之间的协调，可以在 Condition 上调用 await() 方法使线程等待，其它线程调用 signal() 或 signalAll() 方法唤醒等待的线程。

  - 相比于 wait() 这种等待方式，await() 可以指定等待的条件，因此更加灵活。

  - 使用 Lock 来获取一个 Condition 对象。

  - ```java
    public class AwaitSignalExample {
    
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
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                lock.unlock();
            }
        }
        public static void main(String[] args) {
            ExecutorService executorService = Executors.newCachedThreadPool();
            AwaitSignalExample example = new AwaitSignalExample();
            executorService.execute(() -> example.after());
            executorService.execute(() -> example.before());
        }
    
    }
    ```

## 3.6 线程状态

- 一个线程只能处于一种状态，并且这里的线程状态特指 Java 虚拟机的线程状态，不能反映线程在特定操作系统下的状态。

- 新建(new)：

  - 创建后尚未启动。

- 可运行(runnable)：

  - 正在 Java 虚拟机中运行。但是在操作系统层面，它可能处于运行状态，也可能等待资源调度（例如处理器资源），资源调度完成就进入运行状态。所以该状态的可运行是指可以被运行，具体有没有运行要看底层操作系统的资源调度。

- 阻塞(Blocked)：

  - 请求获取 monitor lock 从而进入 synchronized 函数或者代码块，但是其它线程已经占用了该 monitor lock，所以出于阻塞状态。要结束该状态进入从而 RUNABLE 需要其他线程释放 monitor lock。

- 无限期等待(waiting)：

  - 等待其它线程显式地唤醒。

  - 阻塞和等待的区别在于，阻塞是被动的，它是在等待获取 monitor lock。而等待是主动的，通过调用  Object.wait() 等方法进入。

  - | 进入方法                                   | 退出方法                             |
    | ------------------------------------------ | ------------------------------------ |
    | 没有设置 Timeout 参数的 Object.wait() 方法 | Object.notify() / Object.notifyAll() |
    | 没有设置 Timeout 参数的 Thread.join() 方法 | 被调用的线程执行完毕                 |
    | LockSupport.park() 方法                    | LockSupport.unpark(Thread)           |

- 限期等待(timed_waiting)：

  - 无需等待其它线程显式地唤醒，在一定时间之后会被系统自动唤醒。

  - | 进入方法                                 | 退出方法                                        |
    | ---------------------------------------- | ----------------------------------------------- |
    | Thread.sleep() 方法                      | 时间结束                                        |
    | 设置了 Timeout 参数的 Object.wait() 方法 | 时间结束 / Object.notify() / Object.notifyAll() |
    | 设置了 Timeout 参数的 Thread.join() 方法 | 时间结束 / 被调用的线程执行完毕                 |
    | LockSupport.parkNanos() 方法             | LockSupport.unpark(Thread)                      |
    | LockSupport.parkUntil() 方法             | LockSupport.unpark(Thread)                      |

  - 调用 Thread.sleep() 方法使线程进入限期等待状态时，常常用“使一个线程睡眠”进行描述。调用 Object.wait()  方法使线程进入限期等待或者无限期等待时，常常用“挂起一个线程”进行描述。睡眠和挂起是用来描述行为，而阻塞和等待用来描述状态。

- 死亡(terminated)：

  - 可以是线程结束任务之后自己结束，或者产生了异常而结束。

- **线程和进程的状态：**

  - ![image-20211221160102974](1_Java再概览.assets/image-20211221160102974.png)
  - ![image-20211221160423426](1_Java再概览.assets/image-20211221160423426.png)

## 3.7 JUC-AQS

- java.util.concurrent（J.U.C）大大提高了并发性能，AQS 被认为是 J.U.C 的核心。

- **AQS：**

  - AQS 的全称为（`AbstractQueuedSynchronizer`）：抽象队列同步器，这个类在`java.util.concurrent.locks`包下面。
  - 作用：
    - **模板方法模式：**构建锁和同步器的框架，使用AQS可以简单且高效的构造出广泛大量的同步器：
      - 如：ReentrantLock、Semaphore、ReentrantReadWritedLock，SynchronousQueue、FutureTask
  - 核心思想：
    - 如果被请求的共享资源空闲，则将当前请求资源的线程设置位有效的工作线程，并且将共享资源设置为锁定状态、
    - 如果被请求的共享资源被占用，则需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制AQS是采用CLH队列锁实现的，也就是：将暂时获取不到锁的线程加入到队列中
      - CLH(Craig,Landin,and Hagersten)队列是一个虚拟的双向队列（虚拟的双向队列即不存在队列实例，仅存在结点之间的关联关系）。AQS 是将每条请求共享资源的线程封装成一个 CLH 锁队列的一个结点（Node）来实现锁的分配。
    - ![image-20211221160744336](1_Java再概览.assets/image-20211221160744336.png)

- **CountDownLatch：**

  - 用来控制一个或者多个线程等待多个线程。

  - 维护了一个计数器 cnt，每次调用 CountDownLatch的countDown() 方法会让计数器的值减 1，减到 0 的时候，那些因为调用 CountDownLatch的await() 方法而在等待的线程就会被唤醒。

  - ![img](1_Java再概览.assets/ba078291-791e-4378-b6d1-ece76c2f0b14.png)

  - ```java
    public class CountdownLatchExample {
    
        public static void main(String[] args) throws InterruptedException {
            final int totalThread = 10;
            CountDownLatch countDownLatch = new CountDownLatch(totalThread);
            ExecutorService executorService = Executors.newCachedThreadPool();
            for (int i = 0; i < totalThread; i++) {
                executorService.execute(() -> {
                    System.out.print("run..");
                    countDownLatch.countDown();
                });
            }
            countDownLatch.await();
            System.out.println("end");
            executorService.shutdown();
        }
    }
    ```

- **CyclicBarrier：**

  - 用来控制多个线程互相等待，只有当多个线程都到达时，这些线程才会继续执行。

  - 和 CountdownLatch 相似，都是通过维护计数器来实现的。线程执行 await() 方法之后计数器会减 1，并进行等待，直到计数器为 0，所有调用 await() 方法而在等待的线程才能继续执行。

  - CyclicBarrier 和 CountdownLatch 的一个区别是，CyclicBarrier 的计数器通过调用 reset() 方法可以循环使用，所以它才叫做循环屏障。

  - CyclicBarrier 有两个构造函数，其中 parties 指示计数器的初始值，barrierAction 在所有线程都到达屏障的时候会执行一次。

  - ![img](1_Java再概览.assets/f71af66b-0d54-4399-a44b-f47b58321984.png)

  - ```java
    public class CyclicBarrierExample {
    
        public static void main(String[] args) {
            final int totalThread = 10;
            CyclicBarrier cyclicBarrier = new CyclicBarrier(totalThread);
            ExecutorService executorService = Executors.newCachedThreadPool();
            for (int i = 0; i < totalThread; i++) {
                executorService.execute(() -> {
                    System.out.print("before..");
                    try {
                        cyclicBarrier.await();
                    } catch (InterruptedException | BrokenBarrierException e) {
                        e.printStackTrace();
                    }
                    System.out.print("after..");
                });
            }
            executorService.shutdown();
        }
    }
    ```

- **Semaphore：**

  - Semaphore 类似于操作系统中的信号量，可以控制对互斥资源的访问线程数。

  - 以下代码模拟了对某个服务的并发请求，每次只能有 3 个客户端同时访问，请求总数为 10。

  - ```java
    public class SemaphoreExample {
    
        public static void main(String[] args) {
            final int clientCount = 3;
            final int totalRequestCount = 10;
            Semaphore semaphore = new Semaphore(clientCount);
            ExecutorService executorService = Executors.newCachedThreadPool();
            for (int i = 0; i < totalRequestCount; i++) {
                executorService.execute(()->{
                    try {
                        semaphore.acquire();
                        System.out.print(semaphore.availablePermits() + " ");
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        semaphore.release();
                    }
                });
            }
            executorService.shutdown();
        }
    }
    ```

- **FutureTask：**

  - 在介绍 Callable 时我们知道它可以有返回值，返回值通过 Future<V> 进行封装。FutureTask 实现了  RunnableFuture 接口，该接口继承自 Runnable 和 Future<V> 接口，这使得 FutureTask  既可以当做一个任务执行，也可以有返回值。

  - ```java
    public class FutureTask<V> implements RunnableFuture<V>
    
        
    // 接口间可以进行多继承
    public interface RunnableFuture<V> extends Runnable, Future<V>
    ```

  - FutureTask 可用于异步获取执行结果或取消执行任务的场景。当一个计算任务需要执行很长时间，那么就可以用 FutureTask 来封装这个任务，主线程在完成自己的任务之后再去获取结果。

  - ```java
    public class FutureTaskExample {
    
        public static void main(String[] args) throws ExecutionException, InterruptedException {
            FutureTask<Integer> futureTask = new FutureTask<Integer>(new Callable<Integer>() {
                @Override
                public Integer call() throws Exception {
                    int result = 0;
                    for (int i = 0; i < 100; i++) {
                        Thread.sleep(10);
                        result += i;
                    }
                    return result;
                }
            });
    
            Thread computeThread = new Thread(futureTask);
            computeThread.start();
    
            Thread otherThread = new Thread(() -> {
                System.out.println("other task is running...");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
            otherThread.start();
            System.out.println(futureTask.get());
        }
    }
    ```

- **BlockingQueue：**

  - java.util.concurrent.BlockingQueue 接口有以下阻塞队列的实现：

    - **FIFO 队列**  ：LinkedBlockingQueue、ArrayBlockingQueue（固定长度）
    - **优先级队列**  ：PriorityBlockingQueue

  - 提供了阻塞的 take() 和 put() 方法：如果队列为空 take() 将阻塞，直到队列中有内容；如果队列为满 put() 将阻塞，直到队列有空闲位置。

  - **使用 BlockingQueue 实现生产者消费者问题**

  - ```java
    public class ProducerConsumer {
    
        private static BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);
    
        private static class Producer extends Thread {
            @Override
            public void run() {
                try {
                    queue.put("product");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.print("produce..");
            }
        }
    
        private static class Consumer extends Thread {
    
            @Override
            public void run() {
                try {
                    String product = queue.take();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.print("consume..");
            }
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
    
    ```

- **ForkJoin：**

  - 主要用于并行计算中，和 MapReduce 原理类似，都是把大的计算任务拆分成多个小任务并行计算。

  - ```java
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
    }
    
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ForkJoinExample example = new ForkJoinExample(1, 10000);
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        Future result = forkJoinPool.submit(example);
        System.out.println(result.get());
    }
    
    ```

  - ForkJoin 使用 ForkJoinPool 来启动，它是一个特殊的线程池，线程数量取决于 CPU 核数。

  - ForkJoinPool 实现了工作窃取算法来提高 CPU  的利用率。每个线程都维护了一个双端队列，用来存储需要执行的任务。工作窃取算法允许空闲的线程从其它线程的双端队列中窃取一个任务来执行。窃取的任务必须是最晚的任务，避免和队列所属线程发生竞争。例如下图中，Thread2 从 Thread1 的队列中拿出最晚的 Task1 任务，Thread1 会拿出 Task2  来执行，这样就避免发生竞争。但是如果队列中只有一个任务时还是会发生竞争。

  - ![img](1_Java再概览.assets/e42f188f-f4a9-4e6f-88fc-45f4682072fb.png)

## 3.8 线程不安全示例

- 突出互斥同步的重要性！

- 如果多个线程对同一个共享数据进行访问而不采取同步操作的话，那么操作的结果是不一致的。

- 以下代码演示了 1000 个线程同时对 cnt 执行自增操作，操作结束之后它的值有可能小于 1000。

- ```java
  public class ThreadUnsafeExample {
  
      private int cnt = 0;
  
      public void add() {
          cnt++;
      }
  
      public int get() {
          return cnt;
      }
  }
  
  public static void main(String[] args) throws InterruptedException {
      final int threadSize = 1000;
      ThreadUnsafeExample example = new ThreadUnsafeExample();
      final CountDownLatch countDownLatch = new CountDownLatch(threadSize);
      ExecutorService executorService = Executors.newCachedThreadPool();
      for (int i = 0; i < threadSize; i++) {
          executorService.execute(() -> {
              example.add();
              countDownLatch.countDown();
          });
      }
      countDownLatch.await();
      executorService.shutdown();
      System.out.println(example.get());
  }
  
  ```

## 3.9 JMM

- JMM：Java Memory Model：Java 内存模型试图屏蔽各种硬件和操作系统的内存访问差异，以实现让 Java 程序在各种平台下都能达到一致的内存访问效果。

- **主内存和工作内存：**

  - JMM规定了内存主要划分为主内存和工作内存两种。此处的主内存和工作内存跟JVM内存划分（堆、栈、方法区）是在不同的层次上进行的
  - 如果非要对应起来，主内存对应的是Java堆中的对象实例部分，工作内存对应的是栈中的部分区域
  - 从更底层的来说，主内存对应的是硬件的物理内存，工作内存对应的是寄存器和高速缓存。
  - 处理器上的寄存器的读写的速度比内存快几个数量级，为了解决这种速度矛盾，在它们之间加入了高速缓存。
  - 加入高速缓存带来了一个新的问题：缓存一致性。如果多个缓存共享同一块主内存区域，那么多个缓存的数据可能会不一致，需要一些协议来解决这个问题。
  - ![img](1_Java再概览.assets/942ca0d2-9d5c-45a4-89cb-5fd89b61913f.png)
  - 所有的变量都存储在主内存中，每个线程还有自己的工作内存，工作内存存储在高速缓存或者寄存器中，保存了该线程使用的变量的主内存副本拷贝。
  - 线程只能直接操作工作内存中的变量，不同线程之间的变量值传递需要通过主内存来完成。
  - ![img](1_Java再概览.assets/15851555-5abc-497d-ad34-efed10f43a6b.png)

- **内存间交互操作：**

  - Java 内存模型定义了 8 个操作来完成主内存和工作内存的交互操作。
  - ![image-20211221163948942](1_Java再概览.assets/image-20211221163948942.png)
    - read：把一个变量的值从主内存传输到工作内存中
    - load：在 read 之后执行，把 read 得到的值放入工作内存的变量副本中
    - use：把工作内存中一个变量的值传递给执行引擎
    - assign：把一个从执行引擎接收到的值赋给工作内存的变量
    - store：把工作内存的一个变量的值传送到主内存中
    - write：在 store 之后执行，把 store 得到的值放入主内存的变量中
    - lock：作用于主内存的变量
    - unlock

- **JMM三大特性：**

  - **原子性：**

    - Java 内存模型保证了 read、load、use、assign、store、write、lock 和 unlock  操作具有原子性，例如对一个 int 类型的变量执行 assign 赋值操作，这个操作就是原子性的。但是 Java 内存模型允许虚拟机将没有被  volatile 修饰的 64 位数据（long，double）的读写操作划分为两次 32 位的操作来进行，即 load、store、read 和 write 操作可以不具备原子性。

    - 有一个错误认识就是，int 等原子性的类型在多线程环境中不会出现线程安全问题。前面的线程不安全示例代码中，cnt 属于 int 类型变量，1000 个线程对它进行自增操作之后，得到的值为 997 而不是 1000。

    - 为了方便讨论，将内存间的交互操作简化为 3 个：load、assign、store。

    - 下图演示了两个线程同时对 cnt 进行操作，load、assign、store 这一系列操作整体上看不具备原子性，那么在 T1 修改 cnt  并且还没有将修改后的值写入主内存，T2 依然可以读入旧值。可以看出，这两个线程虽然执行了两次自增运算，但是主内存中 cnt 的值最后为 1  而不是 2。因此对 int 类型读写操作满足原子性只是说明 load、assign、store 这些单个操作具备原子性。

    - ![img](1_Java再概览.assets/2797a609-68db-4d7b-8701-41ac9a34b14f.jpg)

    - AtomicInteger 能保证多个线程修改的原子性

    - ![img](1_Java再概览.assets/dd563037-fcaa-4bd8-83b6-b39d93a12c77.jpg)

    - 使用 AtomicInteger 重写之前线程不安全的代码之后得到以下线程安全实现：

    - ```java
      public class AtomicExample {
          private AtomicInteger cnt = new AtomicInteger();
      
          public void add() {
              cnt.incrementAndGet();
          }
      
          public int get() {
              return cnt.get();
          }
      }
      
      public static void main(String[] args) throws InterruptedException {
          final int threadSize = 1000;
          AtomicExample example = new AtomicExample(); // 只修改这条语句
          final CountDownLatch countDownLatch = new CountDownLatch(threadSize);
          ExecutorService executorService = Executors.newCachedThreadPool();
          for (int i = 0; i < threadSize; i++) {
              executorService.execute(() -> {
                  example.add();
                  countDownLatch.countDown();
              });
          }
          countDownLatch.await();
          executorService.shutdown();
          System.out.println(example.get());
      }
      
      ```

    - 除了使用原子类之外，也可以使用 synchronized 互斥锁来保证操作的原子性。它对应的内存间交互操作为：lock 和 unlock，在虚拟机实现上对应的字节码指令为 monitorenter 和 monitorexit。

    - ```java
      public class AtomicSynchronizedExample {
          private int cnt = 0;
      
          public synchronized void add() {
              cnt++;
          }
      
          public synchronized int get() {
              return cnt;
          }
      }
      
      public static void main(String[] args) throws InterruptedException {
          final int threadSize = 1000;
          AtomicSynchronizedExample example = new AtomicSynchronizedExample();
          final CountDownLatch countDownLatch = new CountDownLatch(threadSize);
          ExecutorService executorService = Executors.newCachedThreadPool();
          for (int i = 0; i < threadSize; i++) {
              executorService.execute(() -> {
                  example.add();
                  countDownLatch.countDown();
              });
          }
          countDownLatch.await();
          executorService.shutdown();
          System.out.println(example.get());
      }
      
      ```

  - **可见性：**

    - 可见性指当一个线程修改了共享变量的值，其它线程能够立即得知这个修改。
    - Java 内存模型是通过在变量修改后将新值同步回主内存，在变量读取前从主内存刷新变量值来实现可见性的。
    - 主要有三种实现可见性的方式：
      - volatile
      - synchronized：对一个变量执行 unlock 操作之前，必须把变量值同步回主内存。
      - final：被 final 关键字修饰的字段在构造器中一旦初始化完成，并且没有发生 this 逃逸（其它线程通过 this 引用访问到初始化了一半的对象），那么其它线程就能看见 final 字段的值。
    - 对前面的线程不安全示例中的 cnt 变量使用 volatile 修饰，不能解决线程不安全问题，因为 volatile 并不能保证操作的原子性。

  - **有序性：**

    - 有序性是指：在本线程内观察，所有操作都是有序的。在一个线程观察另一个线程，所有操作都是无序的，无序是因为发生了指令重排序。在 Java 内存模型中，允许编译器和处理器对指令进行重排序，重排序过程不会影响到单线程程序的执行，却会影响到多线程并发执行的正确性。
    - 两种实现有序性的方式：
      - volatile 关键字通过添加内存屏障的方式来禁止指令重排，即重排序时不能把后面的指令放到内存屏障之前。
      - 也可以通过 synchronized 来保证有序性，它保证每个时刻只有一个线程执行同步代码，相当于是让线程顺序执行同步代码。

- **先行发生原则：**

  - 上面提到了可以用 volatile 和 synchronized 来保证有序性。除此之外，JVM 还规定了先行发生原则，让一个操作无需控制就能先于另一个操作完成。
  - **单一线程原则：**
    - 在一个线程内，在程序前面的操作先行发生于后面的操作。
    - ![img](1_Java再概览.assets/874b3ff7-7c5c-4e7a-b8ab-a82a3e038d20.png)
  - **管程锁定规则：**
    - Monitor Lock Rule：一个 unlock 操作先行发生于后面对同一个锁的 lock 操作。
    - ![img](1_Java再概览.assets/8996a537-7c4a-4ec8-a3b7-7ef1798eae26.png)
  - **volatile变量规则：**
    - 对一个 volatile 变量的写操作先行发生于后面对这个变量的读操作。
    - ![img](1_Java再概览.assets/942f33c9-8ad9-4987-836f-007de4c21de0.png)
  - **线程启动规则：**
    - Thread 对象的 start() 方法调用先行发生于此线程的每一个动作。
    - ![img](1_Java再概览.assets/6270c216-7ec0-4db7-94de-0003bce37cd2.png)
  - **线程加入规则：**
    - Thread 对象的结束先行发生于 join() 方法返回。
    - ![img](1_Java再概览.assets/233f8d89-31d7-413f-9c02-042f19c46ba1.png)
  - **线程中断规则：**
    - 对线程 interrupt() 方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过 interrupted() 方法检测到是否有中断发生。
  - **对象终结规则：**
    - 一个对象的初始化完成（构造函数执行结束）先行发生于它的 finalize() 方法的开始。
  - **传递性：**
    - 如果操作 A 先行发生于操作 B，操作 B 先行发生于操作 C，那么操作 A 先行发生于操作 C。

## 3.10 线程安全

- 什么是线程安全？

  - 多个线程不管以何种方式访问某个类，并且在主调代码中不需要进行同步，都能表现正确的行为。

- 如何实现线程安全？

  - 不可变
  - 互斥同步
  - 非阻塞同步
  - 无同步

- **不可变：**

  - 不可变（Immutable）的对象一定是线程安全的，不需要再采取任何的线程安全保障措施。只要一个不可变的对象被正确地构建出来，永远也不会看到它在多个线程之中处于不一致的状态。多线程环境下，应当尽量使对象成为不可变，来满足线程安全。

  - 不可变的类型：

    - final 关键字修饰的基本数据类型

    - String

    - 枚举类型

    - Number 部分子类，如 Long 和 Double 等数值包装类型，BigInteger 和 BigDecimal 等大数据类型。但同为 Number 的原子类 AtomicInteger 和 AtomicLong 则是可变的。

    - 对于集合类型，可以使用 Collections.unmodifiableXXX() 方法来获取一个不可变的集合。

      - Collections.unmodifiableXXX() 先对原始的集合进行拷贝，需要对集合进行修改的方法都直接抛出异常。

      - ```java
        public V put(K key, V value) {
            throw new UnsupportedOperationException();
        }
        ```

- **互斥同步(阻塞同步)：**

  - synchronized和ReentrantLock

- **非阻塞同步：**

  - 互斥同步最主要的问题就是线程阻塞和唤醒所带来的性能问题，因此这种同步也称为阻塞同步。

  - 互斥同步属于一种**悲观**的并发策略，总是认为只要不去做正确的同步措施，那就肯定会出现问题。无论共享数据是否真的会出现竞争，它都要进行加锁（这里讨论的是概念模型，实际上虚拟机会优化掉很大一部分不必要的加锁）、用户态核心态转换、维护锁计数器和检查是否有被阻塞的线程需要唤醒等操作。

  - 随着硬件指令集的发展，我们可以使用基于冲突检测的**乐观**并发策略：**先进行操作，如果没有其它线程争用共享数据，那操作就成功了，否则采取补偿措施（不断地重试，直到成功为止）**。这种乐观的并发策略的许多实现都不需要将线程阻塞，因此这种同步操作称为非阻塞同步。

  - **CAS：**

    - 乐观锁需要操作和冲突检测这两个步骤具备**原子性**，这里就不能再使用互斥同步来保证了，只能**靠硬件**来完成。硬件支持的原子性操作最典型的是：比较并交换（Compare-and-Swap，CAS）。CAS 指令需要有 3 个操作数，分别是内存地址 V、旧的预期值 A 和新值 B。当执行操作时，只有当 V 的值等于 A，才将 V 的值更新为 B。

  - **AtomicInteger：**

    - J.U.C 包里面的整数原子类 AtomicInteger 的方法调用了 Unsafe 类的 CAS 操作。

    - ```java
      private AtomicInteger cnt = new AtomicInteger();
      
      public void add() {
          cnt.incrementAndGet();
      }
      
      public final int incrementAndGet() {
          return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
      }
      
      
      public final int getAndAddInt(Object var1, long var2, int var4) {
          int var5;
          do {
              var5 = this.getIntVolatile(var1, var2);
          } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));
      
          return var5;
      }
      ```

    - var1 指示对象内存地址，var2 指示该字段相对对象内存地址的偏移，var4 指示操作需要加的数值，这里为 1。通过  getIntVolatile(var1, var2) 得到旧的预期值，通过调用 compareAndSwapInt() 来进行 CAS  比较，如果该字段内存地址中的值等于 var5，那么就更新内存地址为 var1+var2 的变量为 var5+var4。

    - 可以看到 getAndAddInt() 在一个循环中进行，发生冲突的做法是不断的进行重试。

  - **ABA：**

    - 如果一个变量初次读取的时候是 A 值，它的值被改成了 B，后来又被改回为 A，那 CAS 操作就会误认为它从来没有被改变过。
    - J.U.C 包提供了一个带有标记的原子引用类 AtomicStampedReference 来解决这个问题，它可以通过控制变量值的版本来保证 CAS  的正确性。大部分情况下 ABA 问题不会影响程序并发的正确性，如果需要解决 ABA 问题，改用传统的互斥同步可能会比原子类更高效。

- **无同步方案：**

  - 要保证线程安全，并不是一定就要进行同步。如果一个方法本来就不涉及共享数据，那它自然就无须任何同步措施去保证正确性。

  - **栈封闭：**

    - 多个线程访问同一个方法的局部变量时，不会出现线程安全问题，因为局部变量存储在虚拟机栈中，属于线程私有的。

  - **线程本地存储(Thread Local Storage)：**

    - 如果一段代码中所需要的数据必须与其他代码共享，那就看看这些共享数据的代码是否能保证在同一个线程中执行。如果能保证，我们就可以把共享数据的可见范围限制在同一个线程之内，这样，无须同步也能保证线程之间不出现数据争用的问题。

    - 符合这种特点的应用并不少见，大部分使用消费队列的架构模式（如“生产者-消费者”模式）都会将产品的消费过程尽量在一个线程中消费完。其中最重要的一个应用实例就是经典 Web 交互模型中的“一个请求对应一个服务器线程”（Thread-per-Request）的处理方式，这种处理方式的广泛应用使得很多 Web  服务端应用都可以使用线程本地存储来解决线程安全问题。

    - 可以使用 java.lang.ThreadLocal 类来实现线程本地存储功能。

    - 对于以下代码，thread1 中设置 threadLocal 为 1，而 thread2 设置 threadLocal 为 2。过了一段时间之后，thread1 读取 threadLocal 依然是 1，不受 thread2 的影响。

    - ```java
      public class ThreadLocalExample {
          public static void main(String[] args) {
              ThreadLocal threadLocal = new ThreadLocal();
              Thread thread1 = new Thread(() -> {
                  threadLocal.set(1);
                  try {
                      Thread.sleep(1000);
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  }
                  System.out.println(threadLocal.get());
                  threadLocal.remove();
              });
              Thread thread2 = new Thread(() -> {
                  threadLocal.set(2);
                  threadLocal.remove();
              });
              thread1.start();
              thread2.start();
          }
      }
      
      public class ThreadLocalExample1 {
          public static void main(String[] args) {
              ThreadLocal threadLocal1 = new ThreadLocal();
              ThreadLocal threadLocal2 = new ThreadLocal();
              Thread thread1 = new Thread(() -> {
                  threadLocal1.set(1);
                  threadLocal2.set(1);
              });
              Thread thread2 = new Thread(() -> {
                  threadLocal1.set(2);
                  threadLocal2.set(2);
              });
              thread1.start();
              thread2.start();
          }
      }
      
      ```

    - ![img](1_Java再概览.assets/6782674c-1bfe-4879-af39-e9d722a95d39.png)

    - 每个 Thread 都有一个 ThreadLocal.ThreadLocalMap 对象。

      - ```java
        /* ThreadLocal values pertaining to this thread. This map is maintained
         * by the ThreadLocal class. */
        ThreadLocal.ThreadLocalMap threadLocals = null;
        ```

      - 当调用一个 ThreadLocal 的 set(T value) 方法时，先得到当前线程的 ThreadLocalMap 对象，然后将 ThreadLocal->value 键值对插入到该 Map 中

      - ```java
        public void set(T value) {
            Thread t = Thread.currentThread();
            ThreadLocalMap map = getMap(t);
            if (map != null)
                map.set(this, value);
            else
                createMap(t, value);
        }
        
        public T get() {
            Thread t = Thread.currentThread();
            ThreadLocalMap map = getMap(t);
            if (map != null) {
                ThreadLocalMap.Entry e = map.getEntry(this);
                if (e != null) {
                    @SuppressWarnings("unchecked")
                    T result = (T)e.value;
                    return result;
                }
            }
            return setInitialValue();
        }
        
        ```

    - ThreadLocal 从理论上讲并不是用来解决多线程并发问题的，因为根本不存在多线程竞争。

    - 在一些场景  (尤其是使用线程池) 下，由于 ThreadLocal.ThreadLocalMap 的底层数据结构导致 ThreadLocal  有内存泄漏的情况，应该尽可能在每次使用 ThreadLocal 后手动调用 remove()，以避免出现 ThreadLocal  经典的内存泄漏甚至是造成自身业务混乱的风险。

  - **可重入代码(Reentrant Code)：**

    - 这种代码也叫做纯代码（Pure Code），可以在代码执行的任何时刻中断它，转而去执行另外一段代码（包括递归调用它本身），而在控制权返回后，原来的程序不会出现任何错误。
    - 可重入代码有一些共同的特征，例如不依赖存储在堆上的数据和公用的系统资源、用到的状态量都由参数中传入、不调用非可重入的方法等

## 3.11 锁优化

- 锁优化主要是指 JVM 对 synchronized 的优化。

- **自旋锁：**

  - 互斥同步进入阻塞状态的开销都很大，应该尽量避免。在许多应用中，共享数据的锁定状态只会持续很短的一段时间。自旋锁的思想是让一个线程在请求一个共享数据的锁时执行忙循环（自旋）一段时间，如果在这段时间内能获得锁，就可以避免进入阻塞状态。
  - 自旋锁虽然能避免进入阻塞状态从而减少开销，但是它需要进行忙循环操作占用 CPU 时间，它只适用于共享数据的锁定状态很短的场景。
  - 在 JDK 1.6 中引入了自适应的自旋锁。自适应意味着自旋的次数不再固定了，而是由前一次在同一个锁上的自旋次数及锁的拥有者的状态来决定。

- **锁清除：**

  - 锁消除是指对于被检测出不可能存在竞争的共享数据的锁进行消除。

  - 锁消除主要是通过**逃逸分析**来支持，如果堆上的共享数据不可能逃逸出去被其它线程访问到，那么就可以把它们当成私有数据对待，也就可以将它们的锁进行消除。

  - 对于一些看起来没有加锁的代码，其实隐式的加了很多锁。例如下面的字符串拼接代码就隐式加了锁：

    - ```java
      public static String concatString(String s1, String s2, String s3) {
          return s1 + s2 + s3;
      }
      ```

    - String 是一个不可变的类，编译器会对 String 的拼接自动优化。在 JDK 1.5 之前，会转化为 StringBuffer 对象的连续 append() 操作：

    - ```java
      public static String concatString(String s1, String s2, String s3) {
          StringBuffer sb = new StringBuffer();
          sb.append(s1);
          sb.append(s2);
          sb.append(s3);
          return sb.toString();
      }
      ```

    - 每个 append() 方法中都有一个同步块。虚拟机观察变量 sb，很快就会发现它的动态作用域被限制在 concatString()  方法内部。也就是说，sb 的所有引用永远不会逃逸到 concatString() 方法之外，其他线程无法访问到它，因此可以进行消除。

- **锁粗化：**

  - 如果一系列的连续操作都对同一个对象反复加锁和解锁，频繁的加锁操作就会导致性能损耗。
  - 上一节的示例代码中连续的  append()  方法就属于这类情况。如果虚拟机探测到由这样的一串零碎的操作都对同一个对象加锁，将会把加锁的范围扩展（粗化）到整个操作序列的外部。对于上一节的示例代码就是扩展到第一个 append() 操作之前直至最后一个 append() 操作之后，这样只需要加锁一次就可以了。

- **轻量级锁：**

  - JDK 1.6 引入了偏向锁和轻量级锁，从而让锁拥有了四个状态：无锁状态（unlocked）、偏向锁状态（biasble）、轻量级锁状态（lightweight locked）和重量级锁状态（inflated）。
  - 以下是 HotSpot 虚拟机**对象头的内存布局**，这些数据被称为 Mark Word。其中 tag bits 对应了五个状态，这些状态在右侧的 state 表格中给出。除了 marked for gc 状态，其它四个状态已经在前面介绍过了。
  - ![img](1_Java再概览.assets/bb6a49be-00f2-4f27-a0ce-4ed764bc605c.png)
  - 下图左侧是一个线程的虚拟机栈，其中有一部分称为 Lock Record 的区域，这是在轻量级锁运行过程创建的，用于存放锁对象的 Mark Word。而右侧就是一个锁对象，包含了 Mark Word 和其它信息。
    - ![img](1_Java再概览.assets/051e436c-0e46-4c59-8f67-52d89d656182.png)
  - 轻量级锁是相对于传统的重量级锁而言，它使用 CAS 操作来避免重量级锁使用互斥量的开销。对于绝大部分的锁，在整个同步周期内都是不存在竞争的，因此也就不需要都使用互斥量进行同步，可以先采用 CAS 操作进行同步，如果 CAS 失败了再改用互斥量进行同步。
  - 当尝试获取一个锁对象时，如果锁对象标记为 0 01，说明锁对象的锁未锁定（unlocked）状态。此时虚拟机在当前线程的虚拟机栈中创建 Lock Record，然后使用 CAS 操作将对象的 Mark Word 更新为 Lock Record 指针。如果 CAS  操作成功了，那么线程就获取了该对象上的锁，并且对象的 Mark Word 的锁标记变为 00，表示该对象处于轻量级锁状态。
    - ![img](1_Java再概览.assets/baaa681f-7c52-4198-a5ae-303b9386cf47.png)
  - 如果 CAS 操作失败了，虚拟机首先会检查对象的 Mark Word  是否指向当前线程的虚拟机栈，如果是的话说明当前线程已经拥有了这个锁对象，那就可以直接进入同步块继续执行，否则说明这个锁对象已经被其他线程线程抢占了。如果有两条以上的线程争用同一个锁，那轻量级锁就不再有效，要膨胀为重量级锁。

- **偏向锁：**

  - 偏向锁的思想是偏向于让第一个获取锁对象的线程，这个线程在之后获取该锁就不再需要进行同步操作，甚至连 CAS 操作也不再需要。
  - 当锁对象第一次被线程获得的时候，进入偏向状态，标记为 1 01。同时使用 CAS 操作将线程 ID 记录到 Mark Word 中，如果 CAS 操作成功，这个线程以后每次进入这个锁相关的同步块就不需要再进行任何同步操作。
  - 当有另外一个线程去尝试获取这个锁对象时，偏向状态就宣告结束，此时撤销偏向（Revoke Bias）后恢复到未锁定状态或者轻量级锁状态。
  - ![img](1_Java再概览.assets/390c913b-5f31-444f-bbdb-2b88b688e7ce.jpg)

## 3.12 多线程开发的建议

- 给线程起个有意义的名字，这样可以方便找 Bug。
- 缩小同步范围，从而减少锁争用。例如对于 synchronized，应该尽量使用同步块而不是同步方法。
- 多用同步工具少用 wait() 和 notify()。首先，CountDownLatch, CyclicBarrier, Semaphore 和  Exchanger 这些同步类简化了编码操作，而用 wait() 和 notify()  很难实现复杂控制流；其次，这些同步类是由最好的企业编写和维护，在后续的 JDK 中还会不断优化和完善。
- 使用 BlockingQueue 实现生产者消费者问题。
- 多用并发集合少用同步集合，例如应该使用 ConcurrentHashMap 而不是 Hashtable。
- 使用本地变量和不可变类来保证线程安全。
- 使用线程池而不是直接创建线程，这是因为创建线程代价很高，线程池可以有效地利用有限的线程来启动任务。

# 4. Java虚拟机

## 4.1 运行时数据区

- ![img](1_Java再概览.assets/5778d113-8e13-4c53-b5bf-801e58080b97.png)

- **程序计数器：**

  - 记录正在执行的虚拟机字节码指令的地址（如果正在执行的是本地方法则为空）。

- **Java虚拟机栈：**

  - 每个 Java 方法在执行的同时会创建一个栈帧用于存储局部变量表、操作数栈、常量池引用等信息。从方法调用直至执行完成的过程，对应着一个栈帧在 Java 虚拟机栈中入栈和出栈的过程。

  - ![img](1_Java再概览.assets/8442519f-0b4d-48f4-8229-56f984363c69.png)

  - 可以通过 -Xss 这个虚拟机参数来指定每个线程的 Java 虚拟机栈内存大小，在 JDK 1.4 中默认为 256K，而在 JDK 1.5+ 默认为 1M：

  - ```java
    java -Xss2M HackTheJava
    ```

  - 该区域可能抛出以下异常：

    - 当线程请求的栈深度超过最大值，会抛出 StackOverflowError 异常；
    - 栈进行动态扩展时如果无法申请到足够内存，会抛出 OutOfMemoryError 异常。

- **本地方法栈：**

  - 本地方法栈与 Java 虚拟机栈类似，它们之间的区别只不过是本地方法栈为本地方法服务。
  - 本地方法一般是用其它语言（C、C++ 或汇编语言等）编写的，并且被编译为基于本机硬件和操作系统的程序，对待这些方法需要特别处理。
  - ![img](1_Java再概览.assets/66a6899d-c6b0-4a47-8569-9d08f0baf86c.png)

- **堆：**

  - 所有对象都在这里分配内存，是垃圾收集的主要区域（"GC 堆"）。

    现代的垃圾收集器基本都是采用分代收集算法，其主要的思想是针对不同类型的对象采取不同的垃圾回收算法。可以将堆分成两块：

    - 新生代（Young Generation）
    - 老年代（Old Generation）

  - 堆不需要连续内存，并且可以动态增加其内存，增加失败会抛出 OutOfMemoryError 异常。

  - 可以通过 -Xms 和 -Xmx 这两个虚拟机参数来指定一个程序的堆内存大小，第一个参数设置初始值，第二个参数设置最大值。

  - ```java
    java -Xms1M -Xmx2M HackTheJava
    ```

- **方法区：**

  - 用于存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。
  - 和堆一样不需要连续的内存，并且可以动态扩展，动态扩展失败一样会抛出 OutOfMemoryError 异常。
  - 对这块区域进行垃圾回收的主要目标是对常量池的回收和对类的卸载，但是一般比较难实现。
  - HotSpot 虚拟机把它当成永久代来进行垃圾回收。但很难确定永久代的大小，因为它受到很多因素影响，并且每次 Full GC  之后永久代的大小都会改变，所以经常会抛出 OutOfMemoryError 异常。
  - 为了更容易管理方法区，从 JDK 1.8  开始，移除**永久代**，并把方法区移至**元空间**，它位于本地内存中，而不是虚拟机内存中。
  - 方法区是一个 JVM 规范，永久代与元空间都是其一种实现方式。在 JDK 1.8 之后，原来永久代的数据被分到了堆和元空间中。
    - 元空间存储类的元信息
    - 静态变量和常量池等放入堆中。

- **运行时常量池：**

  - 运行时常量池是方法区的一部分。
  - Class 文件中的常量池（编译器生成的字面量和符号引用）会在类加载后被放入这个区域。
  - 除了在编译期生成的常量，还允许动态生成，例如 String 类的 intern()。

- **直接内存：**

  - 在 JDK 1.4 中新引入了 NIO 类，它可以使用 Native 函数库直接分配堆外内存，然后通过 Java 堆里的  DirectByteBuffer 对象作为这块内存的引用进行操作。这样能在一些场景中显著提高性能，因为避免了在堆内存和堆外内存来回拷贝数据。

## 4.2 垃圾收集

- 垃圾收集主要是针对堆和方法区进行。程序计数器、虚拟机栈和本地方法栈这三个区域属于线程私有的，只存在于线程的生命周期内，线程结束之后就会消失，因此不需要对这三个区域进行垃圾回收。

- **判断一个对象是否可以被回收？**

  - 1、**引用计数算法**
    - 为对象添加一个引用计数器，当对象增加一个引用时计数器加 1，引用失效时计数器减 1。引用计数为 0 的对象可被回收。
    - 在两个对象出现循环引用的情况下，此时引用计数器永远不为 0，导致无法对它们进行回收。正是因为循环引用的存在，因此 Java 虚拟机不使用引用计数算法。python使用的就是引用计数法
  - 2、**可达性分析算法**
    - 以 GC Roots 为起始点进行搜索，可达的对象都是存活的，不可达的对象可被回收。
    - Java 虚拟机使用该算法来判断对象是否可被回收，GC Roots 一般包含以下内容：
      - 虚拟机栈中局部变量表中引用的对象
      - 本地方法栈中 JNI 中引用的对象
      - 方法区中类静态属性引用的对象
      - 方法区中的常量引用的对象
    - ![img](1_Java再概览.assets/83d909d2-3858-4fe1-8ff4-16471db0b180.png)

- **方法区的垃圾回收：**

  - 因为方法区主要存放永久代对象，而永久代对象的回收率比新生代低很多，所以在方法区上进行回收性价比不高。
  - 主要是对常量池的回收和对类的卸载。
  - 为了避免内存溢出，在大量使用反射和动态代理的场景都需要虚拟机具备类卸载功能。
  - 类的卸载条件很多，需要满足以下三个条件，并且满足了条件也不一定会被卸载：
    - 该类所有的实例都已经被回收，此时堆中不存在该类的任何实例。
    - 加载该类的 ClassLoader 已经被回收。
    - 该类对应的 Class 对象没有在任何地方被引用，也就无法在任何地方通过反射访问该类方法。

- **finalize()：**

  - 类似 C++ 的析构函数，用于关闭外部资源。但是 try-finally 等方式可以做得更好，并且该方法运行代价很高，不确定性大，无法保证各个对象的调用顺序，因此最好不要使用。
  - 当一个对象可被回收时，如果需要执行该对象的 finalize() 方法，那么就有可能在该方法中让对象重新被引用，从而实现自救。自救只能进行一次，如果回收的对象之前调用了 finalize() 方法自救，后面回收时不会再调用该方法。

- **引用类型：**

  - 无论是通过引用计数算法判断对象的引用数量，还是通过可达性分析算法判断对象是否可达，判定对象是否可被回收都与引用有关。
  - Java 提供了四种强度不同的引用类型。
    - 强引用
      - 被强引用关联的对象不会被回收。
      - 使用 new 一个新对象的方式来创建强引用。
    - 软引用
      - 被软引用关联的对象只有在内存不够的情况下才会被回收。
      - 使用 SoftReference 类来创建软引用。
    - 弱引用
      - 被弱引用关联的对象一定会被回收，也就是说它只能存活到下一次垃圾回收发生之前。
      - 使用 WeakReference 类来创建弱引用。
    - 虚引用
      - 又称为幽灵引用或者幻影引用，一个对象是否有虚引用的存在，不会对其生存时间造成影响，也无法通过虚引用得到一个对象。
      - 为一个对象设置虚引用的唯一目的是能在这个对象被回收时收到一个系统通知。
      - 使用 PhantomReference 来创建虚引用。

- **垃圾收集算法：**

  - **标记清除算法：**

    - ![img](1_Java再概览.assets/005b481b-502b-4e3f-985d-d043c2b330aa.png)
    - 在标记阶段，程序会检查每个对象是否为活动对象，如果是活动对象，则程序会在对象头部打上标记。
    - 在清除阶段，会进行未标记的对象回收并取消标志位，另外，还会判断回收后的分块与前一个空闲分块是否连续，若连续，会合并这两个分块。回收对象就是把对象作为分块，连接到被称为 “空闲链表” 的单向链表，之后进行分配时只需要遍历这个空闲链表，就可以找到分块。
    - 在分配时，程序会搜索空闲链表寻找空间大于等于新对象大小 size 的块 block。如果它找到的块等于 size，会直接返回这个分块；如果找到的块大于 size，会将块分割成大小为 size 与  (block - size) 的两部分，返回大小为 size 的分块，并把大小为 (block - size) 的块返回给空闲链表。
    - 不足：
      - 标记和清除过程效率都不高；
      - 会产生大量不连续的内存碎片，导致无法给大对象分配内存。

  - **标记整理算法：**

    - ![img](1_Java再概览.assets/ccd773a5-ad38-4022-895c-7ac318f31437.png)
    - 让所有存活的对象都向一端移动，然后直接清理掉端边界以外的内存。
    - 优点:
      - 不会产生内存碎片
    - 不足:
      - 需要移动大量对象，处理效率比较低。

  - **复制算法：**

    - ![img](1_Java再概览.assets/b2b77b9e-958c-4016-8ae5-9c6edd83871e.png)
    - 将内存划分为大小相等的两块，每次只使用其中一块，当这一块内存用完了就将还存活的对象复制到另一块上面，然后再把使用过的内存空间进行一次清理。
    - 主要不足是只使用了内存的一半。
    - 现在的商业虚拟机都采用这种收集算法回收新生代，但是并不是划分为大小相等的两块，而是一块较大的 Eden 空间和两块较小的 Survivor 空间，每次使用 Eden 和其中一块 Survivor。在回收时，将 Eden 和  Survivor 中还存活着的对象全部复制到另一块 Survivor 上，最后清理 Eden 和使用过的那一块 Survivor。
    - HotSpot 虚拟机的 Eden 和 Survivor 大小比例默认为 8:1，保证了内存的利用率达到 90%。如果每次回收有多于 10%  的对象存活，那么一块 Survivor 就不够用了，此时需要依赖于老年代进行空间分配担保，也就是借用老年代的空间存储放不下的对象。

  - **分代收集算法：**

    - 现在的商业虚拟机采用分代收集算法，它根据对象存活周期将内存划分为几块，不同块采用适当的收集算法。

      一般将堆分为新生代和老年代。

      - 新生代使用：复制算法
      - 老年代使用：标记 - 清除 或者 标记 - 整理 算法

  - **增量收集算法：**

    - 基本思想：
      - 如果一次性将所有的垃圾进行处理，需要造成系统长时间的停顿，那么就可以让垃圾收集线程和应用程序线程交替执行。每次，垃圾收集线程只收集一小片区域的内存空间，接着切换到应用程序线程。依次反复，直到垃圾收集完成。
      - 总的来说，增量收集算法的基础仍是传统的标记-清除和复制算法。增量收集算法通过**对线程间（用户线程和垃圾收集线程）冲突的妥善处理，允许垃圾收集线程以分阶段的方式完成标记、清理或复制工作。**
    - 缺点：
      - 使用这种方式，由于在垃圾回收过程中，间断性地还执行了应用程序代码，所以能减少系统的停顿时间。但是，因为线程切换和上下文转换的消耗，会使得垃圾回收的总体成本上升，**造成系统吞吐量的下降**。

- **垃圾收集器：**

  - ![img](1_Java再概览.assets/c625baa0-dde6-449e-93df-c3a67f2f430f.jpg)
  - 以上是 HotSpot 虚拟机中的 7 个垃圾收集器，连线表示垃圾收集器可以配合使用。
    - 单线程与多线程：单线程指的是垃圾收集器只使用一个线程，而多线程使用多个线程；
    - 串行与并行、并发：
      - 串行指的是垃圾收集器与用户程序交替执行，这意味着在执行垃圾收集的时候需要停顿用户程序；
      - 并行指的是多条垃圾收集线程并行工作，但此时用户线程仍处于等待状态。
      - 并发指的是垃圾收集器和用户程序同时执行。除了 CMS 和 G1 之外，其它垃圾收集器都是以串行的方式执行。
  - **Serial收集器：**
    - ![img](1_Java再概览.assets/22fda4ae-4dd5-489d-ab10-9ebfdad22ae0.jpg)
    - Serial 翻译为串行，也就是说它以串行的方式执行。
    - 它是单线程的收集器，只会使用一个线程进行垃圾收集工作。
    - 它的优点是简单高效，在单个 CPU 环境下，由于没有线程交互的开销，因此拥有最高的单线程收集效率。
    - 它是 Client 场景下的默认新生代收集器，因为在该场景下内存一般来说不会很大。它收集一两百兆垃圾的停顿时间可以控制在一百多毫秒以内，只要不是太频繁，这点停顿时间是可以接受的。
  - **ParNew收集器：**
    - ![img](1_Java再概览.assets/81538cd5-1bcf-4e31-86e5-e198df1e013b.jpg)
    - 它是 Serial 收集器的多线程版本。
    - 它是 Server 场景下默认的新生代收集器，除了性能原因外，主要是因为除了 Serial 收集器，只有它能与 CMS 收集器配合使用。
  - **Parallel Scavenge收集器：**
    - HotSpot的年轻代中除了拥有ParNew收集器是基于并行回收的以外，Parallel scavenge收集器同样也采用了复制算法、并行回收和"stopthe world"机制。
    - 那么Parallel收集器的出现是否多此一举?
      - 和ParNew收集器不同，Parallel scavenge收集器的目标则是达到
        一个可控制的吞吐量(Throughput)，它也被称为吞吐量优先的垃圾收集器。
      - 自适应调节策略也是Parallel Scavenge与ParNew一个重要区别。
    - 其它收集器目标是尽可能缩短垃圾收集时用户线程的停顿时间，而它的目标是达到一个可控制的吞吐量，因此它被称为“吞吐量优先”收集器。这里的吞吐量指 CPU 用于运行用户程序的时间占总时间的比值。
    - 停顿时间越短就越适合需要与用户交互的程序，良好的响应速度能提升用户体验。而高吞吐量则可以高效率地利用 CPU 时间，尽快完成程序的运算任务，适合在后台运算而不需要太多交互的任务。
    - 缩短停顿时间是以牺牲吞吐量和新生代空间来换取的：新生代空间变小，垃圾回收变得频繁，导致吞吐量下降。
  - **Serial Old收集器：**
    - 是 Serial 收集器的老年代版本，也是给 Client 场景下的虚拟机使用。如果用在 Server 场景下，它有两大用途：
      - 在 JDK 1.5 以及之前版本（Parallel Old 诞生以前）中与 Parallel Scavenge 收集器搭配使用。
      - 作为 CMS 收集器的后备预案，在并发收集发生 Concurrent Mode Failure 时使用。
  - **Parallel Old收集器：**
    - 是 Parallel Scavenge 收集器的老年代版本。
    - 在注重吞吐量以及 CPU 资源敏感的场合，都可以优先考虑 Parallel Scavenge 加 Parallel Old 收集器。
  - **CMS收集器：**
    - ![img](1_Java再概览.assets/62e77997-6957-4b68-8d12-bfd609bb2c68.jpg)
    - CMS（Concurrent Mark Sweep），Mark Sweep 指的是标记 - 清除算法。
    - 分为以下四个流程：
      - 初始标记：仅仅只是标记一下 GC Roots 能直接关联到的对象，速度很快，需要停顿。
      - 并发标记：进行 GC Roots Tracing 的过程，它在整个回收过程中耗时最长，不需要停顿。
      - 重新标记：为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，需要停顿。
      - 并发清除：不需要停顿。
    - 在整个过程中耗时最长的并发标记和并发清除过程中，收集器线程都可以与用户线程一起工作，不需要进行停顿。
    - 具有以下缺点：
      - 吞吐量低：低停顿时间是以牺牲吞吐量为代价的，导致 CPU 利用率不够高。
      - 无法处理浮动垃圾，可能出现 Concurrent Mode Failure。浮动垃圾是指并发清除阶段由于用户线程继续运行而产生的垃圾，这部分垃圾只能到下一次 GC  时才能进行回收。由于浮动垃圾的存在，因此需要预留出一部分内存，意味着 CMS  收集不能像其它收集器那样等待老年代快满的时候再回收。如果预留的内存不够存放浮动垃圾，就会出现 Concurrent Mode  Failure，这时虚拟机将临时启用 Serial Old 来替代 CMS。
      - 标记 - 清除算法导致的空间碎片，往往出现老年代空间剩余，但无法找到足够大连续空间来分配当前对象，不得不提前触发一次 Full GC
  - **G1收集器：**
    - G1（Garbage-First），它是一款面向服务端应用的垃圾收集器，在多 CPU 和大内存的场景下有很好的性能。HotSpot 开发团队赋予它的使命是JDK1.7已经替换掉 CMS 收集器。
    - 堆被分为新生代和老年代，其它收集器进行收集的范围都是整个新生代或者老年代，而 G1 可以直接对新生代和老年代一起回收。
    - G1 把堆划分成多个大小相等的独立区域（Region），新生代和老年代不再物理隔离。
    - ![img](1_Java再概览.assets/9bbddeeb-e939-41f0-8e8e-2b1a0aa7e0a7.png)
    - 通过引入 Region  的概念，从而将原来的一整块内存空间划分成多个的小空间，使得每个小空间可以单独进行垃圾回收。这种划分方法带来了很大的灵活性，使得可预测的停顿时间模型成为可能。通过记录每个 Region  垃圾回收时间以及回收所获得的空间（这两个值是通过过去回收的经验获得），并维护一个优先列表，每次根据允许的收集时间，优先回收价值最大的  Region。
    - 每个 Region 都有一个 Remembered Set，用来记录该 Region 对象的引用对象所在的 Region。通过使用 Remembered Set，在做可达性分析的时候就可以避免全堆扫描。
    - ![img](1_Java再概览.assets/f99ee771-c56f-47fb-9148-c0036695b5fe.jpg)
    - 如果不计算维护 Remembered Set 的操作，G1 收集器的运作大致可划分为以下几个步骤：
      - 初始标记
      - 并发标记
      - 最终标记：为了修正在并发标记期间因用户程序继续运作而导致标记产生变动的那一部分标记记录，虚拟机将这段时间对象变化记录在线程的 Remembered Set Logs 里面，最终标记阶段需要把 Remembered Set Logs 的数据合并到 Remembered  Set 中。这阶段需要停顿线程，但是可并行执行。
      - 筛选回收：首先对各个 Region 中的回收价值和成本进行排序，根据用户所期望的 GC 停顿时间来制定回收计划。此阶段其实也可以做到与用户程序一起并发执行，但是因为只回收一部分 Region，时间是用户可控制的，而且停顿用户线程将大幅度提高收集效率。
    - 具备如下特点：
      - 空间整合：整体来看是基于“标记 - 整理”算法实现的收集器，从局部（两个 Region 之间）上来看是基于“复制”算法实现的，这意味着运行期间不会产生内存空间碎片。
      - 可预测的停顿：能让使用者明确指定在一个长度为 M 毫秒的时间片段内，消耗在 GC 上的时间不得超过 N 毫秒。

- **如何选择垃圾收集器？**

  - 截止JDK 1.8，-共有7款不同的垃圾收集器。每一-款不同的垃圾收集器都有不同的特点，在具体使用的时候，需要根据具体的情况选用不同的垃圾收集器。
  - ![image-20211221195008995](1_Java再概览.assets/image-20211221195008995.png)

## 4.3 内存分配和回收策略

- **Minor GC、Major GC、Full GC：**
  - Minor GC：回收新生代，因为新生代对象存活时间很短，因此 Minor GC 会频繁执行，执行的速度一般也会比较快。
  - 老年代收集(Major GC/Old GC)：只是老年代的垃圾收集
    - 目前只有CMS GC(并发的垃圾回收器)会有单独收集老年代的行为，大部分时候Major GC会和Full GC混淆使用，需要具体分辨是老年代回收回收整堆回收
  - Full GC：收集整个Java堆和方法区的垃圾收集
- **内存分配策略：**
  - 对象优先在 Eden 分配：大多数情况下，对象在新生代 Eden 上分配，当 Eden 空间不够时，发起 Minor GC。
  - 大对象直接进入老年代：大对象是指需要连续内存空间的对象，最典型的大对象是那种很长的字符串以及数组。
    - 经常出现大对象会提前触发垃圾收集以获取足够的连续空间分配给大对象。
    - -XX:PretenureSizeThreshold，大于此值的对象直接在老年代分配，避免在 Eden 和 Survivor 之间的大量内存复制。
  - 长期存活的对象进入老年代：为对象定义年龄计数器，对象在 Eden 出生并经过 Minor GC 依然存活，将移动到 Survivor 中，年龄就增加 1 岁，增加到一定年龄则移动到老年代中。
    - -XX:MaxTenuringThreshold 用来定义年龄的阈值。
  - 动态对象年龄判定：虚拟机并不是永远要求对象的年龄必须达到  MaxTenuringThreshold 才能晋升老年代，如果在 Survivor 中相同年龄所有对象大小的总和大于 Survivor  空间的一半，则年龄大于或等于该年龄的对象可以直接进入老年代，无需等到 MaxTenuringThreshold 中要求的年龄。
  - 空间分配担保：在发生 Minor GC 之前，虚拟机先检查老年代最大可用的连续空间是否大于新生代所有对象总空间，如果条件成立的话，那么 Minor GC 可以确认是安全的。
    - 如果不成立的话虚拟机会查看 HandlePromotionFailure  的值是否允许担保失败，如果允许那么就会继续检查老年代最大可用的连续空间是否大于历次晋升到老年代对象的平均大小，如果大于，将尝试着进行一次  Minor GC；如果小于，或者 HandlePromotionFailure 的值不允许冒险，那么就要进行一次 Full GC。
- **Full GC的触发条件：**
  - 调用 System.gc()：只是建议虚拟机执行 Full GC，但是虚拟机不一定真正去执行。不建议使用这种方式，而是让虚拟机管理内存。
  - 老年代空间不足：老年代空间不足的常见场景为前文所讲的大对象直接进入老年代、长期存活的对象进入老年代等。
  - 空间分配担保失败：使用复制算法的 Minor GC 需要老年代的内存空间作担保，如果担保失败会执行一次 Full GC。
  - JDK 1.7 及以前的永久代空间不足：
    - 在 JDK 1.7 及以前，HotSpot 虚拟机中的方法区是用永久代实现的，永久代中存放的为一些 Class 的信息、常量、静态变量等数据。
    - 当系统中要加载的类、反射的类和调用的方法较多时，永久代可能会被占满，在未配置为采用 CMS GC 的情况下也会执行 Full GC。如果经过 Full GC 仍然回收不了，那么虚拟机会抛出 java.lang.OutOfMemoryError。
    - 为避免以上原因引起的 Full GC，可采用的方法为增大永久代空间或转为使用 CMS GC。
  - Concurrent Mode Failure
    - 执行 CMS GC 的过程中同时有对象要放入老年代，而此时老年代空间不足（可能是 GC 过程中浮动垃圾过多导致暂时性的空间不足），便会报 Concurrent Mode Failure 错误，并触发 Full GC。

## 4.4 类加载机制

- 类是在运行期间第一次使用时动态加载的，而不是一次性加载所有类。因为如果一次性加载，那么会占用很多的内存。
- **类的生命周期：**
  - ![image-20211222084546095](1_Java再概览.assets/image-20211222084546095.png)
  - **加载（Loading）**
  - **链接(Linking)：**
    - **验证（Verification）**
    - **准备（Preparation）**
    - **解析（Resolution）**
  - **初始化（Initialization）**
  - 使用（Using）
  - 卸载（Unloading）

- **类加载过程：**

  - 加载：
    - 加载过程完成以下三件事：
      - 通过类的完全限定名称获取定义该类的二进制字节流。
      - 将该字节流表示的静态存储结构转换为方法区的运行时存储结构。
      - 在内存中生成一个代表该类的 Class 对象，作为方法区中该类各种数据的访问入口。
    - 其中二进制字节流可以从以下方式中获取：
      - 从 ZIP 包读取，成为 JAR、EAR、WAR 格式的基础。
      - 从网络中获取，最典型的应用是 Applet。
      - 运行时计算生成，例如动态代理技术，在 java.lang.reflect.Proxy 使用 ProxyGenerator.generateProxyClass 的代理类的二进制字节流。
      - 由其他文件生成，例如由 JSP 文件生成对应的 Class 类。
  - **链接：**
    - 验证：确保 Class 文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。
    - 准备：
      - 为类变量(类里面的变量)分配内存并且设置默认初始值(0 or null)
      - final修饰的变量在编译的时候就会分配，因此在准备阶段会显示初始化(已经初始化后，后续直接使用即可)
      - 不会为实例变量(new 类())分配初始化
        - 因为类变量是分配在方法区中，而实例变量会随着对象一起分配到java堆中
    - 解析：
      - 将常量池的符号引用替换为直接引用的过程。
      - 其中解析过程在某些情况下可以在初始化阶段之后再开始，这是为了支持 Java 的动态绑定。
  - **初始化：**
    - 初始化阶段才真正开始执行类中定义的 Java 程序代码。初始化阶段是虚拟机执行类构造器 <clinit\>()  方法的过程。在准备阶段，类变量已经赋过一次系统要求的初始值，而在初始化阶段，根据程序员通过程序制定的主观计划去初始化类变量和其它资源。
    - <clinit>()  是由编译器自动收集类中所有类变量的赋值动作和静态语句块中的语句合并产生的，编译器收集的顺序由语句在源文件中出现的顺序决定。特别注意的是，静态语句块只能访问到定义在它之前的类变量，定义在它之后的类变量只能赋值，不能访问。
    - 虚拟机会保证一个类的 <clinit>()  方法在多线程环境下被正确的加锁和同步，如果多个线程同时初始化一个类，只会有一个线程执行这个类的 <clinit>()  方法，其它线程都会阻塞等待，直到活动线程执行 <clinit>() 方法完毕。如果在一个类的 <clinit>()  方法中有耗时的操作，就可能造成多个线程阻塞，在实际过程中此种阻塞很隐蔽。

- **类初始化时机：**

  - 主动引用：
    - 虚拟机规范中并没有强制约束何时进行加载，但是规范严格规定了有且只有下列五种情况必须对类进行初始化（加载、验证、准备都会随之发生）：
      - 遇到 new、getstatic、putstatic、invokestatic  这四条字节码指令时，如果类没有进行过初始化，则必须先触发其初始化。最常见的生成这 4 条指令的场景是：使用 new  关键字实例化对象的时候；读取或设置一个类的静态字段（被 final  修饰、已在编译期把结果放入常量池的静态字段除外）的时候；以及调用一个类的静态方法的时候。
      - 使用 java.lang.reflect 包的方法对类进行反射调用的时候，如果类没有进行初始化，则需要先触发其初始化。
      - 当初始化一个类的时候，如果发现其父类还没有进行过初始化，则需要先触发其父类的初始化。
      - 当虚拟机启动时，用户需要指定一个要执行的主类（包含 main() 方法的那个类），虚拟机会先初始化这个主类；
      - 当使用 JDK 1.7 的动态语言支持时，如果一个 java.lang.invoke.MethodHandle 实例最后的解析结果为  REF_getStatic, REF_putStatic, REF_invokeStatic  的方法句柄，并且这个方法句柄所对应的类没有进行过初始化，则需要先触发其初始化；
  - 被动引用：
    - 以上 5 种场景中的行为称为对一个类进行主动引用。除此之外，所有引用类的方式都不会触发初始化，称为被动引用。
      - 通过子类引用父类的静态字段，不会导致子类初始化。
      - 通过数组定义来引用类，不会触发此类的初始化。该过程会对数组类进行初始化，数组类是一个由虚拟机自动生成的、直接继承自 Object 的子类，其中包含了数组的属性和方法。
      - 常量在编译阶段会存入调用类的常量池中，本质上并没有直接引用到定义常量的类，因此不会触发定义常量的类的初始化。

- **类与类加载器：**

  - 两个类相等，需要类本身相等，并且使用同一个类加载器进行加载。这是因为每一个类加载器都拥有一个独立的类名称空间。
  - 这里的相等，包括类的 Class 对象的 equals() 方法、isAssignableFrom() 方法、isInstance() 方法的返回结果为 true，也包括使用 instanceof 关键字做对象所属关系判定结果为 true。

- **类加载器分类：**

  - 从 Java 虚拟机的角度来讲，只存在以下两种不同的类加载器：
    - 启动类加载器（Bootstrap ClassLoader），使用 C++ 实现，是虚拟机自身的一部分；
    - 所有其它类的加载器，使用 Java 实现，独立于虚拟机，继承自抽象类 java.lang.ClassLoader。
  - 从 Java 开发人员的角度看，类加载器可以划分得更细致一些：
    - 启动类加载器（Bootstrap  ClassLoader）此类加载器负责将存放在 <JRE_HOME>\lib 目录中的，或者被 -Xbootclasspath  参数所指定的路径中的，并且是虚拟机识别的（仅按照文件名识别，如 rt.jar，名字不符合的类库即使放在 lib  目录中也不会被加载）类库加载到虚拟机内存中。启动类加载器无法被 Java  程序直接引用，用户在编写自定义类加载器时，如果需要把加载请求委派给启动类加载器，直接使用 null 代替即可。
    - 扩展类加载器（Extension ClassLoader）这个类加载器是由  ExtClassLoader（sun.misc.Launcher$ExtClassLoader）实现的。它负责将  <JAVA_HOME>/lib/ext 或者被 java.ext.dir  系统变量所指定路径中的所有类库加载到内存中，开发者可以直接使用扩展类加载器。
    - 应用程序类加载器（Application ClassLoader）这个类加载器是由  AppClassLoader（sun.misc.Launcher$AppClassLoader）实现的。由于这个类加载器是  ClassLoader 中的 getSystemClassLoader()  方法的返回值，因此一般称为系统类加载器。它负责加载用户类路径（ClassPath）上所指定的类库，开发者可以直接使用这个类加载器，如果应用程序中没有自定义过自己的类加载器，一般情况下这个就是程序中默认的类加载器。

- **双亲委派模型：**

  - 应用程序是由三种类加载器互相配合从而实现类加载，除此之外还可以加入自己定义的类加载器。

  - 下图展示了类加载器之间的层次关系，称为双亲委派模型（Parents Delegation  Model）。该模型要求除了顶层的启动类加载器外，其它的类加载器都要有自己的父类加载器。这里的父子关系一般通过组合关系（Composition）来实现，而不是继承关系（Inheritance）。

  - ![img](1_Java再概览.assets/0dd2d40a-5b2b-4d45-b176-e75a4cd4bdbf.png)

  - 工作过程：

    - 一个类加载器首先将类加载请求转发到父类加载器，只有当父类加载器无法完成时才尝试自己加载。

  - 优点：

    - 避免类的重复加载
    - 保护程序安全，防止核心API被随意篡改

  - 具体实现：

    - 先检查类是否已经加载过，如果没有则让父类加载器去加载。当父类加载器加载失败时抛出 ClassNotFoundException，此时尝试自己去加载。

    - ```java
      public abstract class ClassLoader {
          // The parent class loader for delegation
          private final ClassLoader parent;
      
          public Class<?> loadClass(String name) throws ClassNotFoundException {
              return loadClass(name, false);
          }
      
          protected Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException {
              synchronized (getClassLoadingLock(name)) {
                  // First, check if the class has already been loaded
                  Class<?> c = findLoadedClass(name);
                  if (c == null) {
                      try {
                          if (parent != null) {
                              c = parent.loadClass(name, false);
                          } else {
                              c = findBootstrapClassOrNull(name);
                          }
                      } catch (ClassNotFoundException e) {
                          // ClassNotFoundException thrown if class not found
                          // from the non-null parent class loader
                      }
      
                      if (c == null) {
                          // If still not found, then invoke findClass in order
                          // to find the class.
                          c = findClass(name);
                      }
                  }
                  if (resolve) {
                      resolveClass(c);
                  }
                  return c;
              }
          }
      
          protected Class<?> findClass(String name) throws ClassNotFoundException {
              throw new ClassNotFoundException(name);
          }
      }
      ```

- **自定义类加载器：**

  - 以下代码中的 FileSystemClassLoader 是自定义类加载器，继承自  java.lang.ClassLoader，用于加载文件系统上的类。它首先根据类的全名在文件系统上查找类的字节代码文件（.class  文件），然后读取该文件内容，最后通过 defineClass() 方法来把这些字节代码转换成 java.lang.Class 类的实例。

  - java.lang.ClassLoader 的 loadClass() 实现了双亲委派模型的逻辑，自定义类加载器一般不去重写它，但是需要重写 findClass() 方法。

  - ```java
    public class FileSystemClassLoader extends ClassLoader {
    
        private String rootDir;
    
        public FileSystemClassLoader(String rootDir) {
            this.rootDir = rootDir;
        }
    
        protected Class<?> findClass(String name) throws ClassNotFoundException {
            byte[] classData = getClassData(name);
            if (classData == null) {
                throw new ClassNotFoundException();
            } else {
                return defineClass(name, classData, 0, classData.length);
            }
        }
    
        private byte[] getClassData(String className) {
            String path = classNameToPath(className);
            try {
                InputStream ins = new FileInputStream(path);
                ByteArrayOutputStream baos = new ByteArrayOutputStream();
                int bufferSize = 4096;
                byte[] buffer = new byte[bufferSize];
                int bytesNumRead;
                while ((bytesNumRead = ins.read(buffer)) != -1) {
                    baos.write(buffer, 0, bytesNumRead);
                }
                return baos.toByteArray();
            } catch (IOException e) {
                e.printStackTrace();
            }
            return null;
        }
    
        private String classNameToPath(String className) {
            return rootDir + File.separatorChar
                    + className.replace('.', File.separatorChar) + ".class";
        }
    }
    ```

# 5. JavaIO

- Java 的 I/O 大概可以分成以下几类：
  - 磁盘操作：File
  - 字节操作：InputStream 和 OutputStream
  - 字符操作：Reader 和 Writer
  - 对象操作：Serializable
  - 网络操作：Socket
  - 新的输入/输出：NIO

## 5.1 磁盘操作

- File 类可以用于表示文件和目录的信息，但是它不表示文件的内容。

- 从 Java7 开始，可以使用 Paths 和 Files 代替 File。

- 递归地列出一个目录下所有文件：

  - ```java
    public static void listAllFiles(File dir) {
        if (dir == null || !dir.exists()) {
            return;
        }
        if (dir.isFile()) {
            System.out.println(dir.getName());
            return;
        }
        for (File file : dir.listFiles()) {
            listAllFiles(file);
        }
    }
    ```

## 5.2 字节操作

- **实现文件复制：**

  - ```java
    public static void copyFile(String src, String dist) throws IOException {
        FileInputStream in = new FileInputStream(src);
        FileOutputStream out = new FileOutputStream(dist);
    
        byte[] buffer = new byte[20 * 1024];
        int cnt;
    
        // read() 最多读取 buffer.length 个字节
        // 返回的是实际读取的个数
        // 返回 -1 的时候表示读到 eof，即文件尾
        while ((cnt = in.read(buffer, 0, buffer.length)) != -1) {
            out.write(buffer, 0, cnt);
        }
    
        in.close();
        out.close();
    }
    ```

- **装饰者模式：**

  - Java I/O 使用了装饰者模式来实现。以 InputStream 为例，
    - InputStream 是抽象组件；
    - FileInputStream 是 InputStream 的子类，属于具体组件，提供了字节流的输入操作；
    - FilterInputStream 属于抽象装饰者，装饰者用于装饰组件，为组件提供额外的功能。例如 BufferedInputStream 为 FileInputStream 提供缓存的功能。
    - ![img](1_Java再概览.assets/9709694b-db05-4cce-8d2f-1c8b09f4d921.png)

## 5.3 字符操作

- **编码与解码：**

  - 编码就是把字符转换为字节，而解码是把字节重新组合成字符。

    如果编码和解码过程使用不同的编码方式那么就出现了乱码。

    - GBK 编码中，中文字符占 2 个字节，英文字符占 1 个字节；
    - UTF-8 编码中，中文字符占 3 个字节，英文字符占 1 个字节；
    - UTF-16be 编码中，中文字符和英文字符都占 2 个字节。

  - UTF-16be 中的 be 指的是 Big Endian，也就是大端。相应地也有 UTF-16le，le 指的是 Little Endian，也就是小端。

  - Java 的内存编码使用双字节编码 UTF-16be，这不是指 Java 只支持这一种编码方式，而是说 char 这种类型使用 UTF-16be  进行编码。char 类型占 16 位，也就是两个字节，Java 使用这种双字节编码是为了让一个中文或者一个英文都能使用一个 char 来存储。

- **String的编码方式：**

  - String 可以看成一个字符序列，可以指定一个编码方式将它编码为字节序列，也可以指定一个编码方式将一个字节序列解码为 String。

  - ```java
    String str1 = "中文";
    byte[] bytes = str1.getBytes("UTF-8");
    String str2 = new String(bytes, "UTF-8");
    System.out.println(str2);
    ```

  - 在调用无参数 getBytes() 方法时，默认的编码方式不是 UTF-16be。双字节编码的好处是可以使用一个 char 存储中文和英文，而将 String 转为 bytes[] 字节数组就不再需要这个好处，因此也就不再需要双字节编码。getBytes()  的默认编码方式与平台有关，一般为 UTF-8。

- **Reader和Writer：**

  - 不管是磁盘还是网络传输，最小的存储单元都是字节，而不是字符。但是在程序中操作的通常是字符形式的数据，因此需要提供对字符进行操作的方法。

    - InputStreamReader 实现从字节流解码成字符流；
    - OutputStreamWriter 实现字符流编码成为字节流。

  - 实现逐行输出文本文件的内容

    - ```java
      public static void readFileContent(String filePath) throws IOException {
      
          FileReader fileReader = new FileReader(filePath);
          BufferedReader bufferedReader = new BufferedReader(fileReader);
      
          String line;
          while ((line = bufferedReader.readLine()) != null) {
              System.out.println(line);
          }
      
          // 装饰者模式使得 BufferedReader 组合了一个 Reader 对象
          // 在调用 BufferedReader 的 close() 方法时会去调用 Reader 的 close() 方法
          // 因此只要一个 close() 调用即可
          bufferedReader.close();
      }
      ```

## 5.4 对象操作

- **序列化：**

  - 序列化就是将一个对象转换成字节序列，方便存储和传输。
    - 序列化：ObjectOutputStream.writeObject()
    - 反序列化：ObjectInputStream.readObject()
    - 不会对静态变量进行序列化，因为序列化只是保存对象的状态，静态变量属于类的状态。

- **Serializable：**

  - 序列化的类需要实现 Serializable 接口，它只是一个标准，没有任何方法需要实现，但是如果不去实现它的话而进行序列化，会抛出异常。

  - ```java
    public static void main(String[] args) throws IOException, ClassNotFoundException {
    
        A a1 = new A(123, "abc");
        String objectFile = "file/a1";
    
        ObjectOutputStream objectOutputStream = new ObjectOutputStream(new FileOutputStream(objectFile));
        objectOutputStream.writeObject(a1);
        objectOutputStream.close();
    
        ObjectInputStream objectInputStream = new ObjectInputStream(new FileInputStream(objectFile));
        A a2 = (A) objectInputStream.readObject();
        objectInputStream.close();
        System.out.println(a2);
    }
    
    private static class A implements Serializable {
    
        private int x;
        private String y;
    
        A(int x, String y) {
            this.x = x;
            this.y = y;
        }
    
        @Override
        public String toString() {
            return "x = " + x + "  " + "y = " + y;
        }
    }
    ```

- **transient：**

  - transient 关键字可以使一些属性不会被序列化。
  - ArrayList 中存储数据的数组 elementData 是用 transient 修饰的，因为这个数组是动态扩展的，并不是所有的空间都被使用，因此就不需要所有的内容都被序列化。通过重写序列化和反序列化方法，使得可以只序列化数组中有内容的那部分数据。

## 5.5 网络操作

- Java 中的网络支持：

  - InetAddress：用于表示网络上的硬件资源，即 IP 地址；
  - URL：统一资源定位符；
  - Sockets：使用 TCP 协议实现网络通信；
  - Datagram：使用 UDP 协议实现网络通信。

- **InetAddress：**

  - 没有公有的构造函数，只能通过静态方法来创建实例。

  - ```java
    InetAddress.getByName(String host);
    InetAddress.getByAddress(byte[] address);
    ```

- **URL：**

  - 可以直接从 URL 中读取字节流数据。

  - ```java
    public static void main(String[] args) throws IOException {
    
        URL url = new URL("http://www.baidu.com");
    
        /* 字节流 */
        InputStream is = url.openStream();
    
        /* 字符流 */
        InputStreamReader isr = new InputStreamReader(is, "utf-8");
    
        /* 提供缓存功能 */
        BufferedReader br = new BufferedReader(isr);
    
        String line;
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }
    
        br.close();
    }
    ```

- **Sockets：**

  - ServerSocket：服务器端类
  - Socket：客户端类
  - 服务器和客户端通过 InputStream 和 OutputStream 进行输入输出。
  - ![img](1_Java再概览.assets/1e6affc4-18e5-4596-96ef-fb84c63bf88a.png)

- **Datagram：**

  - DatagramSocket：通信类
  - DatagramPacket：数据包类

## 5.6 NIO

- 新的输入/输出 (NIO) 库是在 JDK 1.4 中引入的，弥补了原来的 I/O 的不足，提供了高速的、面向块的 I/O。

- **流与块：**

  - I/O 与 NIO 最重要的区别是数据打包和传输的方式，I/O 以流的方式处理数据，而 NIO 以块的方式处理数据。
  - 面向流的 I/O 一次处理一个字节数据：一个输入流产生一个字节数据，一个输出流消费一个字节数据。为流式数据创建过滤器非常容易，链接几个过滤器，以便每个过滤器只负责复杂处理机制的一部分。不利的一面是，面向流的 I/O 通常相当慢。
  - 面向块的 I/O 一次处理一个数据块，按块处理数据比按流处理数据要快得多。但是面向块的 I/O 缺少一些面向流的 I/O 所具有的优雅性和简单性。
  - I/O 包和 NIO 已经很好地集成了，java.io.* 已经以 NIO 为基础重新实现了，所以现在它可以利用 NIO 的一些特性。例如，java.io.* 包中的一些类包含以块的形式读写数据的方法，这使得即使在面向流的系统中，处理速度也会更快。

- **通道与缓冲区：**

  - 通道：
    - 通道 Channel 是对原 I/O 包中的流的模拟，可以通过它读取和写入数据。
    - 通道与流的不同之处在于，流只能在一个方向上移动(一个流必须是 InputStream 或者 OutputStream 的子类)，而通道是双向的，可以用于读、写或者同时用于读写。
    - 通道包括以下类型：
      - FileChannel：从文件中读写数据；
      - DatagramChannel：通过 UDP 读写网络中数据；
      - SocketChannel：通过 TCP 读写网络中数据；
      - ServerSocketChannel：可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个 SocketChannel。
  - 缓冲区：
    - 发送给一个通道的所有数据都必须首先放到缓冲区中，同样地，从通道中读取的任何数据都要先读到缓冲区中。也就是说，不会直接对通道进行读写数据，而是要先经过缓冲区。
    - 缓冲区实质上是一个数组，但它不仅仅是一个数组。缓冲区提供了对数据的结构化访问，而且还可以跟踪系统的读/写进程。
    - 缓冲区包括以下类型：
      - ByteBuffer
      - CharBuffer
      - ShortBuffer
      - IntBuffer
      - LongBuffer
      - FloatBuffer
      - DoubleBuffer

- **缓冲区状态变量：**

  - **容量 (capacity)** ：作为一个内存块，Buffer具有一定的固定大小，也称为"容量"，缓冲区容量不能为负，并且创建后不能更改。 
  - **限制 (limit)**：表示缓冲区中可以操作数据的大小（limit 后数据不能进行读写）。缓冲区的限制不能为负，并且不能大于其容量。 **写入模式，限制等于buffer的容量。读取模式下，limit等于写入的数据量**。
  - **位置 (position)**：下一个要读取或写入的数据的索引。缓冲区的位置不能为 负，并且不能大于其限制 
  - **标记 (mark)与重置 (reset)**：标记是一个索引，通过 Buffer 中的 mark() 方法 指定 Buffer 中一个特定的 position，之后可以通过调用 reset() 方法恢复到这 个 position.
  - **标记、位置、限制、容量遵守以下不变式： 0 <= mark <= position <= limit <= capacity**

- **选择器：**

  - NIO 常常被叫做非阻塞 IO，主要是因为 NIO 在网络通信中的非阻塞特性被广泛使用。
  - NIO 实现了 IO 多路复用中的 Reactor 模型，一个线程 Thread 使用一个选择器 Selector 通过轮询的方式去监听多个通道 Channel 上的事件，从而让一个线程就可以处理多个事件。
  - 通过配置监听的通道 Channel 为非阻塞，那么当 Channel 上的 IO 事件还未到达时，就不会进入阻塞状态一直等待，而是继续轮询其它 Channel，找到 IO 事件已经到达的 Channel 执行。
  - 因为创建和切换线程的开销很大，因此使用一个线程来处理多个事件而不是一个线程处理一个事件，对于 IO 密集型的应用具有很好地性能。
  - 应该注意的是，只有套接字 Channel 才能配置为非阻塞，而 FileChannel 不能，为 FileChannel 配置非阻塞也没有意义。
  - ![img](1_Java再概览.assets/093f9e57-429c-413a-83ee-c689ba596cef.png)

- **NIO实例：**

  - ```java
    public class NIOServer {
    
        public static void main(String[] args) throws IOException {
    
            Selector selector = Selector.open();
    
            ServerSocketChannel ssChannel = ServerSocketChannel.open();
            ssChannel.configureBlocking(false);
            ssChannel.register(selector, SelectionKey.OP_ACCEPT);
    
            ServerSocket serverSocket = ssChannel.socket();
            InetSocketAddress address = new InetSocketAddress("127.0.0.1", 8888);
            serverSocket.bind(address);
    
            while (true) {
    
                selector.select();
                Set<SelectionKey> keys = selector.selectedKeys();
                Iterator<SelectionKey> keyIterator = keys.iterator();
    
                while (keyIterator.hasNext()) {
    
                    SelectionKey key = keyIterator.next();
    
                    if (key.isAcceptable()) {
    
                        ServerSocketChannel ssChannel1 = (ServerSocketChannel) key.channel();
    
                        // 服务器会为每个新连接创建一个 SocketChannel
                        SocketChannel sChannel = ssChannel1.accept();
                        sChannel.configureBlocking(false);
    
                        // 这个新连接主要用于从客户端读取数据
                        sChannel.register(selector, SelectionKey.OP_READ);
    
                    } else if (key.isReadable()) {
    
                        SocketChannel sChannel = (SocketChannel) key.channel();
                        System.out.println(readDataFromSocketChannel(sChannel));
                        sChannel.close();
                    }
    
                    keyIterator.remove();
                }
            }
        }
    
        private static String readDataFromSocketChannel(SocketChannel sChannel) throws IOException {
    
            ByteBuffer buffer = ByteBuffer.allocate(1024);
            StringBuilder data = new StringBuilder();
    
            while (true) {
    
                buffer.clear();
                int n = sChannel.read(buffer);
                if (n == -1) {
                    break;
                }
                buffer.flip();
                int limit = buffer.limit();
                char[] dst = new char[limit];
                for (int i = 0; i < limit; i++) {
                    dst[i] = (char) buffer.get(i);
                }
                data.append(dst);
                buffer.clear();
            }
            return data.toString();
        }
    }
    ```

  - ```java
    public class NIOClient {
    
        public static void main(String[] args) throws IOException {
            Socket socket = new Socket("127.0.0.1", 8888);
            OutputStream out = socket.getOutputStream();
            String s = "hello world";
            out.write(s.getBytes());
            out.close();
        }
    }
    ```

- **内存映射文件：**

  - 内存映射文件 I/O 是一种读和写文件数据的方法，它可以比常规的基于流或者基于通道的 I/O 快得多。

  - 向内存映射文件写入可能是危险的，只是改变数组的单个元素这样的简单操作，就可能会直接修改磁盘上的文件。修改数据与将数据保存到磁盘是没有分开的。

  - 下面代码行将文件的前 1024 个字节映射到内存中，map() 方法返回一个 MappedByteBuffer，它是 ByteBuffer 的子类。因此，可以像使用其他任何 ByteBuffer 一样使用新映射的缓冲区，操作系统会在需要时负责执行映射。

  - ```java
    MappedByteBuffer mbb = fc.map(FileChannel.MapMode.READ_WRITE, 0, 1024);
    ```

- **对比：**

  - NIO 与普通 I/O 的区别主要有以下两点：
    - NIO 是非阻塞的；
    - NIO 面向块，I/O 面向流。