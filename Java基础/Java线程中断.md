# Java线程中断

一个线程执行完毕之后会自动结束，如果在运行过程中发生异常也会提前结束。

## 1、InterruptredException

通过调用一个线程的interrupt来中断该线程，如果该线程处于阻塞、限期等待或无限期等待状态，那就会抛出InterruptredException，从而提前结束该线程。

但是不能中断I/O阻塞和synchronized锁阻塞。

```
/**
 * InterruptredException测试
 *
 * @author z00465759
 * @since 2020-07-30
 */
class InterruptExample {
    private static class MyThread1 extends Thread {
        @Override
        public void run() {
            try {
                Thread.sleep(2000);
                System.out.println("Thread run");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        Thread thread1 = new MyThread1();
        thread1.start();
        thread1.interrupt();
        System.out.println("Main run");
    }
}


Main run
java.lang.InterruptedException: sleep interrupted
    at java.lang.Thread.sleep(Native Method)
    at InterruptExample.lambda$main$0(InterruptExample.java:5)
    at InterruptExample$$Lambda$1/713338599.run(Unknown Source)
    at java.lang.Thread.run(Thread.java:745)


```



## 2、Interrupted

如果一个线程的 run() 方法执行一个无限循环，并且没有执行 sleep() 等会抛出 InterruptedException 的操作，那么调用线程的 interrupt() 方法就无法使线程提前结束。

但是调用 interrupt() 方法会设置线程的中断标记，此时调用 interrupted() 方法会返回 true。因此可以在循环体中使用 interrupted() 方法来判断线程是否处于中断状态，从而提前结束线程。

```
/**
 * 功能描述
 *
 * @author z00465759
 * @since 2020-07-30
 */
class InterruptExample {
    private static class MyThread2 extends Thread {
        @Override
        public void run() {
            int count=0;
            while (!interrupted()) {
                count++;
            }
            System.out.println("Thread end");
        }
    }

    public static void main(String[] args) {
        Thread thread2 = new MyThread2();
        thread2.start();
        thread2.interrupt();
    }
}

```

## 3、Executor的中断操作

调用 Executor 的 shutdown() 方法会等待线程都执行完毕之后再关闭，但是如果调用的是 shutdownNow() 方法，则相当于调用每个线程的 interrupt() 方法。

以下使用 Lambda 创建线程，相当于创建了一个匿名内部线程。

```
public static void main(String[] args) {
    ExecutorService executorService = Executors.newCachedThreadPool();
    executorService.execute(() -> {
        try {
            Thread.sleep(2000);
            System.out.println("Thread run");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    });
    executorService.shutdownNow();
    System.out.println("Main run");
}


Main run
java.lang.InterruptedException: sleep interrupted
    at java.lang.Thread.sleep(Native Method)
    at ExecutorInterruptExample.lambda$main$0(ExecutorInterruptExample.java:9)
    at ExecutorInterruptExample$$Lambda$1/1160460865.run(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
    at java.lang.Thread.run(Thread.java:745)


```

