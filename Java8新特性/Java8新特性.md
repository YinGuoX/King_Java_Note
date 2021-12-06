# JDK8新特性

## 1. Lambda表达式&函数式接口

### 1.1 什么是Lambda表达式？

- 是一种匿名函数的代替，可以将函数作为一个方法的参数一样传递进方法中，将代码像数据一样传递，目的是简化代码的编写

### 1.2 什么是函数式接口？

- 只有一个抽象方法的接口，JDK8也提供了一个注解在编译时去检查语法是否规范
- @FunctionInterface

### 1.3 Lambda的基本使用

- 基本语法

```
函数式接口 变量名 = (参数1，参数2,...)->{
	// 方法体
};
```

- 实例

```java
public static void main(String[] args) {
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("run...");
            }
        };
        new Thread(runnable).start();

        Runnable runnable1 = ()->{
            System.out.println("hello...");
        };
        new Thread(runnable1).start();
        new Thread(()-> System.out.println("hello xxx")).start();

        Comparator<String> comparator = new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o1.length()-o2.length();
            }
        };
        TreeSet<String> treeSet = new TreeSet<>(comparator);
        
        TreeSet<String> treeSet1 = new TreeSet<>((String o1,String o2)->{
           return o1.length()-o2.length();
        });
        TreeSet<String> treeSet2 = new TreeSet<>((o1,o2)->o1.length()-o2.length());
    }
```

### 1.4 Lambda表达式的注意事项

- Lambda引入了新的操作符: ->(箭头操作符)，->将表达式分割成两部分
  - 左边：(参数1，参数2...)：表示参数列表
  - 右边：{...}：内部是方法体
- 左边的参数列表的数据类型会自动推断，因此无需定义数据类型
- 如果形参列表为空，则只需保留()
- 如果形参列表只有一个，则()可以省略，只保留参数名即可
- 如果方法体的执行语句只有一条
  - 无返回值，则{}可以省略
  - 有返回值，省略{}也要省略return
- lambda不会生成一个单独的内部类文件
- lambda表达式如果访问局部变量，则局部比哪里必须是final的，若局部局部变量没有加final关键字，则系统会自动添加，此后要是修改该局部变量，则会报错

## 2. 流式编程-StreamAPI

### 2.1 什么是Stream?

- Stream是java8中处理数组、集合的抽象概念，可以指定对集合的操作，可以执行非常复杂的查找、过滤、映射数据等操作，使用Stream API对数据进行操作，就类似使用SQL执行的数据库查询
- Stream与集合类似，但是集合是用来保存数据的，Stream主要是对数据进行操作
- Stream的特点
  - Stream不会存储元素
  - Stream不会根本源对象，而是返回一个持有结果的新Stream
  - Stream是延迟操作，需要结果时才会运行
  - 遵循“做什么，而不是怎么去做”的原则：只需要描述需要做什么，而不是考虑程序是怎样实现的

### 2.2 Stream API实例

```java
public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("a");
        list.add("hello");
        list.add("xxxx");
        long count =0;
        for (String s : list) {
            if(s.length()>3){
                count++;
            }
        }
        System.out.println(count);

        // Stream+Lambda
        // 链式编程
        long count2= list.stream().filter((s)->{return s.length()>3;}).count();
        System.out.println(count2);
    }
```

### 2.3 Stream API 的使用步骤

1. 创建：创建一个Stream
2. 中间操作：将初始的stream转化导另一个stream的中间操作
3. 终止操作：使用一个终止操作来产生一个结果，该操作会强制之前的延迟操作立即执行，此后该Stream就不能再被使用了

### 2.4 创建Stream

- 集合创建Stream
  - 对象实例名.stream();
- 数组创建Stream
  - Arrays.stream(array);

### 2.5 Stream的中间操作

```java
package stream;

import java.util.Objects;

public class Employee {
    private String name;
    private int age;
    private  int salary;

    public Employee(String name, int age, int salary) {
        this.name = name;
        this.age = age;
        this.salary = salary;
    }

    public Employee() {
    }

    @Override
    public String toString() {
        return "Employee{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", salary=" + salary +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Employee employee = (Employee) o;
        return age == employee.age &&
                salary == employee.salary &&
                Objects.equals(name, employee.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age, salary);
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public int getSalary() {
        return salary;
    }

    public void setSalary(int salary) {
        this.salary = salary;
    }
}

```

- 过滤、去重、跳过、分页

```java
public static void main(String[] args) {
        List<Employee> employees = new ArrayList<>();
        employees.add(new Employee("hhh",20,80000));
        employees.add(new Employee("lil",30,180000));
        employees.add(new Employee("xxx",40,280000));
        employees.add(new Employee("vb",40,280000));

        // 查询
        Stream<Employee> employeeStream = employees.stream().filter(e -> e.getAge() > 30);
        employeeStream.forEach(e->System.out.println(e));

        // 去重
        long count = employees.stream().distinct().count();
        System.out.println(count);

        // 跳过
        Stream<Employee> skip = employees.stream().skip(1);
        skip.forEach(e-> System.out.println(e));
        System.out.println("-------------");
        // 分页
        Stream<Employee> limit = employees.stream().limit(2);
        limit.forEach(System.out::println);
    }
```

- 映射
  - 通常用于对象转换

```java
 // map
        System.out.println("--------------");
        // 将元素转换成奇怪形式或者提取信息，接收一个函数作为参数，
        // 该函数会作用到每个元素上，并且生成一个新的元素
        Stream<String> stream = employees.stream().map(e -> {return  e.getName()+"-xxx";});
        stream.forEach(System.out::println);
        System.out.println("--------------");
        // 小写转大写
        Stream<String> stream1 = employees.stream().map(e -> {
            return e.getName().toUpperCase();
        });
        stream1.forEach(System.out::println);
```

- 排序

```java
		// 自然排序
        Stream<Integer> sorted = employees.stream().map(Employee::getAge).sorted();
        sorted.forEach(System.out::println);

        // 定制排序
        Stream<Employee> sorted1 = employees.stream().sorted((x, y) -> {
            if (x.getAge() == y.getAge()) {
                return x.getSalary() - y.getSalary();
            } else {
                return x.getAge() - y.getAge();
            }
        });
        sorted1.forEach(System.out::println);
```

### 2.6 终止操作

- 遍历操作
  - forEach
- 查找和匹配
  - allMatch：检查是否匹配所有元素
  - anyMatch：检查是否至少匹配一个元素
  - noneMathc：检查是否没有匹配的元素
  - findFirst：返回第一个元素
  - findAny：返回当前流中的容易一个元素
  - count：返回流中元素的个数
  - max：返回流中的最大值
  - min：返回流中的最小值

```java
		boolean b = employees.stream().allMatch(e -> e.getAge() > 30);
        boolean b1 = employees.stream().anyMatch(e -> e.getAge() > 300);
        Optional<Employee> max = employees.stream().max((x, y) -> x.getAge() - y.getAge());
        System.out.println(max);
        System.out.println(max.get());
```

### 2.7 Stream的串行和并行

- 串行Stream上的操作是在一个线程中依次完成
- 并行Stream上的操作是在多个线程同时完成

```java
		// 初始化数据
        int len = 10000000;
        List<String> values = new ArrayList<>();
        for (int i = 0; i <len ; i++) {
            UUID uuid = UUID.randomUUID();
            values.add(uuid.toString());
        }
        // 串行计算-6472
        long strat = System.currentTimeMillis();
        long count1 = values.stream().sorted().count();
        System.out.println(count1);
        long end = System.currentTimeMillis();
        long millis = end-strat;
        System.out.println(millis);

        // 并行计算-2325
        long strat1 = System.currentTimeMillis();
        long count2 = values.parallelStream().sorted().count();
        System.out.println(count2);
        long end1 = System.currentTimeMillis();
        long millis1 = end1-strat1;
        System.out.println(millis1);
```

