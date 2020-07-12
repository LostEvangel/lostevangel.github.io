---
title: JDK动态代理
date: 2019-10-12 20:45:04
tags: [面试,Java]
categories: [面试,Java]
photos:
  - "https://github.com/LostEvangel/LostEvangel.github.io/blob/master/image/cover/1.jpg?raw=true"
description: JDK动态代理及实现
---

# JDK动态代理

> 整个JDK动态代理的秘密也就这些，简单一句话，动态代理就是要生成一个包装类对象，由于代理的对象是动态的，所以叫动态代理。由于我们需要增强，这个增强是需要留给开发人员开发代码的，因此代理类不能直接包含被代理对象，而是一个InvocationHandler，该InvocationHandler包含被代理对象，并负责分发请求给被代理对象，分发前后均可以做增强。从原理可以看出，JDK动态代理是“对象”的代理。
>
> 版权声明：本文为CSDN博主「探索者一号」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/flyfeifei66/article/details/81481222



```java
interface TargetInterface {
    String method1();
    void method2();
    int method3(int x);
}
class Target implements TargetInterface{

    @Override
    public String method1() {
        System.out.println("method1 running...");
        return "aaa";
    }

    @Override
    public void method2() {
        System.out.println("method2 running...");
    }

    @Override
    public int method3(int x) {
        return x;
    }

}
```

```java
public class Main {
    public static void main(String[] args) throws Exception {
        Target target = new Target();
        TargetInterface newProxyInstance = (TargetInterface) Proxy.newProxyInstance(
                target.getClass().getClassLoader(),
                target.getClass().getInterfaces(),
                // new InvocationHandler 重新invoke方法
                (proxy, method, argss)->{
                    System.out.println("before");Object invoke = method.invoke(target, argss);System.out.println("after");
                    return invoke;});
        String method1 = newProxyInstance.method1();
        newProxyInstance.method2();
        int method3 = newProxyInstance.method3(100);
        System.out.println(method1);
        System.out.println(method3);
    }
}
```

