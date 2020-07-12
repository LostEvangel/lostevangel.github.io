---
title: CopyOnWriteArrayList和CopyOnWriteArraySet的源码解析
date: 2019-06-27 22:49:24
tags: [Java,并发编程]
categories: Java
photos:
  - "https://github.com/LostEvangel/LostEvangel.github.io/blob/master/image/cover/1.jpg?raw=true"
description: 从源码角度彻底理解CopyOnWriteArrayList和CopyOnWriteArraySet
---

CopyOnWriteArrayList 是一个线程安全的 ArrayList，通过内部的 volatile 数组和显式锁 ReentrantLock 来实现线程安全。而 CopyOnWriteArraySet 是线程安全的 Set，它是由 CopyOnWriteArrayList 实现，内部持有一个 CopyOnWriteArrayList 引用，所有的操作都是由 CopyOnWriteArrayList 来实现的。

和 ArrayList 或 Set 相比，CopyOnWriteArrayList / CopyOnWriteArraySet 拥有以下特性：

- 适合元素比较少，并且**读取操作高于更新**(add/set/remove)操作的场景
- 由于每次更新需要复制内部数组，所以更新操作开销比较大
- 内部的迭代器 iterator 使用了“快照”技术，存储了内部数组快照， 所以它的 iterator 不支持remove、set、add操作，但是通过迭代器进行并发读取时效率很高。

## 源码解析

```java
//锁
final transient ReentrantLock lock = new ReentrantLock();

//用于存储元素的内部数组
private transient volatile Object[] array;
```

CopyOnWriteArrayList 实现非常简单。内部使用了一个 volatile 数组(array)来存储数据，保证了多线程环境下的可见性。在更新数据时，都会新建一个数组，并将更新后的数据拷贝到新建的数组中，最后再将该数组赋值给 array。正由于这个原因，涉及到数据更新的操作效率很低。

### add操作

```java
public void add(int index, E element) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        if (index > len || index < 0)
            throw new IndexOutOfBoundsException("Index: "+index+
                    ", Size: "+len);
        Object[] newElements;
        //计算偏移量
        int numMoved = len - index;
        if (numMoved == 0)
            //作为add(E)处理
            newElements = Arrays.copyOf(elements, len + 1);
        else {
            newElements = new Object[len + 1];
            //调用native方法根据index拷贝原数组的前半段
            System.arraycopy(elements, 0, newElements, 0, index);
            //拷贝后半段
            System.arraycopy(elements, index, newElements, index + 1,
                    numMoved);
        }
        newElements[index] = element;
        setArray(newElements);
    } finally {
        lock.unlock();
    }
}

//System中arrayCopy的实现
public static native void arraycopy(Object src,  int  srcPos, Object dest, int destPos, int length);
```