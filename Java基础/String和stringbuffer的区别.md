# String和stringbuffer的区别

## 1、string

被final修饰，基础数据类型之一，immutable，字符串长度不可变。

```java
/** The value is used for character storage. */
private final char value[];

/** Cache the hash code for the string */
private int hash; // Default to 0

/** use serialVersionUID from JDK 1.0.2 for interoperability */
private static final long serialVersionUID = -6849794470754667710L;
```



## 2、stringbuffer

jdk1.0以后增加，字符串变量，但是synchronized的，线程安全。相比string，支持对字符串内容频繁修改。常用方法append，insert。

线程安全的可变字符序列。在任意时间点上它都包含某种特定的字符序列，但通过某些方法调用可以改变该序列的长度和内容。可将字符串缓冲区安全地用于多个线程。

```java
/**
* A cache of the last value returned by toString. Cleared
* whenever the StringBuffer is modified.
*/
private transient char[] toStringCache;

/** use serialVersionUID from JDK 1.0.2 for interoperability */
static final long serialVersionUID = 3388685877147921107L;

/**
* Constructs a string buffer with no characters in it and an
* initial capacity of 16 characters.
*/
public StringBuffer() {
    super(16);
}
```



## 3、stringBuider

jdk5.0以后增加，字符串变量，非线程安全。在内部，stringBuilder对象是一个包含字符序列的变长数组。

默认提供一个和stringbuffer兼容的api, 但不保证同步。该类被设计用作stringbuffer的一个简易替换，用在字符串缓冲区被单个线程使用的时候。

| 构造方法                        | 描述                                                  |
| ------------------------------- | ----------------------------------------------------- |
| StringBuilder()                 | 创建一个容量为16的StringBuilder对象（16个空元素）     |
| StringBuilder(CharSequence cs)  | 创建一个包含cs的StringBuilder对象，末尾附加16个空元素 |
| StringBuilder(int initCapacity) | 创建一个容量为initCapacity的StringBuilder对象         |
| StringBuilder(String s)         | 创建一个包含s的StringBuilder对象，末尾附加16个空元素  |

```java
/** use serialVersionUID for interoperability */
static final long serialVersionUID = 4383685877147921099L;

/**
* Constructs a string builder with no characters in it and an
* initial capacity of 16 characters.
*/
public StringBuilder() {
    super(16);
}
```





## 4、三者区别

1）string作为不可变对象，每次改变实际上就是创建一个新的string对象，并通过指针指向新的string对象。

所以频繁更改string，就会让内存中有很多string但是无引用。Java的GC会因此开始工作，性能就会降低。

2）使用 StringBuffer 类时，每次都会对 StringBuffer 对象本身进行操作，而不是生成新的对象并改变对象引用。所以多数情况下推荐使用 StringBuffer ，特别是字符串对象经常改变的情况下。



## 5、使用策略

1）如果要操作少量的数据，用String ；单线程操作大量数据，用StringBuilder ；多线程操作大量数据，用StringBuffer。



2）不要使用String类的"+"来进行频繁的拼接，因为那样的性能极差的，应该使用StringBuffer或StringBuilder类，这在Java的优化上是一条比较重要的原则。

因为“+”实际上编译时是创建一个stringbuffer来拼接，然后还要转化为string。不如直接在stringbuffer上追加。



3）为了获得更好的性能，在构造 StringBuffer 或 StringBuilder 时应尽可能指定它们的容量。当然，如果你操作的字符串长度（length）不超过 16 个字符就不用了，当不指定容量（capacity）时默认构造一个容量为16的对象。不指定容量会显著降低性能。



4）StringBuilder 一般使用在方法内部来完成类似“+”功能，因为是线程不安全的，所以用完以后可以丢弃。StringBuffer 主要用在全局变量中。



5）相同情况下使用 StringBuilder 相比使用 StringBuffer 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

而在现实的模块化编程中，负责某一模块的程序员不一定能清晰地判断该模块是否会放入多线程的环境中运行，因此：除非确定系统的瓶颈是在 StringBuffer 上，并且确定你的模块不会运行在多线程模式下，才可以采用 StringBuilder；否则还是用 StringBuffer。



参考：
https://blog.csdn.net/kingzone_2008/article/details/9220691