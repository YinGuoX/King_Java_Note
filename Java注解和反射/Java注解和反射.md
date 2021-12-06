# 注解与反射

## 1. 注解-Annotation

### 1.1 什么是注解？

- Annotation是JDK5.0开始引入的新技术
- 作用：
  - 不是程序本身，但是可以对程序作出解释
  - 可以被其他程序读取（如：编译器等）
  - 检查、约束代码
- 格式：
  - @注释名，还可以添加参数
- 使用：
  - 可以附加在package、class、method、field上面，相当于给这些代码添加了额外的辅助信息，可以通过反射机制编程实现对这些信息的访问

### 1.2 内置注解

- ==@Override==：定义在java.lang.Override中，只适用于修饰方法，表示一个方法声明打算重写超类中的另一个方法声明
- ==@Deprecated==：定义在java.lang.Deprecated中，可以用于修饰方法、属性、类，表示不鼓励程序员使用这样的元素，通常是因为很危险或者存在更好的选择
- ==@SuppressWarnings==：定义在java.lang.SuppressWarnings中，用来抑制编译时的警告信息
  - 需要添加一个参数才能正确使用，这些参数都是定义好了的，选择性使用即可

### 1.3 元注解

![](.\images\1.png)

```java
@Target(value={ElementType.METHOD})
@Retention(value = RetentionPolicy.RUNTIME)
@Documented
@Inherited
@interface MyAnnotation{

}
```

### 1.4 自定义注解

![](.\images\2.png)

```java
public class Test3 {
    // 参数的赋值无序，只要标明参数名即可
    @MyAnnotation1(schools = {"xx","xxx"},name1 = "xxx",age=10)
    public static void test(){
        System.out.println("use my define annotation!");
    }
    public static void main(String[] args) {
        test();
    }
}

@Target(value = {ElementType.TYPE,ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@interface MyAnnotation1{
    // 注解的参数：参数类型+参数名+();
    String name1();
    // 提供默认值
    String name2() default "";
    int age();
    int id() default -1;
    String[] schools() default {"xxx","asd"};
}
```

## 2. 反射-Reflection

### 2.1 什么是反射？

- 静态语言和动态语言？

![](.\images\3.png)

- 反射

![](.\images\4.png)

- ==优缺点==:
  - 可以实现动态创建对象和编译，体现出很大的灵活性
  - 对性能有影响。使用反射基本上是一种解释操作：告诉JVM希望做什么并且它满足我们的要求，这类操作总是慢于直接执行相同的操作
- ==反射相关的主要API==：
  - java.lang.Class：代表一个类
  - java.lang.reflect.Method：代表类的方法
  - java.lang.reflect.File：代表类的成员变量
  - java.lang.reflect.Constructor：代表类的构造器
  - ...

### 2.2 Class类

![](.\images\5.png)

![](.\images\6.png)

![](.\images\7.png)

- 代码实例

```java
public class Test1 {
    public static void main(String[] args) throws ClassNotFoundException {
        //通过反射获取类的class对象
        Class<?> c1 = Class.forName("reflection.User");
        Class<?> c2 = Class.forName("reflection.User");
        Class<?> c3 = Class.forName("reflection.User");
        System.out.println(c1);

        // 一个类在内存中只有一个Class对象
        // 一个类被加载后，类的整个结构都会被封装在Class对象中
        System.out.println(c1==c2);
        System.out.println(c1==c3);
        System.out.println(c1.hashCode());
        System.out.println(c2.hashCode());
        System.out.println(c3.hashCode());

    }
}

// 实体类
class User{
    private String name;
    private int id;
    private int age;

    public User() {
    }

    public User(String name, int id, int age) {
        this.name = name;
        this.id = id;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", id=" + id +
                ", age=" + age +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

### 2.3 获取Class

![](.\images\8.png)

- ==哪些类型可以有Class对象==：
  - class：外部类、成员(成员内部类、静态内部类)，局部内部类、匿名内部类
  - interface：接口
  - []：数组
  - enum：枚举
  - annotation：注解
  - primitive type：基本数据类型
  - void
- 代码实例

```java
// 获取所有类型的Class
public class Test2 {
    public static void main(String[] args) {
        Class c1 = Object.class;
        Class c2 = Comparable.class;
        Class c3 = String[].class;
        Class c4 = int[][].class;
        Class c5 = Override.class;
        Class c6 = ElementType.class;
        Class c7 = Integer.class;
        Class c8 = void.class;
        Class c9 = Class.class;

        System.out.println(c1);
        System.out.println(c2);
        System.out.println(c3);
        System.out.println(c4);
        System.out.println(c5);
        System.out.println(c6);
        System.out.println(c7);
        System.out.println(c8);
        System.out.println(c9);
        // 只要元素类型和维度一样，就是同一个Class
        int[] a = new int[10];
        int[] b = new int[10];
        int[] c = new int[100];
        System.out.println(a.getClass().hashCode());
        System.out.println(b.getClass().hashCode());
        System.out.println(c.getClass().hashCode());
    }
}
```

### 2.4 Java内存分析

![](.\images\9.png)

![](.\images\10.png)

![](.\images\11.png)

```java
package reflection;

public class Test3 {
    public static void main(String[] args) {
        A a = new A();
        System.out.println(a.m);
        /*
        输出：
            A类静态代码块初始化...
            A类的构造初始化
            100
        整体过程：
            1.加载到JVM的方法区中，会产生一个类对应的Class对象（应该在堆中???待续）
            2.链接，分配内存，赋初始值，链接结束后m=0;
            3.初始化
                <clinit>(){
                    System.out.println("A类静态代码块初始化...");
                    m=3;
                    m=100;
                }
         */
    }
}
class A{
    static {
        System.out.println("A类静态代码块初始化...");
        m=3;
    }
    static int m = 100;
    public A(){
        System.out.println("A类的构造初始化");
    }
}
```

![](.\images\12.png)

![](.\images\13.png)

![](.\images\14.png)

- 代码实例

```java
package reflection;

public class Test5 {
    public static void main(String[] args) throws ClassNotFoundException {
        // 获取系统类加载器
        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
        System.out.println(systemClassLoader);

        // 获取扩展类加载器
        ClassLoader parent = systemClassLoader.getParent();
        System.out.println(parent);

        // 获取引导类加载器(c/c++)
        ClassLoader parent1 = parent.getParent();
        System.out.println(parent1);

        // 测试当前类是哪个加载器加载的
        ClassLoader classLoader = Class.forName("reflection.Test5").getClassLoader();
        System.out.println(classLoader);
        // 测试内置类是谁加载的
        ClassLoader classLoader1 = Class.forName("java.lang.ClassLoader").getClassLoader();
        System.out.println(classLoader1);

        // 如何获取系统类加载器可以加载的路径
        /*
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\charsets.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\deploy.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\access-bridge-64.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\cldrdata.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\dnsns.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\jaccess.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\jfxrt.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\localedata.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\nashorn.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\sunec.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\sunjce_provider.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\sunmscapi.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\sunpkcs11.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\ext\zipfs.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\javaws.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\jce.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\jfr.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\jfxswt.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\jsse.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\management-agent.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\plugin.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\resources.jar;
        D:\Program Files\Java\jdk1.8.0_261\jre\lib\rt.jar;
        F:\King_File\Java_Study\Java_Note\注解和反射\Demo1\out\production\Demo1;
        D:\Program Files\JetBrains\IntelliJ IDEA 2020.2.1\lib\idea_rt.jar
         */
        System.out.println(System.getProperty("java.class.path"));
    }
}

```

### 2.5 获取运行时类的完整结构

- 通过反射可以获取运行时类的完整结构
  - Field Method Constructor Superclass Interface Annotation...
- 代码实例

```java
package reflection;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

// 获取类的信息
public class Test6 {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException, NoSuchMethodException {
        Class aClass = Class.forName("reflection.GetClassInfo");

        GetClassInfo getClassInfo = new GetClassInfo();
        Class aClass1 = getClassInfo.getClass();

        // 获取类的名字
        System.out.println(aClass.getName());
        System.out.println(aClass.getSimpleName());
        System.out.println(aClass==aClass1);

        // 获取类的属性
        System.out.println("===========");
        Field[] fields = aClass.getFields();// 只能找到public属性
        for (Field field : fields) {
            System.out.println(field);
        }
        Field[] declaredFields = aClass.getDeclaredFields();//获得类的全部属性
        for (Field declaredField : declaredFields) {
            System.out.println(declaredField);
        }

        // 获得指定的属性
        Field name = aClass.getDeclaredField("name");
        System.out.println(name);

        // 获得类的方法
        System.out.println("===========");
        Method[] methods = aClass.getMethods();//获得类和该父类的所有public方法
        for (Method method : methods) {
            System.out.println("getMethods:"+method);
        }
        Method[] declaredMethods = aClass.getDeclaredMethods();//获得本类的全部方法
        for (Method declaredMethod : declaredMethods) {
            System.out.println("getDeclaredMethods:"+declaredMethod);
        }

        // 获得指定方法
        // 因为有重载，所以方法要指定方法参数的class
        Method getName = aClass1.getMethod("getName", null);
        System.out.println(getName);
        Method setAge = aClass.getMethod("setAge", int.class);
        System.out.println(setAge);

        // 获得指定的构造器
        System.out.println("===========");
        Constructor[] constructors = aClass.getConstructors();
        for (Constructor constructor : constructors) {
            System.out.println(constructor);
        }

        Constructor[] declaredConstructors = aClass.getDeclaredConstructors();
        for (Constructor declaredConstructor : declaredConstructors) {
            System.out.println(declaredConstructor);
        }

        // 获得指定的构造器
        Constructor constructor = aClass.getConstructor(String.class, int.class, int.class);
        System.out.println(constructor);
//        System.out.println(int.class);
//        System.out.println(Integer.class);

    }
}


class GetClassInfo{
    private String name;
    private int id;
    private int age;

    public GetClassInfo() {
    }
    private void test(){

    }
    public GetClassInfo(String name, int id, int age) {
        this.name = name;
        this.id = id;
        this.age = age;
    }

    @Override
    public String toString() {
        return "GetClassInfo{" +
                "name='" + name + '\'' +
                ", id=" + id +
                ", age=" + age +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

### 2.6 根据Class创建对象(实例)

![](.\images\15.png)

![](.\images\16.png)

![](.\images\17.png)

![](.\images\18.png)

- 代码实例

```java
package reflection;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

// 动态创建对象
public class Test7 {
    public static void main(String[] args) throws Exception, IllegalAccessException, InstantiationException {
        // 获得Class对象
        Class c1 = Class.forName("reflection.GetClassInfo");

        // 构造一个对象
        GetClassInfo o = (GetClassInfo)c1.newInstance();// 本质是调用了类的无参构造器
        System.out.println(o);

        // 通过构造器创建对象
        Constructor declaredConstructor = c1.getDeclaredConstructor(String.class, int.class, int.class);
        GetClassInfo hh = (GetClassInfo)declaredConstructor.newInstance("hh", 18, 666);
        System.out.println(hh);
        System.out.println(hh.getAge());

        // 通过反射获取一个方法
        Method setName = c1.getDeclaredMethod("setName", String.class);
        setName.invoke(hh,"喜喜");
        System.out.println(hh.getName());
        System.out.println(hh);
        // 通过反射操作类的属性
        System.out.println("===========");
        Field name = c1.getDeclaredField("name");
        // 不能直接操作私有属性，如果需要则要关闭程序的安全检测
        name.setAccessible(true);
        name.set(hh,"六六六");
        System.out.println(hh);
    }
}

```

## 3. 性能分析

- 代码实例

```java
package reflection;

import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

// 分析反射的性能
public class Test8 {
    // 普通方式调用
    public static void test1(){
        GetClassInfo getClassInfo=new GetClassInfo();
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < 1000000000; i++) {
            getClassInfo.getName();
        }
        long endTime = System.currentTimeMillis();
        System.out.println("普通方式执行10忆次："+(endTime-startTime)+"ms");
    }

    // 反射方式调用
    public static void test2() throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        GetClassInfo getClassInfo=new GetClassInfo();
        Class c1 = getClassInfo.getClass();
        Method getName = c1.getDeclaredMethod("getName", null);

        long startTime = System.currentTimeMillis();
        for (int i = 0; i < 1000000000; i++) {
            getName.invoke(getClassInfo,null);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("反射方式执行10忆次："+(endTime-startTime)+"ms");
    }

    // 反射方式调用 关闭权限检测
    public static void test3() throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        GetClassInfo getClassInfo=new GetClassInfo();
        Class c1 = getClassInfo.getClass();
        Method getName = c1.getDeclaredMethod("getName", null);
        getName.setAccessible(true);
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < 1000000000; i++) {
            getName.invoke(getClassInfo,null);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("反射反射+关闭权限检测执行10忆次："+(endTime-startTime)+"ms");
    }

    public static void main(String[] args) throws NoSuchMethodException, IllegalAccessException, InvocationTargetException {
        test1();
        test2();
        test3();
    }
}
普通方式执行10忆次：4ms
反射方式执行10忆次：1942ms
反射反射+关闭权限检测执行10忆次：1426ms
```

## 4. 反射操作泛型

![](.\images\19.png)

- 代码实例

```java
package reflection;

import java.lang.reflect.Method;
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;
import java.util.List;
import java.util.Map;

public class Test9 {
    public void test1(Map<String ,GetClassInfo> map, List<GetClassInfo> list){
        System.out.println("test1");
    }

    public Map<String,User> test2(){
        System.out.println("test2");
        return null;
    }

    public static void main(String[] args) throws Exception {
        Method test1 = Test9.class.getDeclaredMethod("test1", Map.class, List.class);
        Type[] genericParameterTypes = test1.getGenericParameterTypes();
        for (Type genericParameterType : genericParameterTypes) {
            System.out.println("=:"+genericParameterType);
            if(genericParameterType instanceof ParameterizedType){
                Type[] actualTypeArguments = ((ParameterizedType) genericParameterType).getActualTypeArguments();
                for (Type actualTypeArgument : actualTypeArguments) {
                    System.out.println(actualTypeArgument);
                }
            }
        }
        System.out.println("=========");
        Method test2 = Test9.class.getDeclaredMethod("test2",null);
        Type genericReturnType = test2.getGenericReturnType();
        System.out.println(genericReturnType);
        if(genericReturnType instanceof ParameterizedType){
            Type[] actualTypeArguments = ((ParameterizedType) genericReturnType).getActualTypeArguments();
            for (Type actualTypeArgument : actualTypeArguments) {
                System.out.println(actualTypeArgument);
            }
        }

    }
}

```

## 5.反射操作注解

```java
package reflection;

import java.lang.annotation.*;
import java.lang.reflect.Field;

// 反射操作注解
public class Test10 {
    public static void main(String[] args) throws Exception {
        Class aClass = Class.forName("reflection.Teacher");

        // 通过反射获取注解
        Annotation[] annotations = aClass.getAnnotations();
        for (Annotation annotation : annotations) {
            System.out.println(annotation);
        }

        // 获得注解的value值
        TableKing annotation = (TableKing)aClass.getAnnotation(TableKing.class);
        String value = annotation.value();
        System.out.println(value);

        // 获得类属性的注解
        Field name = aClass.getDeclaredField("name");
        FieldKing annotation1 = name.getAnnotation(FieldKing.class);
        System.out.println(annotation1);
        System.out.println(annotation1.columnName());
        System.out.println(annotation1.type());
        System.out.println(annotation1.length());
        
    }
}

// 类名的注解
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@interface TableKing{
    String value();
}

// 属性的注解
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@interface FieldKing{
    String columnName();
    String type();
    int length();
}
@TableKing(value = "db_teacher")
class Teacher{
    @FieldKing(columnName = "db_id",type = "int",length = 10)
    private int id;
    @FieldKing(columnName = "db_age",type = "int",length = 10)
    private int age;
    @FieldKing(columnName = "db_name",type = "varchar",length = 100)
    private String name;
    public Teacher(){

    }

    public Teacher(int id,int age,String name){
        this.id=id;
        this.age=age;
        this.name=name;
    }

    @Override
    public String toString() {
        return "Teacher{" +
                "id=" + id +
                ", age=" + age +
                ", name='" + name + '\'' +
                '}';
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }
}

```

