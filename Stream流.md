### Stream流

直接上例子

```java
// 输出姓“张”的且名字为3个字的同学
public class StreamTest {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("张三");
        list.add("张无忌");
        list.add("李四");
        list.add("王五");
        list.add("张青美");
        list.add("哈哈哈");
        list.add("赵六");
        list.stream().filter(name -> name.startsWith("张"))
                .filter(name -> name.length() >= 3)
                .forEach(name -> System.out.println(name));
    }
}
```



#### Stream流的特性

```java
在Java 8中，得益于Lambda所带来的函数式编程，引入了一个全新的Stream概念，用于解决已有集合类库既有的弊端。

// Stream流的特性：
1. Stream流不是一种数据结构，其本身并不存储任何数据，它只是在原数据集上定义了一组操作.
2. 这些操作是惰性的，即每当访问到流中的一个元素，才会在此元素上执行这一系列操作.
3. Stream不保存数据，故每个Stream流只能使用一次.
 
// Stream流上的操作
1. 关于应用在Stream流上的操作，可以分成两种：Intermediate(中间操作)和Terminal(终止操作)。
2. 中间操作的返回结果都是Stream，故可以多个中间操作叠加；终止操作用于返回我们最终需要的数据，只能有一个终止操作。
    
// 获取流
所有的 Collection 集合都可以通过 stream 默认方法获取流
Stream 接口的静态方法 of 可以获取数组对应的流。

```



#### 获取流：

##### 1. Collection接口

```java
public static void main(String[] args) {
    Stream<Object> stream = new ArrayList<>().stream();
	Stream<Object> stream1 = new HashSet<>().stream();
}
```



##### 2. Map接口

```java
// 注意一点：Map接口不是Collection的子接口，所以有自己获取流的方式(需要分key,value,entry情况获取)
public static void main(String[] args) {
    Map<String, String> map = new HashMap<>();
    Stream<String> keyStream = map.keySet().stream();	// 获取key的流
    Stream<String> valueStream = map.values().stream();		// 获取value的流
    Stream<Map.Entry<String, String>> entryStream = map.entrySet().stream();//获取entry的流
}
```



##### 3. 数组

```java
public static void main(String[] args) {
    String[] array = { "张无忌", "张翠山", "张三丰", "张一元" };
    Stream<String> stream = Stream.of(array);
}
```





#### Stream流中常用方法

##### 中间操作流(Intermediate)

###### 1. filter过滤

```java
// 传入一个Predicate接口
Stream<T> filter(Predicate<? super T> predicate);
```

```java
// 举例：
public class Test4 {
    public static void main(String[] args) {
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若");
        Stream<String> result = original.filter(s -> s.startsWith("张"));
        // 等价于下面的两行代码:
        // Predicate<String> predicate = s -> s.startsWith("张");
        // Stream<String> result = stream.filter(predicate);
        result.forEach(System.out::println);
    }
}
```



###### 2. map映射

```java
// 如果需要将流中的元素映射到另一个流中，可以使用 map 方法
// 传入一个Function接口
<R> Stream<R> map(Function<? super T, ? extends R> mapper);
```

```java
public class Test4 {
    public static void main(String[] args) {
        Stream<String> original = Stream.of("10", "12", "18");
        Stream<Integer> result = original.map(str -> Integer.parseInt(str));
        // 等价于下面的两行代码:
        // Function<String, Integer> function = s -> Integer.parseInt(s);
        // Stream<Integer> result = original.map(function);
        result.forEach(System.out::println);
    }
}
```



###### 3. limit取前几个

```java
// 传入一个long型数字(int也可以，向下兼容)
Stream<T> limit(long maxSize);
```

```java
public class Test4 {
    public static void main(String[] args) {
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若");
        Stream<String> result = original.limit(2);
        result.forEach(s -> System.out.println(s));
        // result.forEach(System.out::println);
    }
}
```



###### 4. skip跳过几个

```java
Stream<T> skip(long n);		// 用法同上
```



###### 5. distinct剔除重复

```java
Stream<T> distinct();	// 用法同上
```



###### 6. generate无限流

```java
static <T> Stream<T> generate(Supplier<T> s)
```

```java
// 无限输出10以内的随机数， 可以配合limit取前几个
public class Test4 {
    public static void main(String[] args) {
        Stream.generate(() -> new Random().nextInt(10)).forEach(System.out::println);
    }
}
```



###### 7. concat组合

```java
static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b)
```

```java
public class Test4 {
    public static void main(String[] args) {
        Stream<String> streamA = Stream.of("张无忌");
        Stream<String> streamB = Stream.of("张翠山");
        Stream<String> result = Stream.concat(streamA, streamB);
        result.forEach(System.out::println);
    }
}
```



##### 终结流(Terminal)

###### 1. forEach遍历

```java
void forEach(Consumer<? super T> action)	// 见文本第一个例子 （传入一个Consumer）
```



###### 2. count计数

```java
long count()		// 返回流中元素个数
```









```java
Stream流的常用Intermediate方法(中间操作)
    1. filter
    2. map
    3. limit
    4. skip
    5. distinct
    forEach
```





#### 方法引用

```java
JDK8中有双冒号的用法，就是把方法当做参数传到stream内部，使stream的每个元素都传入到该方法里面执行一下。
```



定义MyInter函数式接口

```java
@FunctionalInterface
public interface MyInter {
    void print(String s);
}
```



测试类

```java
public class Test3{

    public static void myPrint(MyInter i) {
        i.print("Hello World!");
    }

    public static void main(String[] args) {
        myPrint(s -> System.out.println(s));
        myPrint(System.out::println);
    }
}
```



其中区别

```java
lambda表达式：
    1. 虚拟一个内部类实现MyInter接口，重写抽象方法print(String s)，里边的代码即为lambda传入的代码：System.out.println(s)
	2. 然后使用接口的引用调用实现类的方法：i.print("Hello World!");

::方法引用：
	1. 虚拟一个内部类实现MyInter接口，重写抽象方法print(String s)，里边的代码即为方法引用(其实际是调用的引用的方法(简单理解: 即相当于把引用方法的代码直接copy一份到重写的抽象方法中,但不是真正意义上的copy))
	2. 然后使用接口的引用调用实现类的方法：i.print("Hello World!");

* 其实：方法引用只不过是lambda表达式的一种骚写法，底层原理基本一致(lambda会在第一次编译(javac)的时候创建一个静态的方法，而方法引用则没有)，所以不必纠结用哪种，哪种顺手用哪种，看得懂就行~
```



































