# JDK8新特性

# 1:Lamdba

## 1.1:匿名内部类的问题

当需要启动一个线程去完成任务时，通常会用Runnable接口来定义任务内容，并使用Thread类来启动

```java
public class Test01 {
    public static void main(String[] args) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("run");
            }
        }).start();
    }
}
```

由于面向对象的语法要求，首先创建一个 Runnable 接口的匿名内部类任务对象来指定线程要执行的任务内容，再将其交给一个线程来启动

对于 Runnable 的匿名内部类用法，可以分析出几点内容： 

- Thread 类需要 Runnable 接口作为参数
- 其中的抽象 run 方法是用来指定线程任务内容的核心 
- 为了指定 run 的方法体，不得不需要 Runnable 接口的实现类 
- 为了省去定义一个 Runnable 实现类的麻烦
- 就不得不使用匿名内部类 必须覆盖重写抽象 run 方法
- 所以方法名称、方法参数、方法返回值不得不再写一遍，且不能写错
- 而实际上，似乎只有方法体才是关键所在



## 1.2:Lambda体验

Lambda是一个匿名函数，可以理解为一段可以传递的代码

Lambda表达式写法，代码如下： 

借助Java 8的全新语法

上述 Runnable 接口的匿名内部类写法可以通过更简单的Lambda表达式达到相同的效果

```java
public class Test02 {
    public static void main(String[] args) {
        new Thread(()->{
            System.out.println("run");
        }).start();
    }
}
```

这段代码和刚才的执行效果是完全一样的，可以在JDK 8或更高的编译级别下通过

从代码的语义中可以看出：我们启动了一个线程，而线程任务的内容以一种更加简洁的形式被指定

我们只需要将要执行的代码放到一个Lambda表达式中，不需要定义类，也不需要创建对象



优点：简化匿名内部类的使用，语法更加简单



## 1.3:标准格式

```
(参数类型 参数名称) -> {
	代码体;
}
```



## 1.4:无参数无返回值

1：接口

```java
interface Swimmable {
    void swimming();
}
```

2：方法，需要一个Swimmable接口

```java
public static void goSwimm(Swimmable swimmable){
    swimmable.swimming();
}
```

3：使用匿名内部类来实现

```java
public static void main(String[] args) {
    goSwimm(new Swimmable() {
        @Override
        public void swimming() {
            System.out.println("匿名内部类游泳");
        }
    });
}
```

4：使用lamdba实来现

```java
goSwimm(()->{
    System.out.println("lamdba游泳");
});
```



## 1.5:有参数有返回值

下面举例演示 java.util.Comparator 接口的使用场景代码，其中的抽象方法定义为：

```java
int compare(T o1, T o2);
```

当需要对一个对象集合进行排序时

Collections.sort 方法需要一个 Comparator 接口实例来指定排序的规则

1：学生类

```java
class Student{
    private String name;
    private Integer age;
    //省略构造方法/set/get/toString
}
```

2：加入学生到集合

```java
public class Test04 {
    public static void main(String[] args) {
        ArrayList<Student> students = new ArrayList<Student>();
        Student student1=new Student("鲁迅",52);
        Student student2=new Student("周志雄",21);
        Student student3=new Student("巴金",74);
        students.add(student1);
        students.add(student2);
        students.add(student3);
    }
}
```

3：使用匿名内部类来按照年龄排序

```java
Collections.sort(students, new Comparator<Student>() {
    @Override
    public int compare(Student o1, Student o2) {
        return o1.getAge()-o2.getAge();
    }
});
System.out.println(students);
```

4：使用lamdba来按照年龄排序

```java
Collections.sort(students,(o1,o2)->{
    return o1.getAge()-o2.getAge();
});
System.out.println(students);
```



## 1.6:实现原理

匿名内部类在编译的时候会生成一个class文件

Lambda在程序运行的时候形成一个类 

在类中新增一个方法，这个方法的方法体就是Lambda表达式中的代码 

还会形成一个匿名内部类，实现接口，重写抽象方法

在接口的重写方法中会调用新生成的方法



## 1.7:省略格式

在Lambda标准格式的基础上，使用省略写法的规则为： 

- 小括号内参数的类型可以省略 
- 如果小括号内有且仅有一个参数，则小括号可以省略
- 如果大括号内有且仅有一个语句，可以同时省略大括号、return关键字及语句分号



完整写法：

```java
(int a) -> {
	return new Person();
}
```

简化后写法：

```java
a -> new Person()
```



## 1.8:前提条件

Lambda的语法非常简洁，但是Lambda表达式不是随便使用的，使用时有几个条件要特别注意： 

- 方法的参数或局部变量类型必须为接口才能使用Lambda

- 接口中有且仅有一个抽象方法



## 1.9:函数式接口

函数式接口在Java中是指：有且仅有一个抽象方法的接口 

函数式接口，即适用于函数式编程场景的接口

而Java中的函数式编程体现就是Lambda

所以函数式接口就是可以适用于Lambda使用的接口

只有确保接口中有且仅有一个抽象方法

Java中的Lambda表达式才能顺利地进行推导



FunctionalInterface 注解 

Java 8中专门为函数式接口引入了一个新的注解： @FunctionalInterface 

该注解可用于一个接口的定义上

```java
@FunctionalInterface
public interface Operator {
	void myMethod();
}
```

一旦用该注解来定义接口，编译器将会强制检查该接口是否确实有且仅有一个抽象方法，否则将会报错，不过即使不使用该注解，只要满足函数式接口的定义，这仍然是一个函数式接口，只是多了检查，本质上没有区别

## 1.10:匿名内部类对比

1：所需的类型不一样 

- 匿名内部类，需要的类型可以是类，抽象类，接口 
- Lambda表达式，需要的类型必须是接口 

2：抽象方法的数量不一样 

- 匿名内部类所需的接口中抽象方法的数量随意 
- Lambda表达式所需的接口只能有一个抽象方法 

3：实现原理不同 

- 匿名内部类是在编译后会形成class
- Lambda表达式是在程序运行的时候动态生成class



# 2:常用内置函数式接口

## 2.1:由来

我们知道使用Lambda表达式的前提是需要有函数式接口【有且仅有一个抽象方法的接口】而Lambda使用时

不关心接口名，抽象方法名，只关心抽象方法的参数列表和返回值类型。因此为了让我们使用Lambda方

便，JDK提供了大量常用的函数式接口

它们主要在 java.util.function 包中。下面是最常用的几个接口

- Supplier接口【供应】
- Consumer接口【消费】
- Function接口【函数】
- Predicate接口【断言】



## 2.2:Supplier

```sh
1.Supplier<T>:包含一个无参的方法

2.T get()：获得结果

3.该方法不需要参数，它会按照某种实现逻辑(Lambda表达式实现)返回一个数据

4.Supplier<T>接口也被称为生产型接口，如果我们指定了接口的泛型是什么类型，那么接口中的get方法就会生产什么类型的数据供我们使用
```

翻译为供应商，它意味着"供给" ，对应的Lambda表达式需要“对外提供”一个符合泛型类型的对象数据

供给型接口，通过Supplier接口中的get方法可以得到一个值，Supplier是一个无参数，但是有返回的接口

这个接口的作用就是：不需要提供数据，来得到一个泛型的值

```java
@FunctionalInterface
public interface Supplier<T> {
    
    T get();
    
}
```

案例：使用Lambda表达式返回数组元素最大值

使用 Supplier 接口作为方法参数类型，通过Lambda表达式求出int数组中的最大值

提示：接口的泛型请使用 java.lang.Integer 类

![image-20230304225417392](https://zzx-note.oss-cn-beijing.aliyuncs.com/javase/image-20230304225417392.png)

上面的代码：传入printMax()的时候，没有写任何的参数，但是返回了一个值

这就体现了供给的思想，不提供参数，但是得到了结果！

## 2.3:Consumer

Consumer的意思是消费：意思是提供参数，被消费，但是没有返回值

这个接口和Supplier则正好相反，它不是生产一个数据，而是消费一个数据，其数据类型由泛型参数决定

```java
@FunctionalInterface
public interface Consumer<T> {

    void accept(T t);
    
}
```

案例：使用Lambda表达式将一个英文的字符串转成大写和小写的字符串

![image-20230304225639837](https://zzx-note.oss-cn-beijing.aliyuncs.com/javase/image-20230304225639837.png)



## 2.4:Function

根据一个类型的数据得到另一个类型的数据，前者称为前置条件， 后者称为后置条件。有参数有返回值，前面是参数，后面是返回值

```java
@FunctionalInterface
public interface Function<T, R> {
	public abstract R apply(T t);
}
```

案例：使用Lambda表达式将字符串类型的数字转成数字

![image-20230304225855518](https://zzx-note.oss-cn-beijing.aliyuncs.com/javase/image-20230304225855518.png)



## 2.5:Predicate

有时候我们需要对某种类型的数据进行判断，从而得到一个boolean值结果。这时可以使用Predicate接口，有参数，返回值是布尔类型

```java
@FunctionalInterface
public interface Predicate<T> {
	public abstract boolean test(T t);
}
```

案例：使用Lambda判断一个人名如果超过3个字就认为是很长的名字

![image-20230304230124766](https://zzx-note.oss-cn-beijing.aliyuncs.com/javase/image-20230304230124766.png)

既然是条件判断，就会存在与、或、非三种常见的逻辑关系

其中将两个 Predicate 条件使用“与”逻辑连接起来实现“并且”的效果时

可以使用default 方法 and

同理还有 or 方法

```java
@FunctionalInterface
public interface Predicate<T> {

    boolean test(T t);

    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }
    
    default Predicate<T> or(Predicate<? super T> other) {
    	Objects.requireNonNull(other);
    	return (t) -> test(t) || other.test(t);
    }
}
```

使用Lambda表达式判断一个字符串中即包含W,也包含H 

使用Lambda表达式判断一个字符串中包含W或者包含H 



1：使用Lambda表达式判断一个字符串中即包含W,也包含H

```java
public class TestPredicate01 {
    public static void main(String[] args) {
        isContain("张三HW",(str)->{
            return str.contains("W");
        }, (str)->{
            return str.contains("H");
        });
    }

    public static void isLongName(String name, Predicate<String> predicate1,Predicate<String> predicate2){
        boolean test = predicate1.and(predicate2).test(name);
        System.out.println(test);
    }
}
```

2：使用Lambda表达式判断一个字符串中包含W或者包含H 

```java
public class TestPredicate01 {
    public static void main(String[] args) {
        isLongName("张三W",(str)->{
            return str.contains("W");
        }, (str)->{
            return str.contains("H");
        });
    }

    public static void isLongName(String name, Predicate<String> predicate1,Predicate<String> predicate2){
        boolean test = predicate1.or(predicate2).test(name);
        System.out.println(test);
    }
}
```

# 3:Stream流

## 3.1:集合处理数据的弊端

当我们需要对集合中的元素进行操作的时候，除了必需的添加、删除、获取外，最典型的就是集合遍历

我们来体验 集合操作数据的弊端，需求如下：

一个ArrayList集合中存储有以下数据：张无忌,周芷若,赵敏,张强,张三丰 

需求:

- 拿到所有姓张的
- 拿到名字长度为3个字的 
- 打印这些数据

使用原来的方法处理

```java
public class Test01 {
    public static void main(String[] args) {
        List<String> strings = new ArrayList<>();
        List<String> newList = new ArrayList<>();
        strings.add("张无忌");
        strings.add("周芷若");
        strings.add("赵敏");
        strings.add("张强");
        strings.add("张三丰");

        //需求一
        for (String name:strings){
            if (name.startsWith("张")){
                newList.add(name);
            };
        }
        System.out.println(newList);
        newList.clear();

        //需求二
        for (String name:strings){
            if(name.length()==3){
                newList.add(name);
            }
        }
        System.out.println(newList);
        newList.clear();

        //需求三
        for (String name:strings){
            System.out.println(name);
        }

    }
}
```

循环遍历的弊端：

这段代码中含有三个循环，每一个作用不同

1：首先筛选所有姓张的人

2：然后筛选名字有三个字的人

3：最后进行对结果进行打印输出

每当我们需要对集合中的元素进行操作的时候，总是需要进行循环、循环、再循环。这是理所当然的么？不是。循环是做事情的方式，而不是目的。每个需求都要循环一次，还要搞一个新集合来装数据，如果希望再次遍历，只能再使用另一个循环从头开始



## 3.2:Stream的更优写法

```java
public class Test01 {
    public static void main(String[] args) {
        List<String> strings = new ArrayList<>();
        strings.add("张无忌");
        strings.add("周芷若");
        strings.add("赵敏");
        strings.add("张强");
        strings.add("张三丰");

        // 这里得到的流就是包含姓为张的数据
        strings.stream().filter((name)->{
            return name.startsWith("张");
        });

        // 这里得到的流就是长度为3的数据
        strings.stream().filter((name)->{
            return name.length()==3;
        });
        
        strings.stream().forEach((name)->{
            System.out.println(name);
        });

    }
}
```

## 3.3:Stream流式思想

注意：Stream和IO流(InputStream/OutputStream)没有任何关系，请暂时忘记对传统IO流的固有印象！

Stream流式思想类似于工厂车间的 生产流水线，Stream流不是一种数据结构，不保存数据，而是对数据进

行加工处理。Stream可以看作是流水线上的一个工序。在流水线上，通过多个工序让一个原材料加工成一

个商品，Stream API能让我们快速完成许多复杂的操作，如筛选、切片、映射、查找、去除重复，统计，匹

配和归约，Stream是流式思想,相当于工厂的流水线,对集合中的数据进行加工处理



## 3.4:获取Stream流方式

java.util.stream.Stream 是JDK 8新加入的流接口

获取一个流非常简单，有以下几种常用的方式： 

- 所有的 Collection 集合【List Set】都可以通过 stream 默认方法获取流
- Stream 接口的静态方法 of 可以获取数组对应的流



1： 根据Collection获取流

首先， java.util.Collection 接口中加入了default方法 stream 用来获取流，所以其所有实现类均可获取流

```java
public class Test01 {
    public static void main(String[] args) {
        List<String> strings = new ArrayList<>();
        strings.add("张无忌");
        strings.add("周芷若");
        strings.add("赵敏");
        strings.add("张强");
        strings.add("张三丰");
        //获取Stream流
        Stream<String> stream = strings.stream();
    }
}
```

2：Stream中的静态方法of获取流

Stream 接口中提供了静态方法 of ，使用很简单

```java
public class Test01 {
    public static void main(String[] args) {
        Stream<String> stringStream = Stream.of("张三", "李四", "王五");
    }
}
```

of 方法的参数其实是一个可变参数，所以支持数组



## 3.5:Stream的常用方法

Stream流模型的操作很丰富，这里介绍一些常用的API。这些方法可以被分成两种：

1：终结方法：返回值类型不再是 Stream 类型的方法，不再支持链式调用



2：非终结方法：返回值类型仍然是 Stream 类型的方法，支持链式调用

注意：除了终结方法外，其余方法均为非终结方法【反正就是看返回值是不是Stream对象】

![image-20221008102219127](https://zzx-note.oss-cn-beijing.aliyuncs.com/javase/image-20221008102219127.png)

## 3.6:Stream注意事项

1：Stream只能操作一次 

2：Stream方法返回的是新的流 

3：Stream不调用终结方法，中间的操作不会执行



## 3.7:forEach

```java
void forEach(Consumer<? super T> action);
```

该方法接收一个 Consumer 接口函数【有参数，无返回值】会将每一个流元素交给该函数进行处理

```java
void accept(T t);
```

```java
public class Test01 {
    public static void main(String[] args) {
        List<String> one = new ArrayList<>();
        Collections.addAll(one, "迪丽热巴", "宋远桥", "苏星河", "老子", "庄子", "孙子");
        one.stream().forEach((name)->{
            System.out.println(name);
        });
    }
}
```



## 3.8:count

```java
long count()
```

该方法返回一个long值代表元素个数。基本使用：

```java
public class Test01 {
    public static void main(String[] args) {
        List<String> one = new ArrayList<>();
        Collections.addAll(one, "迪丽热巴", "宋远桥", "苏星河", "老子", "庄子", "孙子");
        long count = one.stream().count();
    }
}
```



## 3.9:filter

filter用于过滤数据，返回符合过滤条件的数据

可以通过 filter 方法将一个流转换成另一个子集流。

方法声明： Stream filter(Predicate predicate)

该接口接收一个 Predicate 函数式接口参数，如下，有参数，返回值boolean

```java
public abstract boolean test(T t)
```

案例：筛选姓名长度为2个字的并打印

```java
public class Test01 {
    public static void main(String[] args) {
        List<String> one = new ArrayList<>();
        Collections.addAll(one, "迪丽热巴", "宋远桥", "苏星河", "老子", "庄子", "孙子");
        one.stream().filter((name)->{
            return name.length()==2;
        }).forEach((name)->{
            System.out.println(name);
        });
    }
}
```

## 3.10:limit

limit 方法可以对流进行截取，只取用前n个

```java
Stream<T> limit(long maxSize)
```

参数是一个long型，如果集合当前长度大于参数则进行截取。否则不进行操作

基本使用：

```java
public class Test01 {
    public static void main(String[] args) {
        List<String> one = new ArrayList<>();
        Collections.addAll(one, "迪丽热巴", "宋远桥", "苏星河", "老子", "庄子", "孙子");
        one.stream().limit(3).forEach((name)->{
            System.out.println(name);
        });
    }
}
```



## 3.11:skip

如果希望跳过前几个元素，可以使用 skip 方法获取一个截取之后的新流

```java
Stream<T> skip(long n)
```

如果流的当前长度大于n，则跳过前n个；否则就是全部跳过了，将会得到一个长度为0的空流

基本使用：

```java
public class Test01 {
    public static void main(String[] args) {
        List<String> one = new ArrayList<>();
        Collections.addAll(one, "迪丽热巴", "宋远桥", "苏星河", "老子", "庄子", "孙子");
        one.stream().skip(4).forEach(System.out::println);
    }
}
```

## 3.12:map

如果需要将流中的元素映射到另一个流中，可以使用 map 方法

```java
<R> Stream<R> map(Function<? super T, ? extends R> mapper);
```

该接口需要一个 Function 函数式接口参数，可以将当前流中的T类型数据转换为另一种R类型的流

Function的抽象方法如下：

```java
@FunctionalInterface
public interface Function<T, R> {
	public abstract R apply(T t);
}
```

使用：

```java
public class Test01 {
    public static void main(String[] args) {
        Stream<String> stringStream = Stream.of("111", "222", "333");
        Stream<Integer> integerStream = stringStream.map((str) -> {
            return Integer.valueOf(str);
        });
    }
}
```

## 3.13:sorted

如果需要将数据排序，可以使用 sorted 方法

```java
Stream<T> sorted();
Stream<T> sorted(Comparator<? super T> comparator);
```

```java
public class Test01 {
    public static void main(String[] args) {
        Stream.of(33, 22, 11, 55)
                .sorted()
                .forEach(System.out::println);

        Stream.of(33,22,11,55)
                .sorted((o1,o2)->{
                    return o2-o1;
                }).forEach(System.out::println);
    }
}
```

## 3.14:distinct

如果需要去除重复数据，可以使用 distinct 方法

```java
Stream<T> distinct()
```

```java
public class Test01 {
    public static void main(String[] args) {
        Stream.of(22, 33, 22, 11, 33)
                .distinct()
                .forEach(System.out::println);
    }
}
```

如果是自定义类型如何是否也能去除重复的数据呢？

```java
public class Test01 {
    public static void main(String[] args) {
        Stream.of(
                new Person("刘德华", 58),
                new Person("张学友", 56),
                new Person("张学友", 56),
                new Person("黎明", 52))
                .distinct()
                .forEach(System.out::println);
    }
}
```

结果：

```java
Person{name='刘德华', age=58}
Person{name='张学友', age=56}
Person{name='张学友', age=56}
Person{name='黎明', age=52}
```

注意：自定义类型是根据对象的hashCode和equals来去除重复元素的



## 3.15:match

如果需要判断数据是否匹配指定的条件，可以使用 Match 相关方法

```java
boolean allMatch(Predicate<? super T> predicate);
boolean anyMatch(Predicate<? super T> predicate);
boolean noneMatch(Predicate<? super T> predicate);
```

基本使用

```java
public class Test01 {
    public static void main(String[] args) {
        boolean b = Stream.of(5, 3, 6, 1)
                .allMatch(e->e>0);
        System.out.println("b = " + b);
    }
}
```

```java
public class Test01 {
    public static void main(String[] args) {
        boolean b = Stream.of(5, 3, 6, 1)
                .anyMatch(e->e>0);
        System.out.println("b = " + b);
    }
}
```

```java
public class Test01 {
    public static void main(String[] args) {
        boolean b = Stream.of(5, 3, 6, 1)
                .noneMatch(e->e>0);
        System.out.println("b = " + b);
    }
}
```



## 3.16:find

```java
//其作用就是从Stream中取第一个元素
Optional<T> findFirst();

//其作用就是从Stream中取任意一个元素，正常情况下一般会取第一个元素，在并行流的情况下会随机取一个元素
Optional<T> findAny();
```

Stream流中的 find 相关方法基本使用的代码

```java
public class Test01 {
    public static void main(String[] args) {
        Optional<Integer> first = Stream.of(5, 3, 6, 1).findFirst();
        System.out.println("first = " + first.get());
        Optional<Integer> any = Stream.of(5, 3, 6, 1).findAny();
        System.out.println("any = " + any.get());
    }
}
```

## 3.17:max和min

```java
Optional<T> max(Comparator<? super T> comparator);
Optional<T> min(Comparator<? super T> comparator);
```

```java
public class Test01 {
    public static void main(String[] args) {
        Optional<Integer> max = Stream.of(5, 3, 6, 1).max((o1, o2) -> o1 - o2);
        System.out.println("max=" + max.get());
        Optional<Integer> min = Stream.of(5, 3, 6, 1).min((o1, o2) -> o1 - o2);
        System.out.println("min=" + min.get());
    }
}
```

## 3.18:mapToInt

如果需要将Stream中的Integer类型数据转成int类型，可以使用 mapToInt 方法

```java
IntStream mapToInt(ToIntFunction<? super T> mapper);
```

```java
public class Test01 {
    public static void main(String[] args) {
        Stream<Integer> stream = Arrays.stream(new Integer[]{1, 2, 3, 4, 5});
        stream.mapToInt((integer)->{
            return integer.intValue();
        });
    }
}
```



## 3.19:concat

如果有两个流，希望合并成为一个流，那么可以使用 Stream 接口的静态方法 concat 

备注：这是一个静态方法，与 java.lang.String 当中的 concat 方法是不同的

```java
public class Test01 {
    public static void main(String[] args) {
        Stream<String> streamA = Stream.of("张三");
        Stream<String> streamB = Stream.of("李四");
        Stream<String> result = Stream.concat(streamA, streamB);
        result.forEach(System.out::println);
    }
}
```



## 3.20:Stream案例

现在有两个 ArrayList 集合存储队伍当中的多个成员姓名，要求使用Stream来完成以下若干操作步骤： 

1：第一个队伍只要名字为3个字的成员姓名

2：第一个队伍筛选之后只要前3个人

3：第二个队伍只要姓张的成员姓名 

4：第二个队伍筛选之后不要前2个人 

5：将两个队伍合并为一个队伍 



原始数据如下：

```java
List<String> one = new ArrayList<>();
        one.add("迪丽热巴");
        one.add("宋远桥");
        one.add("苏星河");
        one.add("老子");
        one.add("庄子");
        one.add("孙子");
        one.add("洪七公");

List<String> two = new ArrayList<>();
        two.add("古力娜扎");
        two.add("张无忌");
        two.add("张三丰");
        two.add("赵丽颖");
        two.add("张二狗");
        two.add("张天爱");
        two.add("张三");
```



```java
//1：第一个队伍只要名字为3个字的成员姓名
one.stream().filter((name)->{
    return name.length()==3;
}).forEach(System.out::println);
```

```java
//2：第一个队伍筛选之后只要前3个人
one.stream().limit(3).forEach(System.out::println);
```

```java
//3：第二个队伍只要姓张的成员姓名
two.stream().filter((name)->{
    return name.startsWith("张");
}).forEach(System.out::println);
```

```java
//4：第二个队伍筛选之后不要前2个人
two.stream().skip(2).forEach(System.out::println);
```

```java
//5：将两个队伍合并为一个队伍
Stream<String> concat = Stream.concat(one.stream(), two.stream());
concat.forEach(System.out::println);
```



## 3.21:Stream->集合

Stream流提供 collect 方法，可以转化为集合

 public static  Collector> toList() ：转换为 List 集合

public static  Collector> toSet() ：转换为 Set 集合

```java
public class Test02 {
    public static void main(String[] args) {
        List<String> list = Stream.of("张三", "李四", "王五", "赵六").
        collect(Collectors.toList());
        
        Set<String> set = Stream.of("张三", "李四", "王五", "赵六").
        collect(Collectors.toSet());

        list.forEach(System.out::println);
        set.stream().forEach(System.out::println);
    }
}
```

## 3.22:Stream->数组

Stream提供 toArray 方法来将结果放到一个数组中，返回值类型是Object[]的

```java
Object[] toArray();
```

```java
public class Test02 {
    public static void main(String[] args) {
        Object[] objects = Stream.of("张三", "李四", "王五", "赵六").toArray();
        for (int i = 0; i < objects.length; i++) {
            System.out.println(objects[i]);
        }
    }
}
```

# 4:新的日期API

## 4.1:旧的存在的问题

旧版日期时间 API 存在的问题 

1：在 java.util 和 java.sql 的包中都有日期类，java.util.Date 同时包含日期和时间，而 java.sql.Date 仅包含日期

2：非线程安全：java.util.Date 是非线程安全的，所有的日期类都是可变的，这是Java日期类最大的问题之一

3：时区处理麻烦：日期类并不提供国际化，没有时区支持



## 4.2:新的API介绍

JDK 8中增加了一套全新的日期时间API，这套API设计合理，是线程安全的

新的日期及时间 API 位于 java.time 包 中，下面是一些关键类

LocalDate ：表示日期，包含年月日，格式为 2019-10-16 

LocalTime ：表示时间，包含时分秒，格式为 16:38:54.158549300 

LocalDateTime ：表示日期时间，包含年月日，时分秒，格式为 2018-09-06T15:33:56.750 

DateTimeFormatter ：日期时间格式化类

## 4.3:LocalDate

```java
// LocalDate 得到的日期格式是 2001-05-08
public class TestLocalDate {
    public static void main(String[] args) {
        // 创建指定日期
        LocalDate myDate = LocalDate.of(2001, 5, 8);
        System.out.println("自定义日期 = " + myDate);

        // 得到当前日期
        LocalDate nowDate = LocalDate.now();
        System.out.println("nowDate = " + nowDate);

        // 获取日期信息
        System.out.println("年: " + nowDate.getYear());
        System.out.println("月: " + nowDate.getMonthValue());
        System.out.println("日: " + nowDate.getDayOfMonth());
        System.out.println("星期: " + nowDate.getDayOfWeek());
    }
}
```

## 4.4:LocalTime

```java
// LocalTime 得到的时间格式为 16:38:54.158549300
public class TestLocalTime {
    public static void main(String[] args) {
        // 得到指定的时间【不加参数的话则不会显示】
        
        LocalTime myTime = LocalTime.of(12,12,12);  // 12:12:12
        LocalTime myTime = LocalTime.of(12,12);  // 12:12
        System.out.println("自定义时间 = " + myTime);

        // 得到当前时间
        LocalTime nowTime = LocalTime.now();
        System.out.println("nowTime = " + nowTime);

        // 获取时间信息
        System.out.println("小时: " + nowTime.getHour());
        System.out.println("分钟: " + nowTime.getMinute());
        System.out.println("秒: " + nowTime.getSecond());
        System.out.println("纳秒: " + nowTime.getNano());
    }
}
```

## 4.5:LocalDateTime

```java
// 得到的时间格式 2023-04-23T15:15:29.550929600
public class TestLocalDateTime {
    public static void main(String[] args) {
        LocalDateTime myDateTime = LocalDateTime.of(2001, 5, 8, 10, 10, 10);
        System.out.println("自定义日期时间 = " + myDateTime);

        // 得到当前日期时间
        LocalDateTime now = LocalDateTime.now();
        System.out.println("now = " + now);

        // 获取日期的信息
        System.out.println(now.getYear());
        System.out.println(now.getMonthValue());
        System.out.println(now.getDayOfMonth());
        System.out.println(now.getHour());
        System.out.println(now.getMinute());
        System.out.println(now.getSecond());
        System.out.println(now.getNano());
    }
}
```

## 4.6:DateTimeFormatter

通过 java.time.format.DateTimeFormatter 类可以进行日期时间解析与格式化

将日期格式为字符串：使用 LocalDateTime 的 format 方法

将字符串解析为日期：使用 LocalDateTime 的 parse 方法

```java
public class TestDateTimeFormatter {
    public static void main(String[] args) {
        // 得到当前日期时间
        LocalDateTime now = LocalDateTime.now();

        // 创建日期格式化对象并指定格式
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

        // 将日期时间格式化为字符串
        String format = now.format(formatter);
        System.out.println("format = " + format);

        // 将字符串解析为日期时间
        LocalDateTime parse = LocalDateTime.parse("1985-09-23 10:12:22", formatter);
        System.out.println("parse = " + parse);
    }
}
```

