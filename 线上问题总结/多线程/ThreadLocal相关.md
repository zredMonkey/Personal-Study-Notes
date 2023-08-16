# 应用场景

```java

private SimpleDateFormat f = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

public void seckillSku(){
String dateStr = f.format(new Date());
// 业务流程
}
```

如果还在这么写，那就已经犯了一个线程安全的错误。++SimpleDateFormat，并不是一个线程安全的类++。



## 线程不安全验证

```java
private static SimpleDateFormat f = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

public static void main(String[] args) {
  while (true) {
     new Thread(() -> {
        String dateStr = f.format(new Date());
            try {
                 Date parseDate = f.parse(dateStr);
                 String dateStrCheck = f.format(parseDate);
                 boolean equals = dateStr.equals(dateStrCheck);
                 if (!equals) {
                    System.out.println(equals + " " + dateStr + " " + dateStrCheck);
                 } else {
                    System.out.println(equals);
                 }
             } catch (ParseException e) {
                 System.out.println(e.getMessage());
             }
       }).start();
   }
}
```

这是一个多线程下 SimpleDateFormat 的验证代码。当 equals 为 false 时，证明线程不安全。运行结果如下；

```java
true
true
false 2020-09-23 11:40:42 2230-09-23 11:40:42
true
true
false 2020-09-23 11:40:42 2020-09-23 11:40:00
false 2020-09-23 11:40:42 2020-09-23 11:40:00
false 2020-09-23 11:40:00 2020-09-23 11:40:42
true
false 2020-09-23 11:40:42 2020-08-31 11:40:42
true
```

为了线程安全最直接的方式，就是每次调用都直接 new SimpleDateFormat。但这样的方式终究不是最好的，所以我们使用 ThreadLocal ，来优化这段代码。

```java
private static ThreadLocal<SimpleDateFormat> threadLocal = ThreadLocal.withInitial(() -> new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));

   public static void main(String[] args) {
      while (true) {
        new Thread(() -> {
            String dateStr = threadLocal.get().format(new Date());
            try {
               Date parseDate = threadLocal.get().parse(dateStr);
               String dateStrCheck = threadLocal.get().format(parseDate);
               boolean equals = dateStr.equals(dateStrCheck);
               if (!equals) {
                  System.out.println(equals + " " + dateStr + " " + dateStrCheck);
               } else {
                  System.out.println(equals);
               }
             } catch (ParseException e) {
                System.out.println(e.getMessage());
             }
        }).start();
   }
}
```

如上我们把 SimpleDateFormat ，放到 ThreadLocal 中进行使用，即不需要重复new 对象，也避免了线程不安全问题。测试结果如下；

```java
true
true
true
true
true
true
```



# 数据结构

以下是 ThreadLocal 的简单使用以及部分源码以下是 ThreadLocal 的简单使用以及部分源码。

new ThreadLocal&lt;String&gt;().set("小傅哥");

```java

private void set(ThreadLocal<?> key, Object value) {
   Entry[] tab = table;
   int len = tab.length;
   int i = key.threadLocalHashCode & (len-1);
   for (Entry e = tab[i];
      e != null;
      e = tab[i = nextIndex(i, len)]) {
      ...
}
```

从这部分源码中可以看到，ThreadLocal 底层采用的是数组结构存储数据，同时还有哈希值计算下标，这说明它是一个散列表的数组结构，

