# Java线程池

## 1、两种线程池

线程池的基本思想是，在系统中开辟一块区域，其中存放一些待命的线程，这个区域称为线程池，如果需要执行任务，则从线程池取出一个待命的线程来执行指定的任务，到任务结束后再将所取线程放回。

这样就避免了大量重复创建线程对象，浪费CPU、内存等资源的问题

## 1.1 固定尺寸线程池

```
/**
 * 固定尺寸线程池demo
 *
 * @author z00465759
 * @since 2020-07-30
 */
public class FixTest implements Runnable{
    private String name;
    public FixTest(String fname) {
        this.name = fname;
    }

    @Override
    public void run() {
        System.out.println("\n----"+name+"开始执行");
        for(int i=0; i<50; i++) {
            System.out.print("("+i+")");
        }
        System.out.println("\n----"+name+"执行结束");
    }

    public static void main(String[] args){
        // 创建尺寸为2的固定线程池，只允许两个线程执行
        ExecutorService threadPool = Executors.newFixedThreadPool(2);
        // 创建3个任务对象
        FixTest ft1 = new FixTest("ft1");
        FixTest ft2 = new FixTest("ft2");
        FixTest ft3 = new FixTest("ft3");
        // 启动3个任务执行，但是线程只有2个，所以有一个任务要等待
        threadPool.execute(ft1);
        threadPool.execute(ft2);
        threadPool.execute(ft3);
        // 任务结束后，线程池中的线程依然存活等待新的任务，除非shutdown手动关闭
        threadPool.shutdown();
    }
}

```

### 1.2 可变尺寸线程池

```
/**
 * 可变尺寸线程池demo
 *
 * @author z00465759
 * @since 2020-07-30
 */
class ShrinkTest implements Runnable {
    private String name;
    public ShrinkTest(String fname) {
        this.name = fname;
    }

    @Override
    public void run() {
        System.out.println("\n----"+name+"开始执行");
        for(int i=0; i<50; i++) {
            System.out.print("("+i+")");
        }
        System.out.println("\n----"+name+"执行结束");
    }

    public static void main(String[] args){
        // 创建可变尺寸的线程池
        ExecutorService threadPool = Executors.newCachedThreadPool();
        // 创建3个任务对象
        ShrinkTest ft1 = new ShrinkTest("ft1");
        ShrinkTest ft2 = new ShrinkTest("ft2");
        ShrinkTest ft3 = new ShrinkTest("ft3");
        // 启动3个任务执行
        // 三个任务会交替并发执行，可变尺寸线程池可以根据任务的多少来自动调整待命线程的数量、优化执行性能
        threadPool.execute(ft1);
        threadPool.execute(ft2);
        threadPool.execute(ft3);
        // 可变线程池的大小不固定，当执行任务时，先选取重用缓存中已有空闲线程来完成任务
        // 如果没有空闲线程，则创建新线程，空闲超过60秒时，线程将从线程池中删除
        // threadPool.shutdown(); 所以shutdown是非必须的
    }
}

```

