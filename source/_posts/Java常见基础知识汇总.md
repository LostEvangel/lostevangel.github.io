---
title: Java常见基础知识汇总
date: 2019-06-25 20:07:29
tags: [面试,Java]
categories: [面试,Java]
photos:
  - "https://github.com/CherryKeinz/cherrykeinz.github.io/blob/master/images/cover/1.jpg?raw=true"
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