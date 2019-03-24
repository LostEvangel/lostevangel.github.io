---
title: Lambda序列化问题
date: 2017-12-12 16:05:58
tags: Java
categories: Lambda表达式

---

# Lambda序列化问题


这几天用spark遇到一个问题，到现在仍没有解决，不过查到了一些思路，可能与序列化有关。

### 先上代码


```
//不用Lambda，没有问题
lines.mapToPair(new PairFunction<String, Integer, Integer>() {
			private static final long serialVersionUID = 1323L;

			@Override
			public Tuple2<Integer, Integer> call(String word) throws Exception {
				return new Tuple2<Integer, Integer>(1, 1);
			}
		}).take(200).forEach(v -> System.out.println(v));
//用lambda，报错
lines.mapToPair(v -> new Tuple2<Integer, Integer>(1, 1)).take(200).forEach(v -> System.out.println(v));
```

### 错误信息：

> 
```
17/12/12 16:00:53 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, 10.103.244.216): java.io.IOException: unexpected exception type
	at java.io.ObjectStreamClass.throwMiscException(ObjectStreamClass.java:1582)
	at java.io.ObjectStreamClass.invokeReadResolve(ObjectStreamClass.java:1154)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2022)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1535)
	at java.io.ObjectInputStream.defaultReadFields(ObjectInputStream.java:2231)
	at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:2155)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2013)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1535)
	at java.io.ObjectInputStream.defaultReadFields(ObjectInputStream.java:2231)
	at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:2155)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2013)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1535)
	at java.io.ObjectInputStream.defaultReadFields(ObjectInputStream.java:2231)
	at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:2155)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2013)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1535)
	at java.io.ObjectInputStream.defaultReadFields(ObjectInputStream.java:2231)
	at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:2155)
	at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2013)
	at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1535)
	at java.io.ObjectInputStream.readObject(ObjectInputStream.java:422)
	at org.apache.spark.serializer.JavaDeserializationStream.readObject(JavaSerializer.scala:76)
	at org.apache.spark.serializer.JavaSerializerInstance.deserialize(JavaSerializer.scala:115)
	at org.apache.spark.scheduler.ResultTask.runTask(ResultTask.scala:61)
	at org.apache.spark.scheduler.Task.run(Task.scala:89)
	at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:213)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at java.lang.Thread.run(Thread.java:745)
Caused by: java.lang.NoSuchMethodException: TestSUANZI.test.$deserializeLambda$(java.lang.invoke.SerializedLambda)
	at java.lang.Class.getDeclaredMethod(Class.java:2130)
	at java.lang.invoke.SerializedLambda$1.run(SerializedLambda.java:224)
	at java.lang.invoke.SerializedLambda$1.run(SerializedLambda.java:221)
	at java.security.AccessController.doPrivileged(Native Method)
	at java.lang.invoke.SerializedLambda.readResolve(SerializedLambda.java:221)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at java.io.ObjectStreamClass.invokeReadResolve(ObjectStreamClass.java:1148)
	... 27 more

17/12/12 16:00:53 INFO TaskSetManager: Starting task 0.1 in stage 1.0 (TID 2, 10.103.244.216, partition 0,ANY, 2193 bytes)
17/12/12 16:00:53 INFO TaskSetManager: Lost task 0.1 in stage 1.0 (TID 2) on executor 10.103.244.216: java.io.IOException (unexpected exception type) [duplicate 1]
17/12/12 16:00:53 INFO TaskSetManager: Starting task 0.2 in stage 1.0 (TID 3, 10.103.244.216, partition 0,ANY, 2193 bytes)
17/12/12 16:00:53 INFO TaskSetManager: Lost task 0.2 in stage 1.0 (TID 3) on executor 10.103.244.216: java.io.IOException (unexpected exception type) [duplicate 2]
17/12/12 16:00:53 INFO TaskSetManager: Starting task 0.3 in stage 1.0 (TID 4, 10.103.244.216, partition 0,ANY, 2193 bytes)
17/12/12 16:00:53 INFO TaskSetManager: Lost task 0.3 in stage 1.0 (TID 4) on executor 10.103.244.216: java.io.IOException (unexpected exception type) [duplicate 3]
17/12/12 16:00:53 ERROR TaskSetManager: Task 0 in stage 1.0 failed 4 times; aborting job
```

目前的解决方法是实现Serializable这个接口。


有时间读一下 
https://www.zhihu.com/question/51491241/answer/126232275


