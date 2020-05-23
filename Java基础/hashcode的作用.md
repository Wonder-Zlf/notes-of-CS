# hashcode的作用

## 1、hashcode的作用

Java的集合中有两类，一个是list, 一个是set。前者集合内元素是有序的，元素可以重复。后者是无序的，而且元素不可重复。equals方法用于比较元素，保证不重复。

但是你想象一个1000个元素的set，加入1001个元素岂不是比较1000次？为了提高这里的效率，Java使用了哈希表的原理。

哈希算法也称散列算法，是将数据依定特定算法指定到一个地址上。当集合要添加新元素时，先调用这个hashcode，就能定位到它应该存放的物理位置。

1）如果这个位置上没有元素，就直接新增存储元素

2）如果这个位置已经有元素，使用equals方法比较是否相同，相同就不存了

3）如果不相同，也就是重复的hashcode, 那就新建一个类似链表（其实是散列存储结构），存放所有的同hashcode元素。



## 2、理解hashcode

JVM的对象管理，对每个new的object都丢到hash表中，从而每次查找对象先找hash表，找到对象再判断是否重复。也就是hashcode做到了一个集合指针的效果。



## 3、总结hashcode的特性

1）hashcode用于简化查找复杂性，指向了一个存储地址；

2）如果两个对象的equals相等，那么hashcode一定相同，因为存储在同一散列结构；

3）如果两个对象hashcode相同，只能说明是同散列结构中(算法指向相同地址)，但是实际上可能是A和B

4）如果对象的equals重写，那么对象的hashcode也尽量重写



## 4、hashcode的java api解释

1）在java应用程序执行期间，如果在equals方法比较中所用的信息没有被修改，那么在同一个对象上多次调用hashCode方法时必须一致地返回相同的整数。如果多次执行同一个应用时，不要求该整数必须相同。

2）如果两个对象通过调用equals方法是相等的，那么这两个对象调用hashCode方法必须返回相同的整数。

3）如果两个对象通过调用equals方法是不相等的，不要求这两个对象调用hashCode方法必须返回不同的整数。但是程序员应该意识到对不同的对象产生不同的hash值可以提供哈希表的性能。

Java api原文：

```java
public int hashCode​()

Returns a hash code value for the object. This method is supported for the benefit of hash tables such as those provided by HashMap.
The general contract of hashCode is:

--Whenever it is invoked on the same object more than once during an execution of a Java application, the hashCode method must consistently return the same integer, provided no information used in equals comparisons on the object is modified. This integer need not remain consistent from one execution of an application to another execution of the same application.
--If two objects are equal according to the equals(Object) method, then calling the hashCode method on each of the two objects must produce the same integer result.
--It is not required that if two objects are unequal according to the equals(java.lang.Object) method, then calling the hashCode method on each of the two objects must produce distinct integer results. However, the programmer should be aware that producing distinct integer results for unequal objects may improve the performance of hash tables.

As much as is reasonably practical, the hashCode method defined by class Object does return distinct integers for distinct objects. (The hashCode may or may not be implemented as some function of an object's memory address at some point in time.)

Returns:
a hash code value for this object.
See Also:
equals(java.lang.Object), System.identityHashCode(java.lang.Object)
```



## 5、为什么hashcode要跟着equals重写

比较复杂，先从equals说起。

1）equals是什么

equals首先是来自超类object的一个基本方法，用于比较对象之间是否相等。在object中，equals的定义是这样的：

```java
public boolean equals(Object obj) {   return (this == obj);     }
```

实际上我们知道所有的对象都拥有标识(内存地址)和状态(数据)，同时“==”比较两个对象的的内存地址，所以说 Object 的 equals() 方法是比较两个对象的内存地址是否相等，即如果object1.equals(object2) 为 true，则表示 equals1 和 equals2 实际上是引用同一个对象。



2）equals经常被重写

但是日常使用的例如string的equals是比较了字符的序列，两个不同对象的相同内容可以正确比较。说明什么？string类就重写了equals方法。可以想象，object的各个子类，满足各自特性都会一定程度重写equals方法。



3）同步重写hashcode保证高效

回到最初，hashcode的设计逻辑是算法将对象指向物理地址，同一物理地址存储相同元素来提高查找效率。如果子类重写了equals，但是不重写hashcode，string中两个“abc”会有不同的散列码。说回来，你这不就是浪费内存吗？

当然，两个“abc”有不同的hashcode，但是equals可以正确比较是不影响功能的。可是，没有重写优化的hashcode意味着维护越来越长的哈希表，与设计初衷背离了。

记得api的话：

```java
2）如果两个对象通过调用equals方法是相等的，那么这两个对象调用hashCode方法必须返回相同的整数。
```



## 6、哪些用到了hashcode

java.lang.object的子类，例如hashmap、string

```java
Class HashMap<K,V>
java.lang.Object
java.util.AbstractMap<K,V>
java.util.HashMap<K,V>
```

