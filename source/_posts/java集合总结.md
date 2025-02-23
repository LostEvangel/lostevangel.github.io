---
title: Java集合总结
date: 2019-03-27 22:17:19
tags: [面试,Java]
categories: [面试,Java]
description: Collection、Map到底是什么
---


# Java集合总结

参考：[java三大集合框架](https://blog.csdn.net/weixin_39464761/article/details/75137902)

![image](/image/interview_prep/collection.jpg)
### Collection

#### List
- ArrayList：Object数组
- Vector：Object数组
- LinkedList：双向循环链表

#### Set
- HashSet（无序，唯一）：基于HashMap实现，底层采用HashMap保存数组。
- LinkedHashSet：继承与HashSet，并且其内部是通过LinkedHashMap实现。
- TreeSet（有序，唯一）：红黑树。

### Map 
- HashMap：JDK1.8之前HashMap由数组+链表组成的，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的.JDK1.8以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为8）时，将链表转化为红黑树，以减少搜索时间。
- LinkedHashMap：继承自HashMap，所以底层同HashMap，增加了一条双向链表，实现了顺序相关逻辑。
- HashTable：数组+链表组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的。
- TreeSet：红黑树。


## ArrayList和LinkedList
- **线程安全**：都不是线程安全的。
- **底层数据结构**：ArrayList使用的是Object数据，LinkedList使用双向循环链表。
- **插入删除是否受位置影响**：ArrayList采用数组存储，所以时间复杂度受位置影响；LinkedList是双向链表，所以近似O(1)。
- **是否支持快速随机访问**：ArrayList支持通过元素的序号访问，LinkedList不可以。
- **内存空间占用**：ArrayList的结尾会预留一定空间，而LinkedList则是每个元素需要空间存放前驱和后继。

## ArrayList与Vector
- Vector的所有方法都是同步的，所以线程安全
- ArrayList线程不安全。

## HashMap
- JDK1.8前是数组+链表
- JDK1.8后，当容量大于阈值（默认8）后，链表转化为红黑树。
- TreeMap、TreeSet和JDK1.8后的HashMap底层都是红黑树。


## HashMap和HashTable
- **线程安全**：HashMap不是线程安全的，HashTable内部方法都是synchronized修饰，线程安全。
- **效率**：HashMap更快。
- **null的支持**：HashMap中，只有一个键可以为null，值可以多个为null，HashTable不允许，抛异常。
- **初始容量和扩充容量**：HashMap初始为16，每次扩充原来的2倍，HashTable初始为11，扩充为2n+1
- **底层结构**：JDK1.8后，当容量大于阈值（默认8）后，链表转化为红黑树

[Java集合类面试题](https://blog.csdn.net/hfismyangel/article/details/78156419)

## 为何Collection不从Cloneable和Serializable接口继承
Collection接口指定一组对象，对象就是它的元素。如何维护这些元素由具体实现决定。Collection是一个抽象表现，重要的实现。

## 为何Map接口不继承于Collection接口
尽管Map接口和它的实现也是集合框架的一部分，但Map不是集合，集合也不是Map。因此，Map继承Collection毫无意义，反之亦然。

如果Map继承Collection接口，那么元素去哪儿？Map包含key-value对，它提供抽取key或value列表集合的方法，但是它不适合“一组对象”规范。

## Iterator是什么
Iterator接口提供遍历任何Collection的接口。我们可以从一个Collection中使用迭代器方法来获取迭代器实例。迭代器取代了Java集合框架中的Enumeration。迭代器允许调用者在迭代过程中移除元素。

## Enumeration和Iterator接口的区别
Enumeration的速度是Iterator的两倍，也使用更少的内存。Enumeration是非常基础的，也满足了基础的需要。但是，与Enumeration相比，Iterator更加安全，因为当一个集合正在被遍历的时候，它会阻止其它线程去修改集合。

迭代器取代了Java集合框架中的Enumeration。迭代器允许调用者从集合中移除元素，而Enumeration不能做到。为了使它的功能更加清晰，迭代器方法名已经经过改善。

## 为何迭代器没有一个方法可以直接获取下一个元素，而不需要移动游标
可以在当前Iterator的顶层实现，但是它用得很少，如果将它加到接口中，每个继承都要去实现它，这没有意义。

## Iterator和ListIterator之间有什么区别
- Iterator可以适用于Set、List，ListIterator只能用于List
- ListIterator可以双向遍历，Iterator只能向前遍历
- ListIterator继承于Iterator，添加了额外的功能，比如添加、替换、获取前面或后面的元素。

## 遍历List方式

```java
List<String> strList = new ArrayList<>();
// for-each遍历
for(String obj : strList){
    System.out.println(obj);
}
// Iterator遍历
Iterator<String> it = strList.iterator();
while(it.hasNext()){
    String obj = it.next();
    System.out.println(obj);
}
```
迭代器更加线程安全，因为它可以确保，在当前遍历的集合元素被更改的时候，它会抛出ConcurrentModificationException。

## 如何从给定集合那里创建一个synchronized的集合

使用Collections.synchronizedCollection(Collectionc)根据指定集合来获取一个synchronized（线程安全的）集合

## 哪些集合类是线程安全的
Vector、HashTable、Properties和Stack是同步类，所以它们是线程安全的，可以在多线程环境下使用。Java1.5并发API包括一些集合类，允许迭代时修改，因为它们都工作在集合的克隆上，所以它们在多线程环境中是安全的。






