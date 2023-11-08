---
title: JAVA基础-面向对象
date: 2023-05-09 18:37:47
tags: [java,面向对象]
---

# JAVA基础-面向对象

## 三大特性

### 封装

利用抽象数据类型和基于数据类型的操作封装在一起，使其构成一个不可分隔的独立实体。数据被保护在抽象数据类型的内部，尽可能地隐藏内部的细节，只保留一些对外接口使之外部发生联系。用户无需知道对象内部的细节，但可以通过对外提供的接口来访问改对象。

#### 优点：

*   减少耦合：可以独立地开发、测试、优化、使用、理解和修改
*   减轻维护的负担：可以更容易被程序员理解，并且在调试的时候可以不影响其他模块
*   有效地调节性能：可以通过剖析确定哪些模块影响了系统的性能
*   提高软件的可重用行
*   降低了构建大型项目的风险：即整个系统不可用，但是这些独立的模块却有可能是可用的

Person类中封装了name, gender,age等属性，外界只能通过get()方法获取Person对象的name、gender属性无法获取age属性，但age属性可以供work()方法使用。

```java
public class Person {
    private String name;
    private int gender;
    private int age;

    public Person() {
    }

    public Person(String name, int gender, int age) {
        this.name = name;
        this.gender = gender;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public String getGender() {
        return gender == 0 ? "man" : "woman";
    }

    public void work(){
        if(18 <= age && age <= 50){
            System.out.println(name+"is working very heard!");
        }else{
            System.out.println(name + "can't work any more!");
        }
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Person xiaoming = new Person("小明", 0, 24);
        xiaoming.work();
        System.out.println(xiaoming.getGender());
        Person xiaogang = new Person("小刚",1,77);
        xiaogang.work();
        System.out.println(xiaogang.getGender());
    }
}
```

```out
小明is working very heard!
man
小刚can't work any more!
woman
```



### 继承

继承实现了IS-A关系，例如Cat和Animal就是一种IS-A的关系，因此Cat可以继承自Animal，从而获得Animal非private的属性和方法。

继承时应当遵守里氏替换原则，子类必须能够替换掉所有父类对象。

Cat可以当作Animal来使用，也就是说可以使用Animal引用Cat对象。

父类引用指向子类对象成为**向上转型**(向上转型是安全的，会失去子类扩充的属性和方法)。

```java
public class Animal {
    private String name;
}
```

```java
public class Cat extends Animal {
}
```

```java
public class Main {
    public static void main(String[] args) {
        Animal cat = new Cat();
    }
}
```

### 多态

多态分为编译时多态和运行时多态：

*   编译时多态主要指方法的重载
*   运行时多态指程序中定义的对象引用所指向的具体类型在运行期间才能确定

#### 	运行时多态有三个条件

*   继承
*   重写(重写)
*   向上转型

​	乐器类(Instrument)有两个子类：Wind和Percussion，他们都实现了play()方法，并且在main()方法中使用Instrument来引用Wind和Percussion对象。在Instrument引用中调用play()方法时，会执行引用对象所在类的play()方法，而不是Instrument类的方法。

```java
public class Instrument {
    public void play(){
        System.out.println("Instrument is playing...");
    }
}
```

```java
public class Wind extends Instrument{
    @Override
    public void play() {
        System.out.println("Wind is playing...");
    }
}
```

```java
public class Percussion extends Instrument{
    @Override
    public void play() {
        System.out.println("Percussion is playing...");
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        List<Instrument> instruments = new ArrayList<>();
        instruments.add(new Instrument());
        instruments.add(new Wind());
        instruments.add(new Percussion());
        for (Instrument instrument : instruments) {
            instrument.play();
        }
    }
}
```

```out
Instrument is playing...
Wind is playing...
Percussion is playing...
```



### 类图

#### 泛化关系

用来描述继承关系，在JAVA中使用extends关键字

![image-20230509220023910](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1/202305092200035.png)

#### 实现关系

用来实现一个接口，在JAVA中使用implements关键字

![image-20230509223500411](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1/202305092245104.png)



#### 聚合关系

表示整体有部分组成，但整体和部分不是强依赖的，整体不存在了部分还是会存在

![image-20230509223833343](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1/202305092245859.png)

#### 组合关系

和聚合关系不同，组合中整体和部分是强依赖的，整体不存在了部分也不存在了。比如公司部门，公司没了部门就不存在了。但是公司和员工又属于聚合关系，因为公司没了员工还在

![image-20230509224509365](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1/202305092245925.png)

#### 关联关系

表示不同对象之间的关联，这是一种静态关系，与运行过程的状态无关，在最开始就可以确定。因此也可以使用1对1、多对多这种关联来表示。比如和学校就是一关联关系，一个学校可以有很多学生，但一个学生只属于一个学校，因此这是一种多对一的关系，在运行开始之前就可以确定

![image-20230509225127502](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1/202305092251924.png)

#### 依赖关系

和关联关系不同得是，依赖关系是在运行过程中起作用的。A类和B类是依赖关系主要有三种形式

* A类是B类中的(某个方法中的)局部变量

* A类是B类方法当中的一个参数

* A类向B类发送消息，从而影响B类发生变化

  ![image-20230509225744317](https://ytk-imgs.oss-rg-china-mainland.aliyuncs.com/imgs1/202305092257348.png)

# 相关链接：

UML类图绘制：https://plantuml.com/zh/

Java全栈博客：https://pdai.tech/md/java/basic/java-basic-oop.html
