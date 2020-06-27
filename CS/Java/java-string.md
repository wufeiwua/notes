# Java 源码阅读 - java.lang.String

## 1. 为什么不能被继承和修改
String 类中使用 `final` 关键字修饰字符数组来保存字符串，所以 String 对象是不可变的

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
    // ...
}
```