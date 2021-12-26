# Java线程同步

## 1、生产-消费者模型

典型的生产消费者问题，要求Java提供线程同步控制机制。

1）生产者要保存产品到仓库

2）仓库容量有限，只有当库房有空间，才能加入产品，否则等待

3）消费者要从仓库取出产品

4）只有仓库中有商品，才能取出产品，否则等待

## 2、wait和notify

Java语言为互斥对象提供了两个方法，一个是wait()，一个是notify()，用于对如上的问题提供两个线程的同步。但是注意，这两个方法是Object类的，不作为Thread类的方法提供。

wait: 当一个线程执行了该方法，则该线程进入阻塞状态，同时让出同步对象的互斥锁，并自动进入互斥对象的等待队列。

notify: 当一个线程执行了该方法，则拥有该线程的互斥对象的等待队列中的第一个线程被唤醒，同时自动获得该对象的互斥锁，并进入就绪状态等待调度。

对wait和notify的使用要注意：

1）两个方法都必须位于临界代码段中。也就是说，执行该方法的线程必须已经获得了互斥对象的互斥锁。

2）wait和notify必须配对使用。当某个线程由于调用某个互斥对象的wait进入阻塞状态，只有另一个线程调用该互斥对象的notify才能唤醒该线程进入就绪状态。否则，该线程会一直阻塞

3）在某些情况下，可以根据需要使用notifyall。

## 3、demo

```
/**
 * 线程同步demo
 *
 * @author z00465759
 * @since 2020-07-29
 */
class Account4 {
    double balance;

    public Account4(){
        balance=0;
        System.out.println("total money: "+balance);
    }

    public synchronized  void withdraw(double money) {
        if (balance == 0){
            try{
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        balance = balance -money;
        System.out.println("withdraw 100 success");
        notify();
    }

    public synchronized void deposit(double money) {
        if (balance != 0){
            try{
                wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        balance = balance +money;
        System.out.println("deposit 100 success");
        notify();
    }

}

class WithdrawThread extends Thread{
    Account4 account;
    public WithdrawThread(Account4 account){
        this.account = account;
    }
    public void run() {
        for(int i = 0; i<5; i++) {
            account.withdraw(100);
        }
    }
}

class DepositThread extends Thread{
    Account4 account;
    public DepositThread(Account4 account) {
        this.account = account;
    }
    public void run() {
        for(int i = 0; i<5; i++) {
            account.deposit(100);
        }
    }
}

class TestProCon{
    public static void main(String[] args) {
        Account4 account = new Account4();
        WithdrawThread withdraw = new WithdrawThread(account);
        DepositThread deposit = new DepositThread(account);
        withdraw.start();
        deposit.start();
    }
}

```

