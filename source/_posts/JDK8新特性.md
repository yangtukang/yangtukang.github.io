---
title: JDK8新特性
date: 2023-05-19 23:34:56
tags: [JDK8]
---

# JDK8新特性

## 函数编程（Lambda表达式）

### lambda表达式（不产生*.class）

lambda是一个匿名函数。lambda允许把函数作为一个方法的参数（函数作为参数传递进方法）

输入->输出 箭头将lambda表达式拆分成两部分

*   左侧：lambda的参数列表

*   右侧：lambda表达式所需要执行的功能

```java
//语法格式一：无参数、无返回值
()-> System.out.println("Hello");

//语法格式二：有一个参数、无返回值
(String s)->System.out.println(s);

//语法格式三：数据类型可以省略，由编译器自动推断得出，称为“类型推断”
(s)->System.out.println(s);
 
//语法格式四：只有一个参数，小括号可以省略不写
s->System.out.println(s);

//语法格式五：有多个参数，有返回值，并且lambda体中有多条语句
(x,y)->{
  System.out.println("函数式接口");
  return Integer.compare(x,y);
}

//语法格式六：若lambda体中只有一条语句，return和大括号都可以省略不写
Compartor<Integer> com = (x,y)->return Integer.compare(x,y);

```

### 函数式接口

*   lambda表达式的本质：作为函数式接口的实例
*   只包含一个抽象方法的接口，称为函数式接口
*   函数式接口可以被隐式转换为lambda表达式
*   我们可以在任意函数式接口上使用@FunctionInterface注解，这样做可以检查它是否是一个函数式接口，同时javadoc也会包含一条声明，说明这个接口是一个函数式接口

```java
@FunctionInterface
interface MyFunctionInterface{
  void sayMessage(String message);
}

MyFunctionInterface myFunctionInterface = message -> System.out.println("message: "+message);
myFunctionInterface.sayMessage("hhhhhhhh");
//message: hhhhhhhh
```

#### JAVA内置的四大核心函数式接口

| 函数式接口               | 参数类型 | 返回类型 | 用途                                                   | 包含方法          |
| :----------------------- | -------- | -------- | ------------------------------------------------------ | ----------------- |
| Consumer<T> 消费型接口   | T        | void     | 对类型为T的对象进行消费                                | void accept(T t)  |
| Suppiler<T> 供给型接口   | 无       | T        | 返回类型为T的对象                                      | T get()           |
| Function<T,R> 函数型接口 | T        | R        | 对类型为T的对象应用操作，并返回结果。结果是R类型的对象 | R apply()         |
| Predicate<T> 断定型接口  | T        | boolean  | 确定类型为T的对象是否满足其约束，并返回boolean值       | Boolean test(T t) |

#### 其他函数式接口

*   数据类型前缀：类型明确，不带泛型

*   Bi前缀：表示两个参数 
*   Operator：表示参数和返回值类型相同 

### 方法引用（不产生*.class）

*   方法引用本质上就是lambda表达式
*   当要传递给lambda体的操作已经有实现的方法了，可以使用方法引用
*   要求：实现接口的抽象方法的参数列表和返回值类型，必须与方法引用的方法的参数列表和返回值类型保持一致
*   格式：使用操作符`::`将方法名和对象或类的名字分割开

#### 使用情况

##### 实例方法引用

对象名::实例方法名

```java
String str = "任何方法都有对应的函数式接口";
char c = str.charAt(5);
System.out.println(c);
//有
Function<Integer, Character> integerCharacterFunction = str::charAt;
System.out.println(integerCharacterFunction.apply(5));
//有
```

##### 静态方法引用

类名::静态方法名

```java
//String.valueOf是String的静态方法
Comparator<Integer> com1 = (x, y)->Integer.compare(x,y);
//等价于
Comparator<Integer> com2 = Integer::compareTo;
```

##### 构造方法引用

类名::new（因为new出来的对象需要使用变量接收，可以看作new拥有返回值）

```java
//构造方法引用
Function<String, Student> studentNewFunction = Student::new;
Student student = studentNewFunction.apply("小红");
```

##### 对象方法引用

类名::实例方法名

```java
//对象方法引用
Function<Student, String> getName = Student::getName;
String name = getName.apply(student);
```

## StreamAPI

*   Stream API 对集合数据进行操作，就类似于SQL执行的数据库查询，Stream API主要是通过Lambda表达式完成

*   Stream 的工作流程就像流水线一样

    ![Stream流.drawio](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1202305231911570.png)

### 数值流（基本数据类型的流）

*   IntStream
*   LongStream
*   DoubleStream

#### 特有的方法

*   range（IntStream和LongStream特有）

*   rangeClosed（IntStream和LongStream特有）

*   sum
*   average

##### 数值数组-->数值流 

*   IntStream Arrays.stream(int[])
*   LongStream Arrays.stream(long[])
*   DobleStream Arrays.stream(double[])

### 测试数据

```java
  Student[] students = new Student[10];
  students[0] = new Student("小明", 18, "男");
  students[1] = new Student("小明", 18, "男");
  students[2] = new Student("小明", 18, "男");
  students[3] = new Student("小李", 16, "男");
  students[4] = new Student("小亮", 11, "男");
  students[5] = new Student("小红", 23, "女");
  students[6] = new Student("小美", 14, "男");
  students[7] = new Student("李倩", 17, "女");
  students[8] = new Student("小明", 34, "男");
  students[9] = new Student("花花", 12, "女");
```

### 中间操作

返回值：新的流供下一个操作使用（支持链式调用）

数量：0个或多个

#### 过滤

##### fifter

```java
//将所有男性筛选出来
Arrays.stream(students)
        .filter(student -> "男".equals(student.getSex()))
        .forEach(System.out::println);
//Student{name='小明', age=18, sex='男'}
//Student{name='小明', age=18, sex='男'}
//Student{name='小明', age=18, sex='男'}
//Student{name='小李', age=16, sex='男'}
//Student{name='小亮', age=11, sex='男'}
//Student{name='小美', age=14, sex='男'}
//Student{name='小明', age=34, sex='男'}
```

#### 去重

##### distinct

```java
//调用equals判断是否相等
Arrays.stream(students)
        .distinct()
        .forEach(System.out::println);
//Student{name='小明', age=18, sex='男'}
//Student{name='小李', age=16, sex='男'}
//Student{name='小亮', age=11, sex='男'}
//Student{name='小红', age=23, sex='女'}
//Student{name='小美', age=14, sex='男'}
//Student{name='李倩', age=17, sex='女'}
//Student{name='小明', age=34, sex='男'}
//Student{name='花花', age=12, sex='女'}
```

#### 排序

##### sorted

```java
//需要对象实现接口或者传入一个比较器
//流中的类实现Comparable接口
Arrays.stream(students)
        .sorted()
        .forEach(System.out::println);
//传入比较器
Arrays.stream(students)
        .sorted(new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                return o1.getAge()-o2.getAge();
            }
        })
        .forEach(System.out::println);
//使用lambda简化
Arrays.stream(students)
        .sorted((o1, o2) -> o1.getAge()-o2.getAge())
        .forEach(System.out::println);
//使用对象方法引用 Student::getAge（类名::实例方法）
Arrays.stream(students)
        .sorted(Comparator.comparingInt(Student::getAge))
        .forEach(System.out::println);
//Student{name='小亮', age=11, sex='男'}
//Student{name='花花', age=12, sex='女'}
//Student{name='小美', age=14, sex='男'}
//Student{name='小李', age=16, sex='男'}
//Student{name='李倩', age=17, sex='女'}
//Student{name='小明', age=18, sex='男'}
//Student{name='小明', age=18, sex='男'}
//Student{name='小明', age=18, sex='男'}
//Student{name='小红', age=23, sex='女'}
//Student{name='小明', age=34, sex='男'}
```

#### 截取

##### limit （可以截取无限流）

```java
//限制个数
Arrays.stream(students)
        .limit(4)
        .forEach(System.out::println);
//Student{name='小明', age=18, sex='男'}
//Student{name='小明', age=18, sex='男'}
//Student{name='小明', age=18, sex='男'}
//Student{name='小李', age=16, sex='男'}
```

##### skip

```java
//跳过指定个数的数据
Arrays.stream(students)
        .skip(3)
        .forEach(System.out::println);
//Student{name='小李', age=16, sex='男'}
//Student{name='小亮', age=11, sex='男'}
//Student{name='小红', age=23, sex='女'}
//Student{name='小美', age=14, sex='男'}
//Student{name='李倩', age=17, sex='女'}
//Student{name='小明', age=34, sex='男'}
//Student{name='花花', age=12, sex='女'}
```

#### 合并

##### concat（静态方法）

```java
//将两个流进行拼接
Stream.concat(Arrays.stream(students),Arrays.stream(students))
        .forEach(System.out::println);
//Student{name='小明', age=18, sex='男'}
//Student{name='小明', age=18, sex='男'}
//Student{name='小明', age=18, sex='男'}
//Student{name='小李', age=16, sex='男'}
//Student{name='小亮', age=11, sex='男'}
//Student{name='小红', age=23, sex='女'}
//Student{name='小美', age=14, sex='男'}
//Student{name='李倩', age=17, sex='女'}
//Student{name='小明', age=34, sex='男'}
//Student{name='花花', age=12, sex='女'}

//Student{name='小明', age=18, sex='男'}
//Student{name='小明', age=18, sex='男'}
//Student{name='小明', age=18, sex='男'}
//Student{name='小李', age=16, sex='男'}
//Student{name='小亮', age=11, sex='男'}
//Student{name='小红', age=23, sex='女'}
//Student{name='小美', age=14, sex='男'}
//Student{name='李倩', age=17, sex='女'}
//Student{name='小明', age=34, sex='男'}
//Student{name='花花', age=12, sex='女'}
```

#### 映射

##### map（一对一映射）

```java
//将Student对象转位String Student::getName 对象方法引用（类名::实例方法）
Arrays.stream(students)
        .map(Student::getName)
        .forEach(System.out::println);
```

##### mapToXXXX（转数值流）

```java
//得到的对象是对应的数值流 Student::getAge 对象方法引用（类名::实例方法）
IntStream intStream = Arrays.stream(students)
        .mapToInt(Student::getAge);
intStream.forEach(System.out::println);
//18
//18
//18
//16
//11
//23
//14
//17
//34
//12
```

##### flatMap（一对多）

#### 执行操作

##### peek（对流中每个元素执行特定的操作，但不改变当前流，可以改变流当中的元素）

```java
Arrays.stream(students)
        .distinct()
        .peek(e -> System.out.println("我是" + e.getName()))
        .peek(e->e.setAge(e.getAge()+10))
        .forEach(System.out::println);
//我是小明
//Student{name='小明', age=28, sex='男'}
//我是小明
//Student{name='小明', age=28, sex='男'}
//我是小明
//Student{name='小明', age=28, sex='男'}
//我是小李
//Student{name='小李', age=26, sex='男'}
//我是小亮
//Student{name='小亮', age=21, sex='男'}
//我是小红
//Student{name='小红', age=33, sex='女'}
//我是小美
//Student{name='小美', age=24, sex='男'}
//我是李倩
//Student{name='李倩', age=27, sex='女'}
//我是小明
//Student{name='小明', age=44, sex='男'}
//我是花花
//Student{name='花花', age=22, sex='女'}
```



### 终止操作

返回值：不是Stream

数量：只能有一个

遇到终止操作时，流才开始工作（延迟计算）

终止操作后流不可用（不可重用）

#### 循环

##### forEach

```java
Arrays.stream(students)
        .forEach(System.out::println);
//Student{name='小明', age=28, sex='男'}
//Student{name='小明', age=28, sex='男'}
//Student{name='小明', age=28, sex='男'}
//Student{name='小李', age=26, sex='男'}
//Student{name='小亮', age=21, sex='男'}
//Student{name='小红', age=33, sex='女'}
//Student{name='小美', age=24, sex='男'}
//Student{name='李倩', age=27, sex='女'}
//Student{name='小明', age=44, sex='男'}
//Student{name='花花', age=22, sex='女'}
```

#### 聚合计算（Stream返回的结果是Optional）

##### min

```java
//需要传入一个比较器
Optional<Student> min = Arrays.stream(students)
        .min(new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                return o1.getAge() - o2.getAge();
            }
        });
//使用lambda表达式简化
Optional<Student> min = Arrays.stream(students)
        .min((o1, o2) -> o1.getAge() - o2.getAge());
//使用对象方法引用 Student::getAge（类名::实例方法）
Optional<Student> min = Arrays.stream(students)
        .min(Comparator.comparingInt(Student::getAge));
System.out.println(min.get());
//Student{name='小亮', age=11, sex='男'}
```

##### max

```java
//需要传入一个比较器
Optional<Student> min = Arrays.stream(students)
        .max(new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                return o1.getAge() - o2.getAge();
            }
        });
//使用lambda表达式简化
Optional<Student> min = Arrays.stream(students)
        .max((o1, o2) -> o1.getAge() - o2.getAge());
//使用对象方法引用 Student::getAge（类名::实例方法）
Optional<Student> max = Arrays.stream(students)
        .max(Comparator.comparingInt(Student::getAge));
System.out.println(max.get());
//Student{name='小明', age=34, sex='男'}
```

##### sum （需要为数值流）

```java
//要先将Streamliu转换为IntStream
int sum = Arrays.stream(students)
        .mapToInt(Student::getAge)
        .sum();
System.out.println(sum);
//181
```

##### average（需要为数值流）

```java
OptionalDouble average = Arrays.stream(students)
        .mapToInt(Student::getAge)
        .average();
System.out.println(average);
//OptionalDouble[18.1]
System.out.println(average.getAsDouble());
//18.1
```

#### 匹配查找

##### anyMatch

```java
//只要有一个满足就为true
boolean b = Arrays.*stream*(students)
        .anyMatch(new Predicate<Student>() {
            @Override
            public boolean test(Student student) {
                return student.getAge() > 30;
            }
        });
//使用lambda简化
boolean b = Arrays.stream(students)
        .anyMatch(student -> student.getAge() > 30);
System.out.println(b);
//true
```

##### allMatch

```java
//全部匹配才为true
boolean b = Arrays.*stream*(students)
        .allMatch(new Predicate<Student>() {
            @Override
            public boolean test(Student student) {
                return student.getAge() > 30;
            }
        });
//使用lambda简化
boolean b = Arrays.stream(students)
        .allMatch(student -> student.getAge() > 30);
System.out.println(b);
//false
```

##### noneMatch

```java
//全部不匹配才为true
boolean b = Arrays.*stream*(students)
        .noneMatch(new Predicate<Student>() {
            @Override
            public boolean test(Student student) {
                return student.getAge() > 30;
            }
        });
//使用lambda简化
boolean b = Arrays.stream(students)
        .noneMatch(student -> student.getAge() > 30);
System.out.println(b);
//false
```

##### findFirst

```java
//返回流中第一个元素
Optional<Student> first = Arrays.stream(students)
        .findFirst();
System.out.println(first);
//Optional[Student{name='小明', age=18, sex='男'}]
```

##### findAny

```java
//串行地情况下，一般会返回第一个结果，如果是并行的情况，那就不能确保是第一个
Optional<Student> first = Arrays.stream(students)
        .findFirst();
System.out.println(first);
//Optional[Student{name='小明', age=18, sex='男'}]
```

#### 转换

##### toArray

```java
//返回的是Object[]
Stream<Student> stream = Arrays.stream(students);
Object[] array = stream.toArray();
```

#### 收集

##### collect

```java
//Stream -> R
//Collectors.toList();
List<Student> studentList = Arrays.stream(students).collect(Collectors.toList());
System.out.println(studentList);
//[Student{name='小明', age=18, sex='男'}, Student{name='小明', age=18, sex='男'}, Student{name='小明', age=18, sex='男'}, Student{name='小李', age=16, sex='男'}, Student{name='小亮', age=11, sex='男'}, Student{name='小红', age=23, sex='女'}, Student{name='小美', age=14, sex='男'}, Student{name='李倩', age=17, sex='女'}, Student{name='小明', age=34, sex='男'}, Student{name='花花', age=12, sex='女'}]

//Collectors.toSet();
Set<Student> studentSet = Arrays.stream(students).collect(Collectors.toSet());
System.out.println(studentSet);
//[Student{name='花花', age=12, sex='女'}, Student{name='小李', age=16, sex='男'}, Student{name='小明', age=18, sex='男'}, Student{name='小亮', age=11, sex='男'}, Student{name='小红', age=23, sex='女'}, Student{name='小明', age=34, sex='男'}, Student{name='小美', age=14, sex='男'}, Student{name='李倩', age=17, sex='女'}]

//Collectors.toMap() 需要双列，即flatMap
```

#### 迭代计算

##### reduce

```java
//将上一次的结果作为下一次计算的输入 Stream<T> -> T
Optional<Integer> reduce = Arrays.stream(students)
        .map(Student::getAge)
        .reduce((x, y) -> x + y);
System.out.println(reduce);
//Optional[181]
```

## Optional类

Optional类是一个容器类，代表一个值或不存在，原来null表示一个值不存在，现在用Optional可以更好的表达这个概念

Optional类主要解决的就是空指针异常

### 常用方法

```java
//创建一个Optional对象
Optional<Integer> integer = Optional.of(10); 
Optional<Integer> integer = Optional.of(null); //传入值为null或抛出 java.lang.NullPointerException 异常

//创建一个空的Optional对象
Optional<Integer> empty = Optional.empty();

//若传入的值为null 返回一个Optional.empty 对象
Optional<Integer> notNull = Optional.ofNullable(11);

//判断是否包含值
empty.isPresent();
//false
notNull.isPresent();
//true

//若包含的对象包含值，返回该值，否则返回指定的值
notNull.orElse(20);
//11
empty.orElse(20);
//20

//若包含的对象包含值，返回该值，否则返回指定的s提供的值
empty.orElseGet(new Supplier<Integer>() {
            @Override
            public Integer get() {
                return 11;
            }
        });
//11
empty.orElseGet(new Supplier<Integer>() {
            @Override
            public Integer get() {
                return 11;
            }
        });
//20

//如果有值对其进行映射 返回映射的值 否则返回 Optional.empty 对象
Optional<String> s = student.map(Student::getName);

//flatMap与map类似
```



## 接口的默认方法和静态方法

## 新的日期API（不可变对象）

![日期类.drawio](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1202305221947246.png)

java8中java.time包中的类是不可变且线程安全的。

*   Instant：它代表的是时间戳

*   LocalDate：不包含具体的日期
*   LocalTime：它代表的是不含日期的时间
*   LocalDataTime：它包含了日期及时间，没有时区
*   ZonedDataTime：包含时区的完整的日期时间，偏移量以UTC/格林威治时间为基准

### 方法概述

*   of：静态方法工厂
*   parse：静态工厂方法，关注于解析
*   get：获取某些时间量的值
*   with：不可变setter的等价物 返回一个新的时间对象
*   plus：为当前时间增加一个时间量 返回一个新的时间对象
*   minus：为当前时间减少一个时间量 返回一个新的时间对象
*   to：转换到另一个类型
*   at：把这个对象与另一个对象组合起来

### 创建实例

#### 根据系统当前时间创建对象

##### LocalDataTime

```java
LocalDateTime dateTime = LocalDateTime.now();
System.out.println(dateTime);
//2023-05-22T20:08:13.387
```

##### LocalData (年、月、日)

```java
LocalDate date = LocalDate.now();
System.out.println(date);
//2023-05-22
```

##### LocalTime（时、分、秒）

```java
LocalTime time = LocalTime.now();
System.out.println(time);
//20:08:13.388
```

#### 指定时间创建对象

##### LocalDataTime

```java
LocalDateTime dateTime = LocalDateTime.of(2020,1,3,14,9,9);
System.out.println(dateTime);
//2020-01-03T14:09:09
```

##### LocalData (年、月、日)

```java
//年、月、日
LocalDate date = LocalDate.of(2001,1,2);
System.out.println(date);
//2001-01-02
```

##### LocalTime（时、分、秒）

```java
//时 、分、秒、纳秒
LocalTime time = LocalTime.of(10,23,22,330000);
System.out.println(time);
//10:23:22.000330
```

#### 读取字段

##### LocalDataTime

```java
LocalDateTime dateTime = LocalDateTime.of(2020,1,3,14,9,9);
System.out.println(dateTime);
//2020-01-03T14:09:09
//===LocalData=============================================
System.out.println("Year: "+dateTime.getYear());
//Year: 2020
System.out.println("Month: "+dateTime.getMonth());
//Month: JANUARY
System.out.println("MonthValue: "+dateTime.getMonthValue());
//MonthValue: 1
System.out.println("DayOfYear: "+dateTime.getDayOfYear());
//DayOfYear: 3
System.out.println("DayOfMonth: "+dateTime.getDayOfMonth());
//DayOfMonth: 3
System.out.println("DayOfWeek: "+dateTime.getDayOfWeek());
//DayOfWeek: FRIDAY
//===LocalTime=============================================
System.out.println("Hour: "+dateTime.getHour());
//Hour: 14
System.out.println("Minute: "+dateTime.getMinute());
//Minute: 9
System.out.println("Second: "+dateTime.getSecond());
//Second: 9
```

#### 修改字段

##### LocalDataTime

```java
LocalDateTime dateTime = LocalDateTime.of(2020,1,3,14,9,9);
System.out.println(dateTime);
//================================
//｜plus｜ 在这个时间上添加一个时间的值
//================================
//2020-01-03T14:09:09
LocalDateTime dateTime1 = dateTime.plusYears(1);
//===LocalData=============================================
//不可变性 修改后返回一个修改后的时间 原时间不变
System.out.println(dateTime);//原来的时间变量 数值没有变化
//2020-01-03T14:09:09
System.out.println(dateTime1);//返回的新的变量
//2021-01-03T14:09:09
//溢出的时间单位会自动进位转换
System.out.println(dateTime.plusMonths(13));
//2021-02-03T14:09:09
System.out.println(dateTime.plusWeeks(5));
//2020-02-07T14:09:09
System.out.println(dateTime.plusDays(7));
//2020-01-10T14:09:09
//===LocalTime=============================================
System.out.println(dateTime.plusHours(25));
//2020-01-04T15:09:09
System.out.println(dateTime.plusMinutes(3));
//2020-01-03T14:12:09
System.out.println(dateTime.plusSeconds(6));
//2020-01-03T14:09:15

//=================================
//｜with｜ 为这个时间重新设置一个时间的值
//=================================
LocalDateTime dateTime = LocalDateTime.of(2020,1,3,14,9,9);
System.out.println(dateTime);
//2020-01-03T14:09:09
//===LocalData=============================================
System.out.println(dateTime.withYear(2022));
//2022-01-03T14:09:09
System.out.println(dateTime.withMonth(12));
//2020-12-03T14:09:09
System.out.println(dateTime.plusDays(29));
//2020-02-01T14:09:09
//===LocalTime=============================================
System.out.println(dateTime.withHour(22));
//2020-01-03T22:09:09
System.out.println(dateTime.plusMinutes(30));
//2020-01-03T14:39:09
System.out.println(dateTime.withSecond(24));
//2020-01-03T14:09:24
//支持链式调用
System.out.println(dateTime.withYear(2022).withMonth(12).withHour(20));
//2022-12-03T20:09:09
//添加的时间超出范围会抛异常
System.out.println(dateTime.withHour(25));
//java.time.DateTimeException: Invalid value for HourOfDay (valid values 0 - 23): 25
```

#### 转换

```java
LocalDateTime dateTime = LocalDateTime.of(2020, 1, 3, 14, 9, 9);
System.out.println(dateTime);
//2020-01-03T14:09:09
LocalDate date = LocalDate.from(dateTime);
System.out.println(date);
System.out.println(dateTime.toLocalDate());
//2020-01-03
LocalTime time = LocalTime.from(dateTime);
System.out.println(time);
System.out.println(dateTime.toLocalTime());
//14:09:09

//只能从大往小转 
//LocalDateTime->LocalDate
//LocalDateTime->LocalTime

//LocalDate->LocalTime 抛出异常
//LocalTime->LocalDateTime 抛出异常

```

##### LocalData (年、月、日)

```java
//添加字段
LocalDate date = LocalDate.of(2001,1,2);
System.out.println(date);
//2001-01-02
LocalTime time = LocalTime.of(10,23);
System.out.println(date.atTime(time));
//2001-01-02T10:23
//时、分、秒
System.out.println(date.atTime(10, 8,33));
//2001-01-02T10:08:33
```

#### 格式化

```java
LocalDateTime dateTime = LocalDateTime.of(2020, 1, 3, 14, 9, 5);
//用常量表示的常用的日期格式（基于ISO标准）
System.out.println(DateTimeFormatter.ISO_LOCAL_DATE_TIME.format(dateTime));
//2020-01-03T14:09:05

//自定义格式化日期格式
String pattern = "公元yyyy年的第DD天HH时mm分ss秒";
DateTimeFormatter formatter = DateTimeFormatter.ofPattern(pattern);
System.out.println(formatter.format(dateTime));
//公元2020年的第03天14时09分05秒

//使用系统的日期格式
System.out.println(DateTimeFormatter.ofLocalizedDate(FormatStyle.FULL).format(dateTime));
System.out.println(DateTimeFormatter.ofLocalizedDate(FormatStyle.LONG).format(dateTime));
//2020年1月3日 星期五
//2020年1月3日
```

#### 与Data转换

```java
//Date -> LocalDateTime
Date date = new Date();
System.out.println(date);
//Mon May 22 21:41:51 CST 2023

Instant instant = date.toInstant();
ZoneId zoneId = ZoneId.systemDefault();

LocalDateTime localDateTime = instant.atZone(zoneId).toLocalDateTime();
System.out.println(localDateTime);
//2023-05-22T21:41:51.217

//LocalDateTime -> Date
ZoneId zoneId = ZoneId.systemDefault();
LocalDateTime localDateTime = LocalDateTime.now();
System.out.println(localDateTime);
//2023-05-22T21:45:24.921

ZonedDateTime zonedDateTime = localDateTime.atZone(zoneId);
Date date = Date.from(zonedDateTime.toInstant());
System.out.println(date);
//Mon May 22 21:45:24 CST 2023
```

#### 时间之间的计算

```java
LocalDate date1 = LocalDate.of(2000,10,10);
LocalDate date2 = LocalDate.of(2021,1,1);
System.out.println(date1.until(date2, ChronoUnit.YEARS));
System.out.println(date2.until(date1, ChronoUnit.YEARS));
//20
//-20
```

#### TemporalAdjuster 时间调节器

只有一个`adjustInto`方法 封装调整时间的计算规则

#### TemporalAdjusters 工具类

```java
LocalDate date = LocalDate.of(2000,10,10);
TemporalAdjuster temporalAdjuster = TemporalAdjusters.firstDayOfMonth();//返回时间调节器
System.out.println(date.with(temporalAdjuster));
//2000-10-01

temporalAdjuster = TemporalAdjusters.next(DayOfWeek.MONDAY);
System.out.println(date.with(temporalAdjuster));
//2000-10-16
```

## JavaFX

## 注解
