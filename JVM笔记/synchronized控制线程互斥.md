# synchronized控制线程互斥

## 1、demo实例

```java

// 创建account类作为共享资源，在线程中被初始化为共享对象
class account{
    int balance;
    public account(int money){
        balance = money;
        System.out.println("balance is "+balance);
    }
}

// 继承thread类，并覆写run方法完成线程创建
// thread类是一个具体的类，不是抽象类，该类已经封装了线程的行为
public class MyThread extends Thread{

    account account;
    String name;
    int amount;
    
    // 完善类的构造器
    public MyThread(account balance, String name,int amount){
        this.account = balance;
        this.name = name;
        this.amount = amount;
    }

    public static void main(String[] args){
        account account = new account(100);

        MyThread p = new MyThread(account,"p1",100);
        MyThread p2 = new MyThread(account,"p2",100);
        MyThread p3 = new MyThread(account,"p3",100);

        // 线程允许设置优先级
        p3.setPriority(Thread.MAX_PRIORITY);
        p2.setPriority(Thread.MIN_PRIORITY);
        p.setPriority(2);

        // 启动线程必须调用start方法，而不是run方法
        // start方法会首先完成多线程的初始化
        p.start();
        p2.start();
        p3.start();
    }

    // 使用线程必须重写run方法，即线程体具体实现了什么功能
    public void run(){
        synchronized (this){
            if (account.balance>=amount){
                account.balance -= amount;
                System.out.println(name+"取款100成功");
            } else{
                System.out.println(name+"取款100失败");
            }
        }
    }
}

```



## 2、互斥对象

并发程序设计中，对多线程共享的资源或数据成为临界资源，而把每个线程中访问邻接资源的那一段代码段成为临界代码段。

Java语言设计：

1）为每个对象设置了一个”互斥锁“标记。该标记保证在每一个时刻，只能有一个线程拥有该互斥锁，其他线程如果需要获得该互斥锁，必须等待当前拥有该锁的线程将其释放。该对象成为互斥对象。

2）为了配合使用对象的互斥锁，Java提供了保留字synchronized。当一个线程执行到该代码段，检测并获得了该互斥对象的互斥锁。当互斥锁被持有时，其他线程自动进入等待队列。

```java
synchronized(互斥对象){
    临界代码段
}
```



### 问题

将account类中的balance变量放到MyThread类中，能不能互斥？

答案是不能，对象是类的初始化。balance只是变量，所以必须被类保护起来

```java
// 创建account类作为共享资源，在线程中被初始化为共享对象
class account{
    int balance;
    public account(int money){
        balance = money;
        System.out.println("balance is "+balance);
    }
}
```



## 3、信号灯

只要是对象就有互斥锁，那一个空类可不可以？可以。

```java
class Semaphore{}

public class MyThread extends Thread{
    Semaphore semaphore;
    public void run(){
        synchronized (semaphore){
        }
    }
}

```



类本身作为信号灯，用this关键字指代。

```java
public class MyThread extends Thread{
    public void run(){
        synchronized (this){
        }
    }
}

```



## 4、直接声明方法

将synchronized关键字直接声明到方法前面，表示以方法所在的对象为互斥对象，类似this关键字。

但是这样会将整个方法的所有代码，都作为临界代码。

```java
public class MyThread extends Thread{
    public synchronized void run(){
    }
}
```



## 5、直接声明类

可以将关键字直接声明在类的前面，表示该类的所有方法为临界代码（同步方法），该类的对象为互斥对象。

——没有demo