# Java接口

## 1、Java接口

接口在Java语言中是一个抽象类型，是抽象方法的集合，接口通常以interface来声明。一个类通过继承接口的方式，从而来继承接口的抽象方法。

接口并不是类，它们很相似但属于不同概念。类描述对象的属性和方法，接口则包含类要实现的方法。

除非实现接口的类是抽象类，否则该类要定义接口中的所有方法。

接口无法被实例化，但是可以被实现。一个实现接口的类，必须实现接口内所描述的所有方法，否则就必须声明为抽象类。

另外，在Java中，接口类型可用来声明一个变量，他们可以成为一个空指针，或是被绑定在一个以此接口实现的对象。

### 1.1 demo

```
interface printABC{
    final String abc="abc";
    public void print();
};

/**
 * 功能描述
 *
 * @author z00465759
 * @since 2020-07-28
 */
class testInterface implements printABC {
    public void print() {
        System.out.println(abc);
    }

    public static void main(String[] args){
        testInterface tt = new testInterface();
        tt.print();
    }
}

```

### 1.2 接口和类相似点

1）一个接口可以有多个方法

2）接口文件保存在Java结尾的文件中，文件名使用接口名

3）接口的字节码文件保存在 .class结尾的文件中

4）接口相应的字节码文件必须与包名称相匹配的目录结构中

### 1.3 接口与类的区别

1）接口不能用于实例化对象

2）接口没有构造方法

3）接口所有的方法必须是抽象方法，除静态方法体

4）接口不能包含成员变量，除了static和final变量

5）接口不是被类继承了，而是要被类实现

6）接口支持多继承

### 1.4 接口特性

1）接口中每一个方法也是隐式抽象的，接口中的方法会被隐式的指定为public abstract（其他修饰符都会报错）

2）接口中可以含有变量，但是接口中的变量会被隐式的指定为public static final变量（其他修饰符会报错）

3）接口中的非静态方法是不能在接口中实现的，只能由实现接口的类来实现接口中的方法

4）Java JDK1.8后，接口中可以有静态方法和方法体，与抽象类相同

#### 1.4.1 接口中的静态方法体

```
interface printDEF{
    static String def="def";
    static void say(){
        System.out.println(def);
    };
};

/**
 * 功能描述
 *
 * @author z00465759
 * @since 2020-07-28
 */
class testInterface implements printDEF {

    public static void main(String[] args){
        printDEF.say();
    }

}
```

### 1.5接口和抽象类的区别

1）抽象类中的方法可以有方法体、就是能实现方法的具体功能，但是接口中的方法不行

2）抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是public static final类型的

3）一个类只能继承一个抽象类，而一个类却可以实现多个接口

## 2、接口的声明

```
[可见度] interface 接口名称 [extends 其他的接口名] {
        // 声明变量
        // 抽象方法
}
```


注意以下特性：

1）接口是隐式抽象的，当声明一个接口的时候，不必使用abstract关键字

2）接口中每个方法也是隐式抽象的，声明时同样不需要abstract关键字

3）接口中的方法都是公有的

## 3、接口的实现

重写接口中的方法时：

1）类实现接口不能抛出强制性异常，只能在接口中、或者继承接口的抽象类中抛出该强制性异常

2）类在重写方法时要保持一致的方法名，并且应该保持相同或者相兼容的返回值类型

3）当类实现接口时，必须实现接口中的所有方法，否则类必须声明为抽象的类

4）一个类可以实现多个接口（implements），而且一个接口能够继承其他多个接口（extends）

### 3.1 多继承冲突

```
interface printABC{
    final String abc="abc";
    public void print();
};

interface printDEF{
    final String abc="a";
    public void print();
};

/**
 * 功能描述
 *
 * @author z00465759
 * @since 2020-07-28
 */
class testInterface implements printABC, printDEF {
    public void print() {
        System.out.println(abc);
    }

    public static void main(String[] args){
        testInterface tt = new testInterface();
        tt.print();
    }
}

```

```
Error:(23, 28) java: reference to abc is ambiguous
  both variable abc in huawei.printABC and variable abc in huawei.printDEF match
```

## 4、标记接口

最常用的继承接口是没有包含任何方法的接口。

标记接口是没有任何方法和属性的接口。它仅仅表明它的类属于一个特定的类型，供其他代码来测试允许做一些事情。

标记接口作用：简单形象的说就是给某个对象打个标（盖个戳），使对象拥有某个或某些特权。

