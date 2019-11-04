---
title: Java常见基础知识汇总
date: 2019-06-25 20:07:29
tags: [面试,Java]
categories: [面试,Java]
photos:
  - "https://github.com/LostCherry/lostcherry.github.io/blob/master/images/cover/1.jpg?raw=true"
description: Java面试和笔试中遇到的一些基础知识，总是忘，记录一下用以复习。
---

# Java常见基础知识汇总

## 1. sleep()与wait()区别

1. **锁**：sleep()方法正在执行的线程主动让出CPU（然后CPU就可以去执行其他任务），在sleep指定时间后CPU再回到该线程继续往下执行(<font color='red'>注意：sleep方法只让出了CPU，而并不会释放同步资源锁！！！</font>)；wait()方法则是指当前线程让自己暂时退让出同步资源锁，以便其他正在等待该资源的线程得到该资源进而运行，只有调用了notify()方法，之前调用wait()的线程才会解除wait状态，可以去参与竞争同步资源锁，进而得到执行。
2. **使用的位置**：sleep()方法可以在任何地方使用；wait()方法则只能在同步方法或同步块中使用；
3. **属于的类**：sleep()是线程类（Thread）的方法，调用会暂停此线程指定的时间，但监控依然保持，不会释放对象锁，到时间自动恢复；wait()是Object的方法，调用会放弃对象锁，进入等待队列，待调用**notify()/notifyAll()**唤醒指定的线程或者所有线程，才会进入锁池，不再次获得对象锁才会进入运行状态；

## 2. HashSet和TreeSet的区别

1. **实现方式**：HashSet是采用hash表来实现的；TreeSet是采用树结构实现(红黑树算法)。
2. **排列顺序**：HashSet无序；TreeSet支持两种排序方式，自然排序和定制排序（实现comparable接口）。
3. **复杂度**：HashSet方法都是复杂度为O(1)；TreeSet方法都是复杂度为O(log (n))的方法。
4. **判断方法**：HashSet判断通过equals方法比较相等，并且两个对象的hashCode()方法返回值相等（需要重写hashCode（）和equals()方法）；TreeSet通过equals方法返回，或者通过CompareTo方法比较；
5. **实现接口**：HashSet实现Set接口；TreeSet实现SortedSet接口，SortedSet实现Set接口。

备注：**LinkedHashSet**介于HashSet和TreeSet之间。它也是一个hash表，但是同时维护了一个双链表来记录插入的顺序。基本方法的复杂度为O(1)。

## 3. int和Integer的区别

### 1. 区别概述

1. **数据类型**：Integer是int的包装类，int则是java的一种基本数据类型；
2. **使用方法**：Integer变量必须实例化后才能使用，而int变量不需要；
3. **默认值**：Integer 是类，默认值为null，int是基本数据类型，默认值为0；
4. **实质**：Integer实际是对象的引用，当new一个Integer时，实际上是生成一个指针指向此对象；而int则是直接存储数据值。

### 2. 自动装箱和自动拆箱、缓存

自动拆箱和自动装箱是 JDK1.5 以后才有的功能，也就是java当中众多的语法糖之一，它的执行是在编译期，会根据代码的语法，在生成class文件的时候，决定是否进行拆箱和装箱动作。

#### 1. 自动装箱

对于

```java
Integer a = 128;
```



生成class文件时，

```java
Integer a = Integer.valueOf(128);
```

#### 2. 自动拆箱

将 Integer 类表示的数据赋值给基本数据类型int，就执行了自动拆箱。

```java
  Integer a = new Integer(128);
  int m = a;
```

class文件：

```java
Integer a = new Integer(128);
int m = a.intValue();
```

简单来讲：自动装箱就是`Integer.valueOf(int i)`；自动拆箱就是 `i.intValue()`；

#### 3. 缓存

 当`Integer b1 = 12`时，是在常量池中开辟出同一个空间来存储12，在常量池中就必须对其的大小范围做出一个规定，规定其范围为：（-128~127）之间。如果超出了范围，会从堆区new一个Integer对象来存放值。

### 3. `==`的比较

#### 1. Integer与Integer 

由于Integer变量实际上是对一个Integer对象的引用，是对象的地址，所以两个通过new生成的Integer变量永远是不相等的。

```java
Integer i = new Integer(100);
Integer j = new Integer(100);
System.out.print(i == j); //false
```

#### 2. Integer与int

Integer与int比较时，会触发自动拆箱，实际为两个int的比较，因此，结果为true。

```java
Integer i = new Integer(100);
int j = 100;
System.out.print(i == j); //true
```

#### 3. 非new生成的Integer变量和new Integer()生成的变量

因为非new生成的Integer变量指向的是java常量池中的对象，而new Integer()生成的变量指向堆中新建的对象，两者在内存中的地址不同，结果为false。

```java
Integer i = new Integer(100);
Integer j = 100;
System.out.print(i == j); //false
```

#### 4. 对于两个非new生成的Integer对象

如果在缓存范围之内，是指向同一个常量池中的地址，所以是true；如果在缓存范围之外，会new一个对象，内存地址不同，所以为false。

```java
Integer i = 100;
Integer j = 100;
System.out.print(i == j); //true
```

```java
Integer i = 128;
Integer j = 128;
System.out.print(i == j); //false
```

#### 5. 算术运算

由于 a+b包含了算术运算，因此会触发自动拆箱过程（会调用intValue方法）,==比较符又将左边的自动拆箱，因此它们比较的是数值是否相等。

equals()会先触发自动装箱，**equals 运算符不会进行类型转换**，Long.equals(Integer)，结果将是false。

```java
Integer a = 1;
Integer b = 2;
Integer c = 3;
System.out.println(c == (a + b)); //true
System.out.println(c.equals((a+b))); //true
```



## 4. 常量池中的String

Java中的常量池，实际上分为两种形态：**静态常量池**和**运行时常量池**。

### 1. 静态常量池

即*.class文件中的常量池，class文件中的常量池不仅仅包含字符串(数字)字面量，还包含类、方法的信息，占用class文件绝大部分空间。这种常量池主要用于存放两大类常量：**字面量**(Literal)和**符号引用量**(Symbolic References)，字面量相当于Java语言层面常量的概念，如文本字符串，声明为final的常量值等，符号引用则属于编译原理方面的概念，包括了如下三种类型的常量：

- 类和接口的全限定名
- 字段名称和描述符
- 方法名称和描述符

### 2.  运行时常量池

是jvm虚拟机在完成类装载操作后，将class文件中的常量池载入到内存中，并保存在**方法区**中，我们常说的常量池，就是指方法区中的运行时常量池。

### 3. String类的intern() 

String的intern()方法会查找在常量池中是否存在一份equal相等的字符串,如果有则返回该字符串的引用,如果没有则添加自己的字符串进入常量池。

### 4. 实例比较

```java
String s1 = "Hello";
String s2 = "Hello";
String s3 = "Hel" + "lo";
String s4 = "Hel" + new String("lo");
String s5 = new String("Hello");
String s6 = s5.intern();
String s7 = "H";
String s8 = "ello";
String s9 = s7 + s8;
          
System.out.println(s1 == s2);  // true
System.out.println(s1 == s3);  // true
System.out.println(s1 == s4);  // false
System.out.println(s1 == s9);  // false
System.out.println(s4 == s5);  // false
System.out.println(s1 == s6);  // true
```

 首先说明一点，在java 中，直接使用==操作符，比较的是两个字符串的引用地址，并不是比较内容，比较内容请用String.equals()。

 s1 == s2这个非常好理解，s1、s2在赋值时，均使用的字符串字面量，说白话点，就是直接把字符串写死，在编译期间，这种字面量会直接放入class文件的常量池中，从而实现复用，载入运行时常量池后，s1、s2指向的是同一个内存地址，所以相等。

s1 == s3这个地方有个坑，s3虽然是动态拼接出来的字符串，但是所有参与拼接的部分都是已知的字面量，在编译期间，这种拼接会被优化，编译器直接帮你拼好，因此String s3 = "Hel" + "lo";在class文件中被优化成String s3 = "Hello"，所以s1 == s3成立。只有使用引号包含文本的方式创建的String对象之间使用“+”连接产生的新对象才会被加入字符串池中。

s1 == s4当然不相等，s4虽然也是拼接出来的，但new String("lo")这部分不是已知字面量，是一个不可预料的部分，编译器不会优化，必须等到运行时才可以确定结果，结合**字符串不变**定理，鬼知道s4被分配到哪去了，所以地址肯定不同。对于所有包含new方式新建对象（包括null）的“+”连接表达式，它所产生的新对象都不会被加入字符串池中。

s1 == s9也不相等，道理差不多，虽然s7、s8在赋值的时候使用的字符串字面量，但是拼接成s9的时候，s7、s8作为两个变量，都是不可预料的，编译器毕竟是编译器，不可能当解释器用，不能在编译期被确定，所以不做优化，只能等到运行时，在堆中创建s7、s8拼接成的新字符串，在堆中地址不确定，不可能与方法区常量池中的s1地址相同。

![常量池中的String](/image/java/常量池中的String.png)

s4 == s5已经不用解释了，绝对不相等，二者都在堆中，但地址不同。

s1 == s6这两个相等完全归功于intern方法，s5在堆中，内容为Hello ，intern方法会尝试将Hello字符串添加到常量池中，并返回其在常量池中的地址，因为常量池中已经有了Hello字符串，所以intern方法直接返回地址；而s1在编译期就已经指向常量池了，因此s1和s6指向同一地址，相等。

```java
public static final String A = "ab"; // 常量A
public static final String B = "cd"; // 常量B
public static void main(String[] args) {
     String s = A + B;  // 将两个常量用+连接对s进行初始化 
     String t = "abcd";   
    if (s == t) {   
         System.out.println("s等于t，它们是同一个对象");   
     } else {   
         System.out.println("s不等于t，它们不是同一个对象");   
     }   
 } 
// s等于t，它们是同一个对象
// A和B都是常量，值是固定的，因此s的值也是固定的，它在类被编译时就已经确定了。也就是说：String s=A+B; 等同于：String s="ab"+"cd";
```

```java
public static final String A; // 常量A
public static final String B;    // 常量B
static {   
     A = "ab";   
     B = "cd";   
 }   
 public static void main(String[] args) {   
    // 将两个常量用+连接对s进行初始化   
     String s = A + B;   
     String t = "abcd";   
    if (s == t) {   
         System.out.println("s等于t，它们是同一个对象");   
     } else {   
         System.out.println("s不等于t，它们不是同一个对象");   
     }   
 } 
// s不等于t，它们不是同一个对象
// A和B虽然被定义为常量，但是它们都没有马上被赋值。在运算出s的值之前，他们何时被赋值，以及被赋予什么样的值，都是个变数。因此A和B在被赋值之前，性质类似于一个变量。那么s就不能在编译期被确定，而只能在运行时被创建了。
```

### 5. 总结

- 必须要关注编译期的行为，才能更好的理解常量池。

- 运行时常量池中的常量，基本来源于各个class文件中的常量池。

- 程序运行时，除非手动向常量池中添加常量(比如调用intern方法)，否则jvm不会自动添加常量到常量池。

### 6. String、StringBuffer 、StringBuilder

- **可变性**：String 不可变；StringBuffer 和 StringBuilder 可变
- **线程安全**：String 不可变，因此是线程安全的；StringBuilder 不是线程安全的；StringBuffer 是线程安全的，内部使用 synchronized 进行同步。


## 5.static加载顺序、执行时机

### 1. static代码块执行时机

一个类的运行分为以下步骤：

1. **装载**
2. **连接**
3. **初始化**

装载阶段又三个基本动作组成：

1. 通过类型的完全限定名，产生一个代表该类型的二进制数据流
2. 解析这个二进制数据流为方法区内的内部数据结
3. 构创建一个表示该类型的java.lang.Class类的实例

连接阶段又分为三部分：

1. 验证，确认类型符合Java语言的语义，检查各个类之间的二进制兼容性(比如final的类不用拥有子类等)，另外还需要进行符号引用的验证。
2. 准备，Java虚拟机为类变量分配内存，设置默认初始值。
3. 解析(可选的) ，在类型的常量池中寻找类，接口，字段和方法的符号引用，把这些符号引用替换成直接引用的过程。

当一个类被主动使用时，Java虚拟就会对其初始化，如下六种情况为主动使用：

1. 当创建某个类的新实例时（如通过new或者反射，克隆，反序列化等）
2. 当调用某个类的静态方法时
3. 当使用某个类或接口的静态字段时
4. 当调用Java API中的某些反射方法时，比如类Class中的方法，或者java.lang.reflect中的类的方法时
5. 当初始化某个子类时
6. 当虚拟机启动某个被标明为启动类的类（即包含main方法的那个类）

Java编译器会收集所有的类变量初始化语句和类型的静态初始化器，将这些放到一个特殊的方法中：clinit。 

### 2. 带有static的代码执行顺序

#### 1. 如果类还没有被加载

1. 先执行父类的静态代码块和静态变量初始化，并且静态代码块和静态变量的执行顺序只跟代码中出现的顺序有关。 
2. 执行子类的静态代码块和静态变量初始化。 
3. 执行父类的实例变量初始化 
4. 执行父类的构造函数 
5. 执行子类的实例变量初始化 
6. 执行子类的构造函数 

#### 2. 如果类已经被加载

则静态代码块和静态变量就不用重复执行，再创建类对象时，只执行与实例相关的变量初始化和构造方法。

#### 3. 实例

```java
public class Test extends Base{
 
    static{
        System.out.println("test static");
    }
     
    public Test(){
        System.out.println("test constructor");
    }
     
    public static void main(String[] args) {
        new Test();
    }
}
 
class Base{
     
    static{
        System.out.println("base static");
    }
     
    public Base(){
        System.out.println("base constructor");
    }
}
// base static
// test static
// base constructor
// test constructor
```

```java
class Person{
    static{
        System.out.println("person static");
    }
    public Person(String str) {
        System.out.println("person "+str);
    }
}
class MyClass extends main {
    Person person = new Person("MyClass");
    static{
        System.out.println("myclass static");
    }

    public MyClass() {
        System.out.println("myclass constructor");
    }
}
public class main  {
    Person person = new Person("main");
    static{
        System.out.println("main static");
    }
    public main(){
        System.out.println("main constructor");
    }
    public static void main(String[] args) {
        new MyClass();
    }
}
// main static
// myclass static
// person static
// person main
// main constructor
// person MyClass
// myclass constructor
```




## 6. 基本数据类型和引用类型在JVM中存储在哪

### 1. 在方法中声明的变量

即该变量是局部变量，每当程序调用方法时，系统都会为该方法建立一个方法栈，其所在方法中声明的变量就放在方法栈中，当方法结束系统会释放方法栈，其对应在该方法中声明的变量随着栈的销毁而结束，这就局部变量只能在方法中有效的原因。

在方法中声明的变量可以是基本类型的变量，也可以是引用类型的变量。

- 当声明是基本类型的变量的时，其变量名及值（变量名及值是两个概念）是放在JAVA虚拟机栈中；
- 当声明的是引用变量时，所声明的变量（该变量实际上是在方法中存储的是内存地址值）是放在JAVA虚拟机的栈中，该变量所指向的对象是放在堆类存中的。

### 2. 在类中声明的变量

成员变量，也叫全局变量，放在堆中的（因为全局变量不会随着某个方法执行结束而销毁）。

同样在类中声明的变量即可是基本类型的变量，也可是引用类型的变量。

- 当声明的是基本类型的变量其变量名及其值放在堆内存中的；
- 引用类型时，其声明的变量仍然会存储一个内存地址值，该内存地址值指向所引用的对象。引用变量名和对应的对象仍然存储在相应的堆中。

## 7. Java 与 C++ 的区别

- Java 是纯粹的面向对象语言，所有的对象都继承自 java.lang.Object，C++ 为了兼容 C 即支持面向对象也支持面向过程。
- Java 通过虚拟机从而实现跨平台特性，但是 C++ 依赖于特定的平台。
- Java 没有指针，它的引用可以理解为安全指针，而 C++ 具有和 C 一样的指针。
- Java 支持自动垃圾回收，而 C++ 需要手动回收。
- Java 不支持多重继承，只能通过实现多个接口来达到相同目的，而 C++ 支持多重继承。
- Java 不支持操作符重载，虽然可以对两个 String 对象执行加法运算，但是这是语言内置支持的操作，不属于操作符重载，而 C++ 可以。
- Java 的 goto 是保留字，但是不可用，C++ 可以使用 goto。
- Java 不支持条件编译，C++ 通过 #ifdef #ifndef 等预处理命令从而实现条件编译。

## 8. 线程池

https://www.nowcoder.com/discuss/152050?type=0&order=0&pos=6&page=0