# Java 源码阅读 - java.lang.String

## 1. 为什么不能被继承和修改
String 类中使用 `final` 关键字修饰字符数组来保存字符串，所以 String 对象是不可变的。同时使用 `final` 修饰成员变量 `value`，也表明不可改变

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];

    /** Cache the hash code for the string */
    private int hash; // Default to 0
    // ...
}
```

Java 9 之后，String 类改用 `byte` 数组存储字符串，同时使用 `coder` 来标识使用了哪种编码。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final byte[] value;

    /** The identifier of the encoding used to encode the bytes in {@code value}. */
    private final byte coder;
    // ...
}
```

## 2. 字符串比较 

- `==` 。对于基本类型来说比较的是值的大小，对于对象来说比较的是引用地址是否相同。

```java
String s1 = "java";
String s2 = new String("java");
String s3 = new String("java");
String s4 = "hello,java";
String s5 = "hello," + "java";
String s6 = "hello," + s1;
System.out.println(s1 == s2); // false
System.out.println(s2 == s3); // false
System.out.println(s4 == s5); // true
System.out.println(s4 == s6); // false
```
之所以，`s4 == s5` 是因为java 在编译期所做的优化。

```sh
$ javap -c xxx.class
```

```java
// ...
13: new           #3                  // class java/lang/String
16: dup
17: ldc           #2                  // String java
19: invokespecial #4                  // Method java/lang/String."<init>":(Ljava/lang/String;)V
22: astore_3
23: ldc           #5                  // String hello,java
25: astore        4
27: ldc           #5                  // String hello,java
29: astore        5
31: new           #6                  // class java/lang/StringBuilder
34: dup
35: invokespecial #7                  // Method java/lang/StringBuilder."<init>":()V
38: ldc           #8                  // String hello,
40: invokevirtual #9                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
43: aload_1
// ...
```

可见，第 23 和 第 25 行，是相同的引用；第 40 行 可见 其实调用了  `StringBuilder` 类的 `append` 方法，所以 `s4 != s6`

- `equals`。String 类重写 Object 类 `equals` 方法, 首先比较对象引用地址是否相同，如果不相等，则判断字符串长度是否相同，再依次比较每一个字符是否相等

```java
public boolean equals(Object anObject) {
    // 比较引用地址
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        // 长度是否相等
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            // 依次判断每一个字符
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```