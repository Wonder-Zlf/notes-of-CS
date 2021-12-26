# String s="abc" 创建了几个对象?

## 1、几个

1）String s 定义了一个变量，没有创建对象；

2）= 赋值，将某个对象的引用赋给s, 没有创建对象；

3）“ abc" 创建了一个对象

4）但是如果内存中已经存在“ abc" ，那这里不会创建新的对象，而是共享已有的对象

## 2、String s=new String("abc") 创建了几个对象?

同1中，另外

1）new String（） 创建一个对象，这个对象copy了“abc"

总结一下java创建对象的方式：new、clone、反射、反序列化

## 3、那么 String s = "ab"+"cd"？

“ab"一个，”cd“一个，”abcd"一个，共三个。