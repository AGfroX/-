**一. 线程;进程;多线程**

**1、普通方法调用和多线程**

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTQLsLGCicy6TkuMyc1oXe3iaoQGVldPHh9PXzPuicaMwkOJuMevXFHvEtQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**2、程序->进程->线程**

在操作系统中运行的程序就是进程,比如你的电脑可以同时打开QQ,播放器,游戏,IDEA等等.



一个进程可以有多个线程,比如视频可以同时听声音,看图像,看弹幕,看字母,等等.



说起进程就不得不说下程序,程序时指令和数据的有序集合,本身没有任何运行的涵义,是一个静态的概念,而进程是执行程序的一次执行过程,它是一个动态的概念.是系统资源分配的单位.



通常在一个进程中可以包含若干线程,当然一个进程中至少有一个线程,不然没有存在的意义.线程是CPU调度和执行的单位.



注意: 很多多线程是模拟出来的,真正的多线程是需要有多个CPU(即多核),如服务器.如果是模拟出来的多线程,即在一个CPU的情况下,在同一个时间点,CPU只能执行一个代码,因为切换的很快,所以就有同时进行的错觉.



**区分并发和并行的概念:**

   1、并发: 只有一个CPU,模拟出来的多条线程同时(错觉)对一个资源进行处理.

   2、并行: 多个CPU,实际中存在多条线程,多条线程同时对一个资源进行处理.



**3、核心概念**

   1、线程就是独立的执行路径

   2、在程序运行时,即使没有自己创建线程,后台也会有多个线程,如 主线程 GC线程

   3、main() 称之为主线程,为系统的入口,用于执行整个程序

   4、在一个进程中如果开辟了多个线程,线程的运行由调度器安排调度,调度器是与操作系统       紧密相关的,先后顺序是不能人为干预的

   5、对同一个资源操作时,会存在资源抢夺的问题,需要加入并发控制

   6、线程会带来额外的开销,如CPU调度时间,并发控制开销

   7、每个线程在自己的工作内存交互,内存控制不当会造成数据不一致(JMM模型)



**二. 线程创建**

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTFEGbEQCspAanib6Grub8mD50uZJyC0cFWQuJu0rfhCPhJibfEmibpjlDQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**1、继承Thread类**

（1）自定义线程类MyThread继承Thread类

（2）重写run()方法,编写线程执行体

（3）创建线程对象,调用start()方法启动线程

```
public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        System.out.println("我在敲代码");

        // 创建线程对象,并调用start()方法
        new MyThread().start();
    }
}

// 自定义线程
class MyThread extends Thread{
    // 重写 run()方法
    @Override
    public void run() {
        System.out.println("我在听音乐");
    }
}
```

**2、Runnable接口**

（1）定义MyRunnable接口实现类实现Runnable接口

（2）实现run()方法,编写线程执行体

（3）创建线程对象,传参为MyRunnable类的实例对象,调用Thread类的start()方法启动线程

```
// 传统方式
public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        System.out.println("我在敲代码");

        // 创建线程对象(使用Runnable接口的实现类作为参数),并调用start()方法
        new Thread(new MyRunnable()).start();
    }
}

// 自定义线程
class MyRunnable implements Runnable{
    // 重写 run()方法
    @Override
    public void run() {
        System.out.println("我在听音乐");
    }
}
// lambda表达式
public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        System.out.println("我在敲代码");

        // 创建线程对象(使用Runnable接口的实现类作为参数),并调用start()方法
        new Thread(()->{
            // 使用lambda表达式对run()方法进行重写
            System.out.println("我在听音乐");
        }).start();
    }
}
```

**3、Callable接口(目的就是解决以上两种创建线程并执行后没有返回值的问题)**

（1）创建实现类MyCallable实现Callable接口

（2）重写call方法,需要抛出异常(因为call方法可以设置返回值,当主线程调用这个返回值时,可能会引起主线程的阻塞)

（3）创建FutureTask实体类



原因分析: 在new  Thread()创建线程的时,我们知道方法中的参数是可以传Runnable接口的实现类的,但是并没有传Callable接口实现类的方法.因此我们要将Callable的实现类变成Runnable的实现类.这时候就需要使用FutureTask类.因为FutureTask类实现了Runnable接口,同时FutureTask类中存在传参为Callable接口的构造方法,最终可以调用Callable实现类MyCallable的call()方法

```
// FutureTask类实现了RunnableFuture接口,RunnableFuture接口继承了Runnable接口
public class FutureTask<V> implements RunnableFuture<V>{}
public interface RunnableFuture<V> extends Runnable, Future<V>{}

// FutureTask类的构造方法(传参为Callable接口)
public FutureTask(Callable<V> callable) {
        if (callable == null)
            throw new NullPointerException();
        this.callable = callable;
        this.state = NEW;       // ensure visibility of callable
}
public class Test {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        System.out.println("我在敲代码");
        // 创建FutureTask实体类(使用Callable的实现类)
        FutureTask futureTask = new FutureTask(new MyCallable());
        // 调用start()方法
        new Thread(futureTask).start();
        
        System.out.println(futureTask.get()); // get()方法可能会造成线程阻塞
    }
}

// 创建MyCallable实现类
class MyCallable implements Callable<String>{

    // 重写call()方法
    @Override
    public String call() throws Exception {
        System.out.println("我在听音乐");
        return "我在喝饮料";
    }
}
```



**4、小结**

（1）继承Thread类

子类继承Thread类具备多线程能力

启动线程: 子类对象.start();

不建议使用: 避免单继承的局限性



（2）实现Runnable接口

实现接口Runnable具有多线程能力

启动线程: new Thread(实现类对象).start();

推荐使用: 避免单继承的局限性,灵活方便,方便同一个对象被多个线程使用



**三. 线程状态**

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTd22ZZE5nQoLWpJdvPCflRODt32aQSBouMOqMhXccTUAEecPwcyYUVw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnToz2FCOTwarHiaJiby8euXH6ZcaIn6S0MKBiaRfonic3uAJeHGo0q1lsHoA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**四. 线程停止**

- 不推荐使用JDK提供的stop(),destory(),因为已经废弃
- 推荐线程自己停下来
- 或者使用一个标志位进行终止变量 当flag=false,则终止线程运行

```
    class TestStop implements Runnable{
        //1.设置一个标志位,这里不必注意其可见性,因为在while()语句块中是有sout()代码执行的,工作内存会被重新加载
        private boolean flag=true;

        @Override
        public void run() {
            int i=0;
            while (flag){
                System.out.println(Thread.currentThread().getName()+i++); // 因为有sout操作,所以会重新加载主存中的数据
            }
        }

        //2.设置一个公开的方法停止线程，转换标志位
        public void stop(){
            this.flag=false;
        }

        public static void main(String[] args) throws InterruptedException {
            TestStop testStop=new TestStop();
            new Thread(testStop,"T线程").start();
            for (int i = 0; i < 1000; i++) {
                System.out.println("main"+i);
                if(i==900){
                    //调用stop,切换标志位，让线程停止
                    testStop.stop();
                    System.out.println("T线程停止了");
                    TimeUnit.SECONDS.sleep(10);
                }
            }
        }
    }
```

**五. 线程休眠sleep(会让线程处于等待状态)**

- sleep(时间)指定当前线程阻塞的毫秒数
- sleep存在异常InterruptedException
- sleep时间达到后线程进入就绪状态
- sleep可以模拟网络延迟,倒计时等
- 每一个对象都有一个锁,sleep不会释放锁,带着锁一起休眠

**模拟网络延时,扩大问题的发生概率**

```
public class Test {
    public static void main(String[] args) {
        // 使用一个实例对象,这样票数可以固定在100
        Ticket ticket = new Ticket();

        new Thread(() -> {
            for (int i = 1; i <= 100; i++) {
                ticket.buy();
                try {
                    // 线程睡眠1秒,模拟网络延迟,扩大问题的发生概率
                    TimeUnit.SECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"小明").start();

        new Thread(() -> {
            for (int i = 1; i <= 100; i++) {
                ticket.buy();
                try {
                    // 线程睡眠1秒
                    TimeUnit.SECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"老师").start();

        new Thread(() -> {
            for (int i = 1; i <= 100; i++) {
                ticket.buy();
                try {
                    // 线程睡眠1秒
                    TimeUnit.SECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"黄牛").start();
    }
}

class Ticket{
    // 内存100张票
    private Integer tickNum = 100;

    // 买票的方法
    public void buy(){
        if(tickNum > 0){
            System.out.println(Thread.currentThread().getName()+"抢到了第"+tickNum--+"张票,剩余:"+tickNum);
        }
    }
}
```

**模拟倒计时**

```
public class Test {
    public static void main(String[] args) throws InterruptedException {
        TenDown.tenDown();
    }
}

// 倒计时的类
class TenDown{
    // 静态变量,确定倒计时的时常
    private static int num = 10;

    // 倒计时的方法
    public static void tenDown() throws InterruptedException {
        while(true){
            System.out.println(num--);
            TimeUnit.SECONDS.sleep(1);
            if(num < 0){
                break;
            }
        }
    }
}
```

**六. 线程礼让yield(会让线程处于等待状态)**

- 礼让线程,让当前正在执行的线程暂停,但不阻塞
- 将线程从运行状态转为就绪状态
- 让CPU重新调度,**礼让不一定成功!**看CPU心情 (实验了好多次,都没有成功!)

```
public class Test {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("主线程开始运行");
        new Thread(new MyYield()).start();
        TimeUnit.SECONDS.sleep(2);
        System.out.println("主线程运行中...");
    }
}

class MyYield implements Runnable{
    
    @SneakyThrows // 抛异常的注解
    @Override
    public void run() {
        System.out.println("礼让线程开始了");
        TimeUnit.SECONDS.sleep(1);
        Thread.yield(); // 线程礼让 测试过程中没成功过
        System.out.println("礼让线程结束了");
    }
}
```

**七. join合并线程**

- join合并线程,待此线程执行完成后,再执行其他线程,其他线程阻塞
- 可以想象成插队
- 尽量不要使用这个join,容易导致死锁



注意: 线程合并之前,使需要start()方法进行启动线程的,否则没有效果.因为join只是使一个线程插入进来,并不会自动启动该线程

```
public class Test {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(new MyJoin());
        thread.start(); // 线程正常启动,只有启动之后插队才有效果,插队并不会使线程直接启动
        for (int i = 0; i <= 5; i++) {
            if(i == 3){
                thread.join(); // 合并线程/插队
            }
            System.out.println("main=>"+i);
        }
    }
}

// 想要插队的线程(VIP线程)
class MyJoin implements Runnable{

    @SneakyThrows
    @Override
    public void run() {
        System.out.println("线程VIP开始执行...");
        // 睡眠5秒,模拟运行时间
        TimeUnit.SECONDS.sleep(5);
        System.out.println("线程VIP执行结束");
    }
}
```

**八. 线程状态 和 线程优先级**

**1、线程状态**

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTuBGwOuKcXRdd1yMDuXWFr8fichX63bAzoEP6DPlt19eEqegrWRFaWeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

```
// 获得指定线程的状态(6种之一)
Thread.State state = thread.getState();
System.out.println(state);
```

**2、线程的优先级**

（1）java提供一个线程调度器来监控程序中启动后进入就绪状态的所有线程,线程调度器按照优先级决定应该调度哪个线程来执行.

（2）线程优先级用数字表示,范围从1~10.

```
// 10是优先级最高,抢夺到CPU的概率最大.默认的优先级是5.
public final static int MAX_PRIORITY = 10;
public final static int NORM_PRIORITY = 5;
public final static int MIN_PRIORITY = 1;
```

（3）使用以下方式改变或获取优先级

```
// 改变线程优先级
thread.setPriority(int newPriority);

// 获取线程优先级
int priority = thread.getPriority();
```

**九. 守护线程**

- 线程分为用户线程和守护线程
- 虚拟机必须确保用户线程执行完毕
- 虚拟机不需要等待守护线程执行完毕
- 如: 后台记录操作日志,监控内存,垃圾回收等等…

thread.setDaemon(true);

```
public class Test {
    public static void main(String[] args) {
        God god = new God();
        You you = new You();

        Thread thread = new Thread(god);
        // 设置上帝线程为守护线程
        thread.setDaemon(true);
        thread.start();

        Thread thread1 = new Thread(you);
        thread1.start();
    }
}

// 守护线程
class God implements Runnable {

    @Override
    public void run() {
        while (true){
            System.out.println("上帝时刻守护着你,帮助你一辈子的开心快乐..");
        }
    }
}

// 用户线程
class You implements Runnable{

    @SneakyThrows
    @Override
    public void run() {
        for (int i = 0; i < 36500; i++) {
            System.out.println("开心快乐的度过了一生...");
        }
        System.out.println("goodBy");
    }
}
```

自己创建的线程执行完毕,守护线程永远不会执行完毕,当其他线程都结束以后,虚拟机停止工作,守护线程就随之停止了.



当然用户线程结束以后,虚拟机不能及时停下来,守护线程还会跑一会…



**十. 线程同步 synchronized**

**1、多线程带来的问题**

多线程同时操作一个对象,可能带来数据紊乱的问题(并非一定.实验过程中需要加入线程睡眠来提高出现问题的概率)

```
// 多线程操作同一个对象,买火车票
// 可能会出现多个人买到的是同一张票
public class Test {
    public static void main(String[] args) {
        // 使用一个实例对象,这样票数可以固定在100
        Ticket ticket = new Ticket();

        new Thread(ticket,"小明").start();

        new Thread(ticket,"老师").start();

        new Thread(ticket,"黄牛").start();
    }
}

class Ticket implements Runnable{
    // 内存100张票
    private Integer tickNum = 100;
    // 标记位,判定是否还有余票,有为true,没有为false
    private boolean flag = true;

    @Override
    public void run() {
        while (flag){
            this.buy();
        }
    }

    // 买票的方法
    @SneakyThrows
    public void buy(){
        if(tickNum > 0){
            TimeUnit.SECONDS.sleep(1);
            System.out.println(Thread.currentThread().getName()+"抢到了第"+tickNum--+"张票,剩余:"+tickNum);
        }else{
            flag = false; // 标记位置为false,停止循环买票
        }
    }
}
```

**2、线程同步synchronized**

（1）并发: 同一个对象被多个线程同时操作

（2）现实生活中,我们会遇到同一个资源,多个人都想使用的问题,比如: 食堂排队打饭,ATM取钱

（3）处理多线程问题时,多个线程访问同一个对象,并且某些线程还想修改这个对象的数据,这时候就需要线程同步,线程同步其实就是一种等待机制,多个需要同时访问此对象的线程进入这个对象的等待池形成队列,等待前面线程使用完毕,下一个线程再使用

（4）线程同步形成条件: 队列+锁

（5）同步锁,表示的是锁住这个对象或者这个静态方法所在的类,只能一个线程来访问这个对象或者这个类的所有静态同步方法,直到这个线程访问结束以后,释放锁,其他线程才可以访问这个对象.



由于同一进程的多个线程共享同一块存储空间,在带来方便的同时,也带来了访问冲突问题,为了保证数据在方法中被访问时的正确性,在访问时加入锁机制synchronized,当一个线程获得对象或者类的排他锁时,独占资源,其他线程必须等待,使用后释放锁即可,存在以下问题:

一个线程持有锁会导致其他所有需要此锁的线程挂起;

在多线程竞争下,加锁,释放锁会导致较多的上下文切换 和 调度延时,引起性能问题;

如果一个优先级高的线程等待一个优先级低的线程释放锁,会导致优先级倒置,引起性能问题.



**3、同步方法**

（1）由于我们可以通过private关键字来保证数据对象只能被方法访问,所以我们只需要针对方法提出一套机制,这套机制就是synchronized关键字,它包括两种用法:synchronized方法 和 synchronized块

public synchronized void method(int args){}

（2）synchronized方法控制对"对象"的访问,每个对象对应一把锁,每个synchronized方法都必须获得调用该方法的对象的锁才能执行,否则线程会阻塞.方法一旦执行,就独占该锁,直到该方法返回才释放锁,后面被阻塞的线程才能获得这个锁,继续执行.



缺陷: 若将一个大的方法申明为synchronized将会影响效率

```
public class Test{
    public static void main(String[] args) {
        BuyTicket buyTicket = new BuyTicket();
        new Thread(buyTicket,"牛逼的我").start();
        new Thread(buyTicket,"苦逼的你").start();
        new Thread(buyTicket,"万恶的黄牛").start();
    }
}

class BuyTicket implements Runnable{
 // 定义一共10张车票
    private int num=10;
    // 标记位,判定是否还有余票,有为true,没有为false
    private boolean flag = true;

    @SneakyThrows
    @Override
    public void run() {
        while (flag){
            this.buyTicket();
        }
    }

    private synchronized void buyTicket() throws InterruptedException {
        // 判断是否有票
        if (num > 0){
            // 模拟延迟
            TimeUnit.SECONDS.sleep(1);
            // 买票
            System.out.println(Thread.currentThread().getName()+"抢到了第"+num--+"张票");
        }else {
            // 没有票了
            flag = false;
            return;
        }
    }
}
```

**4、同步代码块**

（1）同步块: synchronized(Obj){}

（2）Obj称之为同步监视器

​    1、 Obj可以是任何对象,但是推荐使用共享资源作为同步监视器

​    2、同步方法中无需指定同步监视器,因为同步方法的同步监视器就是this,就是这个对象本身,或者是该类Class(static修饰的同步方法)

（3）同步监视器的执行过程

​    1、第一个线程访问,锁定同步监视器,执行其中代码.

​    2、第二个线程访问,发现同步监视器被锁定,无法访问.

​    3、第一个线程执行完毕,解锁同步监视器.

​    4、第二个线程访问,发现同步监视器没有锁,然后锁定并访问.

**同步代码块**

**锁的对象就是变化的量**

```
// 银行余额的案例
public class Test {
 public static void main(String[] args) {
        Account account = new Account(100, "银行存款");
        Drawing drawing = new Drawing(account, 50);
        Drawing drawing1 = new Drawing(account, 100);

        new Thread(drawing, "你").start();
        new Thread(drawing1, "你女朋友").start();
    }
}

// 创建银行卡实体类
@Data
class Account {
    private int money;
    private String name;

    public Account(int money, String name) {
        this.money = money;
        this.name = name;
    }
}

// 银行,模拟取款
class Drawing implements Runnable {

    private Account account; // 银行卡账户
    private int drawingMoney; // 消费的金额
    private int nowMoney; // 现在剩余的金额

    public Drawing(Account account, int drawingMoney) {
        this.account = account;
        this.drawingMoney = drawingMoney;
    }

    @SneakyThrows
    @Override
    public void run() {
        synchronized (account) {
            TimeUnit.SECONDS.sleep(1); // 睡眠1s,增大问题概率的发生
            if (account.getMoney() < drawingMoney) {
                System.out.println(Thread.currentThread().getName() + ",钱不够,取不了");
                return;
            }
            account.setMoney(account.getMoney() - drawingMoney);
            nowMoney = nowMoney + drawingMoney;
            System.out.println(account.getName() + "余额为: " + account.getMoney());

            System.out.println(Thread.currentThread().getName() + "手里的余额为:" + nowMoney);
        }
    }
}
```

**十一. 死锁**

- 多个线程各自占有一些共享资源,并且互相等待其他线程占有的资源才能运行,而导致两个或者多个线程都在等待对方释放资源,都停止执行的情形.
- 某一个同步块同时拥有"两个以上对象的锁"时,就可能会发生"死锁"的问题

```
public class Test {
    public static void main(String[] args){
        // 创建实体类
        A a = new A();
        B b = new B();
        new Thread(()->{
            synchronized (a){ // 获取到对象a的锁
                try {
                    // 延迟1s,加大死锁的出现概率
                    TimeUnit.SECONDS.sleep(1);
                    System.out.println(Thread.currentThread().getName()+"=>a");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (b){ // 想要获取对象b的锁
                    System.out.println(Thread.currentThread().getName()+"=>b");
             }
            }
        },"T1").start();

        new Thread(()->{
            synchronized (b){ // 获取到对象b的锁
                try {
                    TimeUnit.SECONDS.sleep(1); // 延迟1s,加大死锁的出现概率
                    System.out.println(Thread.currentThread().getName()+"=>b");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (a){ // 想要获取对象a的锁
                    System.out.println(Thread.currentThread().getName()+"=>a");
                }
            }
        },"T2").start();
    }
}

class A {

}

class B {

}
```

**十二. Lock锁**

- 从JDK5.0开始,java提供了更强大的线程同步机制—通过显示定义同步锁对象来实现同步.同步锁使用Lock对象充当
- java.util.concurrent.locks.Lock接口是控制多个线程对共享资源进行访问的工具.锁提供了对共享资源的独占访问,每次只能有一个线程对Lock对象加锁,线程开始访问共享资源之前应先获得Lock对象
- ReentrantLock实现了Lock,它拥有于synchronized相同的并发性和内存语义,在实现线程安全的控制中,比较常用的是ReentrantLock,可以显示加锁,释放锁.

```
// 简单事例
class A{
    private static Lock lock = new ReentrantLock();

    public void m(){
        lock.lock(); // 加锁
        try{
            // 需要保证线程安全的代码
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock(); // 解锁
        }
    }
}
// 多线程操作同一个对象,买火车票
// 可能会出现多个人买到的是同一张票
public class Test {
    public static void main(String[] args) {
        // 使用一个实例对象,这样票数可以固定在100
        Ticket ticket = new Ticket();

        new Thread(ticket,"小明").start();

        new Thread(ticket,"老师").start();

        new Thread(ticket,"黄牛").start();
    }
}

class Ticket implements Runnable{
    // 内存100张票
    private Integer tickNum = 100;
    // 标记位,判定是否还有余票,有为true,没有为false
    private boolean flag = true;
    //
    private static Lock lock = new ReentrantLock();

    @Override
    public void run() {
        while (flag){
            this.buy();
        }
    }

    // 买票的方法
    @SneakyThrows
    public void buy(){
        lock.lock(); // 加锁
        try {
            if(tickNum > 0){
                TimeUnit.SECONDS.sleep(1);
                System.out.println(Thread.currentThread().getName()+"抢到了第"+tickNum--+"张票,剩余:"+tickNum);
            }else{
                flag = false;
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock(); // 解锁
        }
    }
}
```

**synchronized与Lock的对比**

- synchronized是一个内置的java关键字; Lock是一个java类
- synchronized无法判断是否获取了锁; Lock可以判断是否获取到了锁
- Lock是显式锁(手动开启和关闭锁,别忘记关闭锁) synchronized是隐式锁,出了作用域自动释放
- synchronized线程1(获得锁,阻塞),线程2(等待); Lock锁就不一定会等待下去;(lock.tryLock())
- synchronized可重入锁,不可以中断的,非公平;Lcok可重入锁,可以中断锁,可以自己设置(是否公平锁)
- Lock只有代码块锁,synchronized有代码块锁和方法锁
- synchronized适合锁少量的代码同步问题;Lock适合锁大量的同步代码
- 使用Lock锁,JVM将花费较少的时间来调度线程,性能更好.并且具有更好的扩展性(提供更多的子类)

**十三. 线程通信 wait;notify**

**1、线程通信用到的方法: wait, notify**

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTS41BCujuTRsiaUg4MQfbtzWia6OPl6BaC9zPmzlIlZWQMRTrAloIyFJw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



（1）使用 synchronized 关键字配合 wait() 以及 notify() 实现的生产者消费者模式

```
class A {
    public static void main(String[] args) {
        Data data = new Data();

        new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    data.increment();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "A").start();

        new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    data.decrement();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "B").start();
    }
}

// 判断等待,业务,通知
class Data { // 数字 资源类

    private int num = 0;

    // +1
    public synchronized void increment() throws InterruptedException {
        // 注意: 此处判断应使用while(),不要使用if();因为当该线程被唤醒时,仍需要进行判断
        while (num != 0) {
            // 等待
            this.wait();
        }
        num++;
        System.out.println(Thread.currentThread().getName() + "=>" + num);
        // 通知其他线程
        this.notifyAll();
    }

    // -1
    public synchronized void decrement() throws InterruptedException {
        // 注意: 此处判断应使用while(),不要使用if();因为当该线程被唤醒时,仍需要进行判断
        while (num == 0) {
            // 等待
            this.wait();
        }
        num--;
        System.out.println(Thread.currentThread().getName() + "=>" + num);
        // 通知其他线程
        this.notifyAll();
    }
}
```

（2）使用 lock 锁实现的生产者消费者模式

```
public class B {
    public static void main(String[] args) {
        Data2 data = new Data2();

        new Thread(()->{
            try {
                for (int i = 0;i < 10; i++) {
                    data.increment();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"A").start();

        new Thread(()->{
            try {
                for (int i = 0;i <10;i ++) {
                    data.decrement();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"B").start();
    }
}

// 判断等待,业务,通知
class Data2{ // 数字 资源类

    private int num = 0;

    Lock lock = new ReentrantLock();
    Condition condition = lock.newCondition();

    // +1
    public  void increment() throws InterruptedException {
        // 加锁
        lock.lock();
        try {
            // 注意: 此处判断应使用while(),不要使用if();因为当该线程被唤醒时,仍需要进行判断
            while (num != 0){
                // 等待
                condition.await();
            }
            num ++;
            System.out.println(Thread.currentThread().getName()+"=>"+num);
            // 唤醒其他线程
            condition.signalAll();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 解锁
            lock.unlock();
        }
    }

    // -1
    public void decrement() throws InterruptedException {
        // 加锁
        lock.lock();
        try {
            // 注意: 此处判断应使用while(),不要使用if();因为当该线程被唤醒时,仍需要进行判断
            while (num == 0){
                // 等待
                condition.await();
            }
            num --;
            System.out.println(Thread.currentThread().getName()+"=>"+num);
            // 唤醒其他线程
            condition.signalAll();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // 解锁
            lock.unlock();
        }
    }
}
```

（3）使用lock锁进阶,实现精准线程唤醒

```
// A执行完调用B,B执行完调用C,C执行完调用A
// 精准线程唤醒
public class C {

    public static void main(String[] args) {

        Data3 data3 = new Data3();

        new Thread(()->{
            for (int i = 0;i<10;i++) {
                data3.printA();
            }
        },"A").start();

        new Thread(()->{
            for (int i = 0;i<10;i++) {
                data3.printB();
            }
        },"B").start();

        new Thread(()->{
            for (int i = 0;i<10;i++) {
                data3.printC();
            }
        },"C").start();
    }
}

class Data3{

    private Lock lock = new ReentrantLock();
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();
    private int num = 1; // 1A 2B 3C

    public void printA(){
        // 加锁
        lock.lock();
        try {
            // 注意: 此处判断应使用while(),不要使用if();因为当该线程被唤醒时,仍需要进行判断
            while(num != 1){
                // A线程等待
                condition1.await();
            }
            System.out.println(Thread.currentThread().getName()+"=> AAAAA");

            num = 2;
            // 唤醒B线程
            condition2.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 解锁
            lock.unlock();
        }

    }

    public void printB(){
        // 加锁
        lock.lock();
        try {
            // 注意: 此处判断应使用while(),不要使用if();因为当该线程被唤醒时,仍需要进行判断
            while(num != 2){
                // B线程等待
                condition2.await();
            }
            System.out.println(Thread.currentThread().getName()+"=> BBBBB");
            num = 3;
            // 唤醒C线程
            condition3.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 解锁
            lock.unlock();
        }
    }

    public void printC(){
        // 加锁
        lock.lock();
        try {
            // 注意: 此处判断应使用while(),不要使用if();因为当该线程被唤醒时,仍需要进行判断
            while(num != 3){
                // C线程等待
                condition3.await();
            }
            System.out.println(Thread.currentThread().getName()+"=> CCCCC");
            num = 1;
            // 唤醒A线程
            condition1.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // 解锁
            lock.unlock();
        }
    }
}
```

**2、管程法**

生产者消费者模型: 需要两个线程之间进行通信.通信的方式可以有两种: 管程法;信号灯法.

管程法通过容器控制,也就是设置缓冲区

（1）生产者: 负责生产数据的模块(可能式方法,对象,线程,进程);

（2）消费者: 负责处理数据的模块(可能是方法,对象,线程,进程);

（3）缓冲区: 消费者不能直接使用生产者的数据,他们之间有个"缓冲区",生产者将生产好的数据放入缓冲区,消费者从缓冲区拿出数据

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTknKEuNIcMlNDuYAlvY0sdibf13yghFTK6LicSSDQe8NEqZ7HYSUaib9OA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

```
public class Test{
    public static void main(String[] args) {
        Container container = new Container();
        Producer producer = new Producer(container);
        Consumer consumer = new Consumer(container);

        new Thread(producer,"生产者").start();
        new Thread(consumer,"消费者").start();
    }
}

// 生产者
class Producer implements Runnable{
    // 容器对象(管程)
    Container container;

    public Producer(Container container) {
        this.container = container;
    }

    @Override
    public void run() {
        for(int i = 1;i<=100;i++){
            container.push(new Chicken(i));
        }
    }
}

// 消费者
class Consumer implements Runnable{
    // 容器对象(管程)
    Container container;

    public Consumer(Container container) {
        this.container = container;
    }

    @Override
    public void run() {
        for(int i = 1;i<=100;i++){
            container.pop();
        }
    }
}


class Container{
    // 创建数组作为容器使用
    private Chicken[] chickens = new Chicken[10];
    // 容器的计数器
    private int sum = 0;

    // 生产者调用的方法
    @SneakyThrows
    public synchronized void push (Chicken chicken){
        while (sum == 10){
            // 生产者等待
            this.wait();
        }
        chickens[sum] = chicken;
        System.out.println(Thread.currentThread().getName()+"生产了第"+sum+"只小鸡,小鸡的编号为:"+chicken.id);
        sum ++;
        // 解除所有的线程等待
        this.notifyAll();
    }

    // 消费者调用的方法
    @SneakyThrows
    public synchronized Chicken pop(){
        while (sum == 0){
            // 消费者等待
            this.wait();
        }
        sum --;
        Chicken chicken = chickens[sum];
        System.out.println(Thread.currentThread().getName()+"消费了第"+sum+"只小鸡,小鸡的编号为:"+chicken.id);
        // 解除所有的线程等待
        this.notifyAll();
        return chicken;
    }
}

// 小鸡的实体类
class Chicken{
    public int id;

    public Chicken(int id){
        this.id = id;
    }
}
```

**3、信号灯法**

```
// 测试类
public class Test {
    public static void main(String[] args) {
        Restaurant restaurant = new Restaurant();
        new Thread(new Producer(restaurant),"生产者").start();
        new Thread(new Consumer(restaurant),"消费者").start();
    }
}

// 生产者(生产食物)
class Producer implements Runnable{
    // 餐厅对象
    private Restaurant restaurant;

    public Producer(Restaurant restaurant) {
        this.restaurant = restaurant;
    }

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            restaurant.make("汉堡");
        }
    }
}

// 消费者(消费食物)
class Consumer implements Runnable{
    // 餐厅对象
    private Restaurant restaurant;

    public Consumer(Restaurant restaurant) {
        this.restaurant = restaurant;
    }

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            restaurant.eat();
        }
    }
}

// 餐厅(生产者和消费者需要调用的方法)
class Restaurant{
    // 生产者生产的食物信息
    private String food;
    // 使用boolean类型的数据作为信号灯,确保两个线程的交替运行
    private boolean flag = false;

    @SneakyThrows
    public synchronized void make(String str){
        // 需要先生产食物,所以flag初始化为false
        if(flag){
            // 生产者线程等待
            this.wait();
        }
        // 生产食物
        this.food = str;
        System.out.println("制作了"+str);
        this.notifyAll();
        flag = !flag;
    }

    @SneakyThrows
    public synchronized void eat(){
        if(!flag){
            // 消费者线程等待
            this.wait();
        }
        // 消费食物
        System.out.println("吃了"+food);
        this.notifyAll();
        flag = !flag;
    }
}
```



**十四. 8锁问题**

**为了清楚的认识到锁的对象具体是什么,特说明8锁问题**

```
/**
 * 8锁,就是关于锁的8个问题
 * 1. 标准情况下,两个线程先打印 发短息 还是 打电话?
 *      发短信
 */
public class Lock8_1 {

    public static void main(String[] args) throws InterruptedException {
        // 两个对象,两个
        Phone1 phone = new Phone1();
        new Thread(() -> {
            phone.sendSms();
        }, "A").start();

        // 主线程休息1秒
        TimeUnit.SECONDS.sleep(1);

        new Thread(() -> {
            phone.call();
        }, "B").start();
    }
}

class Phone1 {

    public synchronized void sendSms() {
        System.out.println("发短信");
    }

    public synchronized void call() {
        System.out.println("打电话");
    }
}
/**
 * 8锁,就是关于锁的8个问题
 * 2. 发短信线程延迟4秒,两个线程先打印 发短息 还是 打电话?
 *      发短信  (因为发短信先获得锁)
 */
public class Lock8_2 {

    public static void main(String[] args) throws InterruptedException {
        Phone2 phone = new Phone2();
        new Thread(() -> {
            phone.sendSms();
        }, "A").start();

        // 主线程休息1秒
        TimeUnit.SECONDS.sleep(1);

        new Thread(() -> {
            phone.call();
        }, "B").start();
    }
}

class Phone2 {


    public synchronized void sendSms() {
        try {
            // 分支线程暂停4秒
            TimeUnit.SECONDS.sleep(4);
            System.out.println("发短信");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public synchronized void call() {
        System.out.println("打电话");
    }
}
```

 

```
/**
 * 8锁,就是关于锁的8个问题
 * 3. 增加一个普通方法hello,先执行 发短信 打电话 还是 hello
 *      hello
 */
public class Lock8_3 {

    public static void main(String[] args) throws InterruptedException {
        Phone3 phone = new Phone3();
        new Thread(() -> {
            phone.sendSms();
        }, "A").start();

        // 主线程休息1秒
        TimeUnit.SECONDS.sleep(1);

        new Thread(() -> {
            phone.call();
        }, "B").start();

        new Thread(()->{
            phone.hello();
        },"C").start();
    }
}

class Phone3 {

    public synchronized void sendSms() {
        try {
            // 分支线程暂停4秒
            TimeUnit.SECONDS.sleep(4);
            System.out.println("发短信");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public synchronized void call() {
        System.out.println("打电话");
    }

    public void hello(){
        System.out.println("hello");
    }
}
/**
 * 8锁,就是关于锁的8个问题
 * 4. 两个对象,两个同步方法,先执行 发短信 还是 打电话?
 *      打电话
 */
public class Lock8_4 {

    public static void main(String[] args) throws InterruptedException {
        Phone4 phone1 = new Phone4();
        Phone4 phone2 = new Phone4();
        new Thread(() -> {
            phone1.sendSms();
        }, "A").start();

        // 主线程休息1秒
        TimeUnit.SECONDS.sleep(1);

        new Thread(() -> {
            phone2.call();
        }, "B").start();
    }
}

class Phone4 {

    // synchronized 锁的对象是方法的调用者
    // 两个方法用的是同一个锁,谁先拿到谁执行!
    public synchronized void sendSms() {
        try {
            // 分支线程暂停4秒
            TimeUnit.SECONDS.sleep(4);
            System.out.println("发短信");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public synchronized void call() {
        System.out.println("打电话");
    }
}
/**
 * 8锁,就是关于锁的8个问题
 * 5. 两个静态的同步方法,先执行 发短信 还是 打电话
 *      发短信
 */
public class Lock8_5 {

    public static void main(String[] args) throws InterruptedException {
        Phone5 phone = new Phone5();
        new Thread(() -> {
            Phone5.sendSms();
        }, "A").start();

        // 主线程休息1秒
        TimeUnit.SECONDS.sleep(1);

        new Thread(() -> {
            Phone5.call();
        }, "B").start();
    }
}

// Phone5 唯一的一个Class对象
class Phone5 {

    // synchronized 锁的对象是方法的调用者!
    // static 静态方法
    // 类一加载就有了!锁的是 Class 模板
    public static synchronized void sendSms() {
        try {
            // 分支线程暂停4秒
            TimeUnit.SECONDS.sleep(4);
            System.out.println("发短信");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static synchronized void call() {
        System.out.println("打电话");
    }
}
/**
 * 8锁,就是关于锁的8个问题
 * 6. 两个对象!两个静态的同步方法,先打印 发短信 还是 打电话?
 *      发短信
 */
public class Lock8_6 {

    public static void main(String[] args) throws InterruptedException {
        // 两个对象的Class类模板只有一个,static,锁的是Class
        Phone6 phone1 = new Phone6();
        Phone6 phone2 = new Phone6();
        new Thread(() -> {
            Phone5.sendSms();
        }, "A").start();

        // 主线程休息1秒
        TimeUnit.SECONDS.sleep(1);

        new Thread(() -> {
            Phone5.call();
        }, "B").start();
    }
}

class Phone6 {

    // synchronized 锁的对象是方法的调用者!
    // static 静态方法
    // 类一加载就有了!锁的是 Class 模板
    public static synchronized void sendSms() {
        try {
            // 分支线程暂停4秒
            TimeUnit.SECONDS.sleep(4);
            System.out.println("发短信");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static synchronized void call() {
        System.out.println("打电话");
    }
}
/**
 * 8锁,就是关于锁的8个问题
 * 7. 一个静态同步方法,一个普通同步方法,一个对象.先打印 发短信 还是 打电话
 *      打电话
 */
public class Lock8_7 {

    public static void main(String[] args) throws InterruptedException {
        // 两个对象的Class类模板只有一个,static,锁的是Class
        Phone7 phone = new Phone7();
        new Thread(() -> {
            Phone5.sendSms();
        }, "A").start();

        // 主线程休息1秒
        TimeUnit.SECONDS.sleep(1);

        new Thread(() -> {
            phone.call();
        }, "B").start();
    }
}

class Phone7 {

    // 静态的同步方法,锁的是Class 类模板
    public static synchronized void sendSms() {
        try {
            // 分支线程暂停4秒
            TimeUnit.SECONDS.sleep(4);
            System.out.println("发短信");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    // 普通的同步方法,锁的是 调用者
    public synchronized void call() {
        System.out.println("打电话");
    }
/**
 * 8锁,就是关于锁的8个问题
 * 8. 一个静态同步方法,一个普通同步方法,两个个对象.先打印 发短信 还是 打电话
 *      打电话
 */
public class Lock8_8 {

    public static void main(String[] args) throws InterruptedException {
        // 两个对象的Class类模板只有一个,static,锁的是Class
        Phone8 phone1 = new Phone8();
        Phone8 phone2 = new Phone8();
        new Thread(() -> {
            phone1.sendSms();
        }, "A").start();

        // 主线程休息1秒
        TimeUnit.SECONDS.sleep(1);

        new Thread(() -> {
            phone2.call();
        }, "B").start();
    }
}

class Phone8 {

    // 静态的同步方法,锁的是Class 类模板
    public static synchronized void sendSms() {
        try {
            // 分支线程暂停4秒
            TimeUnit.SECONDS.sleep(4);
            System.out.println("发短信");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    // 普通的同步方法,锁的是 调用者
    public synchronized void call() {
        System.out.println("打电话");
    }
}
```

**十五. 多线程下****的集合**

**由于普通的ArrayList,LinkedList等都不是线程同步下的类,因此在多线程并发的情况下是不安全的.所以需要使用JUC(java.util.concurrent)下的类**

- 线程安全的List

List<String> list = new Vector<>();

List<String> list = Collections.synchronizedList(new ArrayList<>());

List<String> list = new CopyOnWriteArrayList<>();

- 线程安全下的Set

Set<String> set = Collections.synchronizedSet(new HashSet<>());

Set<String> set = new CopyOnWriteArraySet();

- 线程安全下的Map

Map<String,String> map = Collections.synchronizedMap(new HashMap<>());

Map<String,String> map = new ConcurrentHashMap<>();



**十六. 多线程中常用的辅助类**

- CountDownLatch 减法计数器

**每次有线程调用,countDown()方法使其数量-1,假设计数器变为0,countDownLatch.await();就会被唤醒,继续向下执行!**

**作用: 当存在必须要执行的任务的时候可以考虑使用**

```
// 计数器
public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        // 总数是6,必须要执行任务的时候,再使用!
        CountDownLatch countDownLatch = new CountDownLatch(6);

        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"Go Out!");
                countDownLatch.countDown(); // 数量-1
            },String.valueOf(i)).start();
        }
  
        countDownLatch.await(); // 等待计数器归零,然后再向下执行

        System.out.println("Close Door");
    }
}
```

- CyclicBarrier 加法计数器

创建加法计数器工具对象,让执行的线程数达到7个时,会唤醒 cyclicBarrier.await()的线程,并且会执行参数中的 “召唤神龙” 方法,同时执行主线程后面的代码



作用: 当存在必须要执行的任务的时候可以考虑使用

```
public class CyclicBarrierDemo {
    public static void main(String[] args) throws InterruptedException {
        /**
         * 集齐七颗龙珠召唤神龙
         */
        // 召唤龙珠的线程
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,()->{
            System.out.println("召唤神龙成功!"); // 执行此方法的同时,执行主线程的代码
        });

        for (int i = 1; i <= 7; i++) {
            final int temp = i;
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"收集"+temp+"颗龙珠");

                try {
                    cyclicBarrier.await(); // 线程等待

                    System.out.println(temp);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            },String.valueOf(i)).start();
        }

        Thread.sleep(1000);
        System.out.println("123");

    }
}
```

- Semaphore 信号量

作用: 1. 多个共享资源互斥时使用

​     \2. 并发限流,控制最大的线程数!

```
public class SemaphoreDemo {
    public static void main(String[] args) {
        // 获得信号量对象,表示最多只有3个线程可以被同时唤醒使用
        Semaphore semaphore = new Semaphore(3);

        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                try {
                    // acquire() 获得1个线程使用.如果达到设定的最大线程数,则等待线程被释放为止
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"抢到车位");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName()+"离开车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    // release() 释放当前线程.会将当前的信号量释放,然后唤醒等待的线程!
                    semaphore.release();
                }
            },String.valueOf(i)).start();
        }
    }
}
```

**十五. 读写锁ReadWriteLock**

多个线程进行读和写的操作时,同一时刻: 可以使多个线程进行读操作,只能有一个线程进行写操作

```
/**
 * 独占锁(写锁) 一次只能被一个线程占有
 * 共享锁(读锁) 一次可以被多个线程占有
 * ReadWriteLock
 *  读 - 读 可以共存
 *  读 - 写 不能共存
 *  写 - 写 不能共存
 */
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCacheLock = new MyCacheLock();
        // 写入
        for (int i = 1;i <= 5;i ++) {
            final int temp = i;
            new Thread(()->{
                myCache.put(temp+"",temp+"");
            }, String.valueOf(i)).start();
        }

        // 读取
        for (int i = 1;i <= 5;i ++) {
            final int temp = i;
            new Thread(()->{
                myCache.get(temp+"");
            }, String.valueOf(i)).start();
        }
    }
}

// 自定义缓存
class MyCacheLock{

    private volatile Map<String,Object> map = new HashMap<>();
    // 读写锁: 更加细粒度的控制
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    // 存,写 写入的时候只希望同时只有一个线程写
    public void put(String key,Object value){

        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+"写入"+key);
            map.put(key,value);
            System.out.println(Thread.currentThread().getName()+"写入OK");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.writeLock().unlock();
        }

    }

    // 取,读 所有人都可以进行读
    public void get(String key){
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+"读取"+key);
            map.get(key);
            System.out.println(Thread.currentThread().getName()+"读取OK");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.readLock().unlock();
        }
    }
}
```

**十六. 阻塞队列(线程池底层所用)**

BlockingQueue 类似于 List



BlockingQueue 接口继承了 Queue 接口, Queue 接口继承了Collection 接口;其同级有: Dequeue(双端队列),以及 AbstractQueue (非阻塞队列)



BlockingQueue 接口的实现类有: LinkedBlockingQueue , ArrayBlockingQueue 等



阻塞队列有四组常用的API

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTYCpH1gL0KPMyvgrWlLhicPwdAuEoqqbH2Hz45NEmB8PCV9ZLUQ0R6Vg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

```
 // 抛出异常
    public void test1(){
        // 队列的大小
        ArrayBlockingQueue<Object> blockingQueue = new ArrayBlockingQueue<>(3);
  
        // 添加元素,返回boolean值. 如果队列满了,还添加,会抛出异常java.lang.IllegalStateException: Queue full
        System.out.println(blockingQueue.add("a"));
        System.out.println(blockingQueue.add("b"));
        System.out.println(blockingQueue.add("c"));

        System.out.println(blockingQueue.element()); // 查看队首元素
        // java.lang.IllegalStateException: Queue full 抛出异常!
        // System.out.println(blockingQueue.add("d"));

        System.out.println("============");

        // 移除元素,返回移除的元素.如果队列空了,还移除,会抛出异常java.util.NoSuchElementException
        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());
        System.out.println(blockingQueue.remove());

        // java.util.NoSuchElementException 抛出异常!
        System.out.println(blockingQueue.remove());

    }
 // 有返回值,不抛出异常
    public void test2(){
        ArrayBlockingQueue<Object> blockingQueue = new ArrayBlockingQueue<>(3);

        // 添加元素,返回boolean值.如果队列满了,还添加,会返回一个false
        System.out.println(blockingQueue.offer("a"));
        System.out.println(blockingQueue.offer("b"));
        System.out.println(blockingQueue.offer("c"));

        // System.out.println(blockingQueue.offer("d")); // false 不抛出异常!

        System.out.println(blockingQueue.peek()); // 检测队首元素
        System.out.println("==============");

        // 移除元素,返回移除的元素.如果队列空了,还移除,会返回null
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());

        // System.out.println(blockingQueue.poll()); // 返回null
    }
 // 等待,阻塞(一直阻塞)
    public void test3() throws InterruptedException {
        ArrayBlockingQueue<Object> blockingQueue = new ArrayBlockingQueue<>(3);
 
        // 添加元素,无返回值,如果队列满了,还添加,会一直处于等待状态
        blockingQueue.put("a");
        blockingQueue.put("b");
        blockingQueue.put("c");
        // blockingQueue.put("d"); // ,队列没有位置了,一直阻塞

        // 移除元素,返回移除的元素,如果队列空了,还移除,会一直处于等待状态
        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());
        System.out.println(blockingQueue.take());
        // System.out.println(blockingQueue.take()); // 队列空了,一直阻塞
    }
 // 等待,阻塞(等待超时)
    public void test4() throws InterruptedException {
        ArrayBlockingQueue<Object> blockingQueue = new ArrayBlockingQueue<>(3);

        // 添加元素,返回一个boolean值,如果队列满了,还添加,会等待2s,然后返回false
        blockingQueue.offer("a");
        blockingQueue.offer("b");
        blockingQueue.offer("c");

        // blockingQueue.offer("d",2,TimeUnit.SECONDS); // 等待超过2秒就退出

        System.out.println("============");

        // 移除元素,返回移除的元素,如果队列空了,还移除,会等待2s,然后返回null
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll());
        System.out.println(blockingQueue.poll(2,TimeUnit.SECONDS));

    }
```

**十七. 同步队列**

SynchronousQueue synchronousQueue = new SynchronousQueue();



同步队列和其他的**BlockingQueue不一样,SynchronousQueue不存储元素,put了一个元素,必须从里面take取出来,否则不能再put**进去.

```
/**
 * 同步队列
 * 和其他的BlockingQueue不一样,SynchronousQueue 不存储元素
 * put了一个元素,必须从里面take取出来,否则不能咋再put进去
 */
public class SynchronousQueueDemo {
    public static void main(String[] args) {
        // 同步队列
        SynchronousQueue synchronousQueue = new SynchronousQueue();

        new Thread(()->{
            try {
                System.out.println(Thread.currentThread().getName()+"put 1");
                synchronousQueue.put(1);
                System.out.println(Thread.currentThread().getName()+"put 2");
                synchronousQueue.put(2);
                System.out.println(Thread.currentThread().getName()+"put 3");
                synchronousQueue.put(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"T1").start();

        new Thread(()->{
            System.out.println();
            try {
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName()+"take "+synchronousQueue.take());
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName()+"take "+synchronousQueue.take());
                TimeUnit.SECONDS.sleep(2);
                System.out.println(Thread.currentThread().getName()+"take "+synchronousQueue.take());
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"T2").start();
    }
}
```

**十八. 线程池**

线程的创建和销毁十分消耗资源,因此引入了线程池技术,能够使得线程重复使用,就是为了解决这一问题.

**1、线程池优点**

减少了创建和销毁线程的次数,每个工作线程都可以被重复利用,可执行多个任务.

可以根据系统的承受能力,调整线程池中工作线程的数目,防止因为消耗过多的内存,而把服务器累趴下(每个线程需要大约1MB内存,线程开的越多,消耗的内存也就越大,最后死机).

**2、线程池继承关系**

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnT85hmkIcjJNHG0Tficc9DCabJ2h0Ezy6vHPu4Hl2iaXX8BGUFMpZIciaHw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



java里面线程池的顶级接口是Executor,但是严格意义上讲Executor并不是一个线程池,而只是一个执行线程的工具.真正的线程池接口是**ExecutorService**.



**比较重要的几个类**

（1）ExecutorService: 真正的线程池接口.

（2）ScheduledExecutorService: 能和Timer/TimerTask类似,解决那么需要任务重复执行的问题.

（3）ThreadPoolExecutor: **ExecutorService**的默认实现

（4）ScheduledThreadPoolExecutor: 

继承**ThreadPoolExecutor的ScheduledExecutorService**接口实现,周期性任务调度的类实现.

要配置一个线程池是比较复杂的,尤其是对于线程池的原理不是很清楚的情况下,很有可能配置的线程池不是较优的,因此在Executors类里面提供了一些静态工厂,生成一些常用的线程池.

**3、如何创建线程池以及线程池参数**

Executor是Java中的一个接口,其默认实现类是ThreadPoolExecutor,构造方法如下：

```
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler)
```

我们可以通过**ThreadPoolExecutor**来创建一个线程池



**线程池七大参数参考:**

（1）corePoolSize: 核心线程的数量

（2）maximumPoolSize: 线程的最大线程数

（3）workQueue: 阻塞队列及其自定义容量

（4）keepAliveTime: 非核心线程没有任务时,最多保持多久时间终止

（5）unit: **keepAliveTime**的时间单位

（6）threadFactory:

 线程工厂,用来创建线程,一般使用**Executors.defaultThreadFactory()**

（7）handler: 当拒绝处理任务时的策略

其中**maximumPoolSize**的定义可以考虑CPU密集型或者IO密集型

CPU密集型: 计算机几核,就设置为几,可以保证CPU效率最高

​     1、获取cpu的核数

```
获取cpu的核数
System.out.println(Runtime.getRuntime().availableProcessors()); // 8
```

​    2、IO密集型: 判断程序中十分消耗IO的线程数,只要大于这个数量即可.(一般设置为2倍)



**线****程池的工作原理,需要结合重要参数**

当线程池提交一个新的任务时,线程池有三种处理情况,分别是: 创建一个工作线程来执行该任务; 将任务加入阻塞队列; 拒绝该任务.

a. 当工作线程数小于核心线程数时,直接创建新的核心工作线程;

b. 当工作线程数不小于核心线程数时, 就需要尝试将新的任务添加到阻塞队列中去;

c. 如果加入失败, 则说明队列已经满了, 那么这时就需要创建新的"临时"工作线程来执行任务;

d. 如果创建成功, 则直接执行该任务, 如果创建失败, 则说明工作线程数已经等于最大线程数了,则只能拒绝该任务了;

**线程池的四种拒绝策略**

拒绝策略是一个**RejectedExecutionHandler**类型的变量, 用户可以自行指定拒绝的策略, 如果不指定的话, 线程池将使用默认的拒绝策略: 抛出异常

（1）AbortPolicy: 默认的拒绝策略,如果阻塞队列满了,还有元素进入,抛出异常

（2）CallerRunsPolicy: 哪来的回哪里!任务仍然会被执行,可能是main线程中执行(阻塞队列满了,回到上一级线程中执行)

（3）DiscardPolicy: 阻塞队列满了,任务会被丢弃,不会抛出异常

（4）DiscardOldestPolicy: 队列满了,尝试去和最早(并不是队首元素)的竞争,竞争失败则丢弃,竞争成功则执行,不会抛出异常

**提交线程池的任务**

线程池提交任务时**submit()和execute()**的区别:

（1）**execut()可以添加一个Runable任务,submit()不仅可以添加Runable任务还可以添加Callable**任务.

（2）**execut()没有返回值,而submit()在添加Callable**任务时会有返回值,可以通过返回值来查看线程执行的情况.

（3）如果发生异常**submit()可以通过Future.get()捕获抛出的异常,而execut()**会终止这个线程.

**4、系统封装的4种线程池**

可以发现,系统提供的4种线程池,最终都是通过**ThreadPoolExecutor**的不同参数,来巧妙的达到不同的线程管理效果

（1）Executors.newSingleThreadExecutor()

创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行。

（2）Executors.newFixedThreadPool(5)

创建固定大小的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。

（3）Executors.newCachedThreadPool()

创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说JVM）能够创建的最大线程大小。

（4）Executors.newSingleThreadExecutor()

创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求。



但是封装好的线程池有一些问题：newSingleThreadExecutor和newFixedThreadPool中的等待对列的长度在底层设置为Integer.MAX_VALUE，newCachedThreadPool创建线程的数目最大也可以为Integer.MAX_VALUE，这样可能会导致out of memory(OOM)。所以一般公司要求自己创建线程池，不可以使用工具类ExecutorService executor =  Executors.newFixedThreadPool(3);来创建线程池。



**5、线程池的五种状态**

（1）RUNNING: -1<<COUNT_BITS,即高3位为1,低29位为0,该状态的线程池会接收新任务,也会处理在阻塞队列种等待处理的任务

（2）SHUTDOWN: 0<<COUNT_BITS,即高3位为0,低29位为0,该状态的线程池不会再接收新任务,但还会处理以及提交到阻塞队列种等待处理的任务

（3）STOP: 1<<COUNT_BITS,即高3位为001,低29位为0,该状态的线程池也不会再接收新任务,不会处理在阻塞队列中等待的任务,而且还会中断正在运行的任务

（4）TIDYING: 2<<COUNT_BITS,即高3位为010,低29位为0,所有任务都被终止了,**workerCount为0,为此状态时还将调用terminated()**方法

（5）TERMINATED: 3<<COUNT_BITS,即高3位为100,低29位为0,**terminated()**方法调用完成后变成此状态



这些状态均由int型表示, 大小关系为RUNNING<SHUTDOWN<STOP<TIDYING<TERMINATED,这个顺序基本上也是遵循线程池从 运行 到 终止这个过程.

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTwkiaOlGWnia0Bpice59ib7Bc8AVvXqElVk5QMPicGgUnQJk32e7qTgdTMvg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





**十九. ForkJoin**

利用多线程,实现将一个大任务分为多个小任务并行执行(特别大的计算量的情况下使用),可以节约时间.(不过我测试的三种方法,并没有发现节约时间…)



特点: 工作窃取

两个双向队列A和B,两条线程同时执行,均为从上向下执行,当A线程将队列中的任务全部执行完成之后,不应该让这条线程处于等待状态,因 为两条队列都完成之后的结果合并才是最终的结果.A线程会从下往上的窃取B队列中的任务进行执行,从而提高效率.

```
/**
 * 求和计算的任务
 * 如何使用ForkJoin
 *  1. forkJoinPool 通过它来执行
 *  2. 计算任务forkJoinPool.execute(ForkJoinTask task)
 *  3. 计算类要继承ForkJoinTask(RecursiveTask的父类)
 */
public class ForkJoinDemo extends RecursiveTask<Long> {

    private long start;
    private long end;

    // 临界值,小于该值时直接进行运算,大于该值时需要拆分任务,多个线程计算
    private long temp = 10000L;

    public ForkJoinDemo(long start, long end) {
        this.start = start;
        this.end = end;
    }

    // 计算方法
    @Override
    protected Long compute() {
        if((end-start)<temp){
            long sum = 0L;
            for(Long i =start;i<=end;i++){
                sum += i;
            }
            return sum;
        }else{ // forkJoin 递归
            long middle = (start+end)/2; // 中间值
            ForkJoinDemo task1 = new ForkJoinDemo(start, middle);
            task1.fork(); // 拆分任务,把任务压入线程队列
            ForkJoinDemo task2 = new ForkJoinDemo(middle+1,end);
            task2.fork(); // 拆分任务,把任务压入线程队列

            return task1.join() + task2.join();
        }
    }
}
```



测试类: 计算0~1_0000_0000的和

```
// test1 为for循环
// test2 为forkJoin
// test3 为Stream流计算
public class Test {

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        // test1(); // 时间: 424
        // test2(); // 时间: 3699
        test3(); // 时间: 163
    }

    // 普通方法进行计算
    public static void test1(){
        long sum = 0;
        long start = System.currentTimeMillis();
        for (long i = 1; i <= 10_0000_0000; i++) {
            sum += i;
        }
        long end = System.currentTimeMillis();
        System.out.println("sum="+sum+",时间: "+(end-start));
    }

    // 使用ForkJoin函数进行计算
    public static void test2() throws ExecutionException, InterruptedException {
        long start = System.currentTimeMillis();

        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinTask<Long> task = new ForkJoinDemo(0L,10_0000_0000L);
        ForkJoinTask<Long> submit = forkJoinPool.submit(task); // 提交任务
        Long sum = submit.get(); // 获得结果

        long end = System.currentTimeMillis();
        System.out.println("sum="+sum+",时间: "+(end-start));
    }

    // stream流式计算
    public static void test3(){
        long start = System.currentTimeMillis();
        // Stream并行流()
        long sum = LongStream.rangeClosed(0L, 10_0000_0000L).parallel().reduce(0, Long::sum);
        long end = System.currentTimeMillis();
        System.out.println("sum="+sum+",时间: "+(end-start));
    }
}
```



**二十. 异步回调**

使用**CompletableFuture**类完成异步回调功能



异步回调和多线程是两个不同的概念



异步回调一般都是在IO等耗时操作上使用异步回调,异步回调是使用硬件上的DMA功能直接访问内存中的数据.他的好处是函数调用立即返回，相应的工作线程立即返还给系统以供重用。异步回调通常用系统线程池的线程，通常情况下性能好些。（因为可以多次利用，申请时不 需要重 新申请一个线程，只需要从池里取就行了。）异步是一种效果，多线程是一种具体技术。可以说，用“多线程”实现“异步”。

```
/**
 * 异步调用: CompletableFuture
 *  异步执行
 *  成功回调
 *  失败回调
 */


public class FutureDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
       
        // 有返回值(成功或者失败)的 supplyAsync 异步回调
        CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName()+"supplyAsync=>Integer");
            // int i = 10 / 0;
            return 1024;
        });
        
        // 成功和失败的回调函数
        // 返回的是错误信息;
        System.out.println(completableFuture.whenComplete((t, u) -> {
            System.out.println("t=>" + t); // 正确的返回结果
            System.out.println("u=>" + u); // 错误信息  java.util.concurrent.CompletionException: java.lang.ArithmeticException: / by zero
        }).exceptionally((e) -> {
            System.out.println(e.getMessage());  // 可以获取到错误的返回结果
            return 233;
        }).get());
    }
}
public class FutureDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        
        // 没有返回值的 runAsync 异步回调
        CompletableFuture<Void> completableFuture = CompletableFuture.runAsync(()->{
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName()+"runAsync=>Void");
        });

        System.out.println(1111);
        Void unused = completableFuture.get(); // 获取执行结果;若没有这行代码,则不会执行异步回调方法
    }
}
```



**二十一. 理解JMM**

**1、JMM即为JAVA 内存模型（java memory model)**

JMM规定了内存主要划分为主内存和工作内存两种.

主内存对应的是Java堆中的对象实例部分，工作内存对应的是栈中的部分区域。

每条线程拥有各自的工作内存，工作内存中的变量是主内存中的一份拷贝。

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTSurzVWWSYpqyKaegCz7gHQathNibiahPEUbatTBnv2XkQQ1sCBq04fNw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**2、JMM的八种交互操作(每个操作都为原子操作)**

（1）lock(锁定): 作用于主内存的变量，把一个变量标识为线程独占状态

（2）unlock(解锁): 作用于主内存的变量，它把一个处于锁定状态的变量释放出来，

释放后的变量才可以被其他线程锁定

（3）read(读取): 作用于主内存变量，它把一个变量的值从主内存传输到线程的工作内存中，

以便随后的load动作使用

（4）load(载入): 作用于工作内存的变量，它把read操作从主存中变量放入工作内存中

（5）use(使用): 作用于工作内存中的变量，它把工作内存中的变量传输给执行引擎，

每当虚拟机遇到一个需要使用到变量的值，就会使用到这个指令

（6）assign(赋值): 作用于工作内存中的变量，它把一个从执行引擎中接受到的值放入工作内存

的变量副本中

（7）store(存储): 作用于主内存中的变量，它把一个从工作内存中一个变量的值传送到主内存中，

以便后续的write使用

（8）write(写入): 作用于主内存中的变量，它把store操作从工作内存中得到的变量的值

放入主内存的变量中

**3、这八种交互操作的规则**

（1）不允许**read和load、store和write操作之一单独出现。即使用了read必须load，使用了store必须write**

（2）不允许线程丢弃他最近的**assign**操作，即工作变量的数据改变了之后，必须告知主存

（3）不允许一个线程将没有**assign**的数据从工作内存同步回主内存

（4）一个新的变量必须在主内存中诞生，不允许工作内存直接使用一个未被初始化的变量。就是对变量实施**use、store操作之前，必须经过assign和load**操作

（5）一个变量同一时间只有一个线程能对其进行**lock。多次lock后，必须执行相同次数的unlock**才能解锁

（6）如果对一个变量进行**lock操作，会清空所有工作内存中此变量的值，在执行引擎使用这个变量前，必须重新load或assign**操作初始化变量的值

（7）如果一个变量没有被**lock，就不能对其进行unlock操作。也不能unlock**一个被其他线程锁住的变量

（8）对一个变量进行**unlock**操作之前，必须把此变量同步回主内存

**4、JMM内存模型会带来一些问题**

（1）可见性问题

CPU中运行的线程从主存中拷贝共享对象obj到它的CPU缓存,把对象的**obj的count属性改为2.但这个变更对运行在右边的CPU中的线程不可见,因为这个更改还没有flush到主存中: 要解决共享对象的可见性这个问题, 我们可以使用volatile**关键字或者是加锁

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTQaUqmUZjoGTSIXVOZeCMsfWDyXbhJectHFycSsJiccChGkf3DmvpJtg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

（2）竞争现象

线程A和线程B共享一个对象obj.假设线程A从主存读取**Obj.count变量到自己的CPU缓存,同时,线程B也读取了Obj.count变量到它的CPU缓存,并且这两个线程都对Obj.count做了加1操作.此时,Obj.count加1操作被执行了两次,不过都在不同的CPU缓存中.如果这两个加1操作是串行执行的,那么Obj.count变量便会在原始值上加2，最终主存中的Obj.count的值会是3.然而下图中两个加1操作是并行的,不管是线程A还是线程B先flush计算结果到主存，最终主存中的Obj.count只会增加1次变成2，尽管一共有两次加1操作.要解决上面的问题我们可以使用synchronized**代码块.

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTzRibxBZrE3DiahQmeznaY01gmmrPjz9ZH1T4q8HUu2ShmVV9gk8oHr0w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**5、java内存模型中的重排序**



- 在指定程序时,为了提高性能,编译器和处理器常常会对指令做重排序.

**（1）重排序类型**

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTibYBqcdlYs7RhaPBoRxc8vZicNmciaIqC7cjYzx82z4pdGNpPRrbgzBVA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

1) 编译器优化的重排序. 编译器在不改变单线程程序语义的前提下,可以重新安排语句的执行顺序.



2) 指令并行的重排序. 现代处理器采用了指令级并行技术（Instruction-LevelParallelism,ILP）来将多条指令重叠执行.如果不存在 数据依赖性,处理器可以改变语句对应机器指令的执行顺序.



3) 内存系统的重排序.由于处理器使用缓存和读/写缓冲区,这使得加载和存储操作看上去可能是在乱序执行.



**（2）重排序与依赖性**

（2.1）数据依赖性

如果两个操作访问同一个变量,且这两个操作中有一个为写操作,此时这两个操作之间就存在数据依赖性.数据依赖分为下列3种类 型,这3种情况,只要重排序两个操作的执行顺序,程序的执行结果就会被改变.

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTkvG94BBRsx7R2Ik4Z12kRfogFZYlp0j0HZu4cvURDQdOjlZnxjbs9w/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

（2.2）控制依赖性

flag变量是个标记,用来标识变量a是否已被写入,在use方法中变量i依赖if (flag)的判断,这里就叫控制依赖,如果发生了重排序,结果 就不对了.

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTkpPEBTLargufnicTxEaDnRx2jgtkBxw4nEQnTIibGgVOicLicZjwjUiaIQQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

（2.3）as-if-serial

不管如何重排序,都必须保证代码在单线程下的运行正确,连单线程下都无法正确,更不用讨论多线程并发的情况,所以就提出了一个as-if-serial的概念.



as-if-serial语义的意思是:  不管怎么重排序(编译器和处理器为了提高并行度),(单线程)程序的执行结果不能被改变.编译器、runtime和处理器都必须遵守as-if-serial语义。为了遵守as-if-serial语义，编译器和处理器不会对存在数据依赖关系的操作做重排序，因为这种重排序会改变执行结果。（强调一下，这里所说的数据依赖性仅针对单个处理器中执行的指令序列和单个线程中执行的操作，不同处理器之间和不同线程之间的数据依赖性不被编译器和处理器考虑。）但是，如果操作之间不存在数据依赖关系，这些操作依然可能被编译器和处理器重排序。

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTeVEsNz7BJtMln0Z6LVsGJDUoNM6xvCHq9Lq7INQYSJStsNUXqfaIzQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



1和3之间存在数据依赖关系，同时2和3之间也存在数据依赖关系。因此在最终执行的指令序列中，3不能被重排序到1和2的前面（3排到1和2的前面，程序的结果将会被改变）。但1和2之间没有数据依赖关系，编译器和处理器可以重排序1和2之间的执行顺序.



asif-serial语义使单线程下无需担心重排序的干扰，也无需担心内存可见性问题。



**3、并发下重排序带来的问题**

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnT7aBEGbQdS07RqRDhyURrhpDic6JJznAc9yBQ2HnyJpXRUkiacH8WImXw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

 



这里假设有两个线程A和B，A首先执行init ()方法，随后B线程接着执行use ()方法。线程B在执行操作4时，能否看到线程A在操作1对 共享变量a的写入呢？答案是：不一定能看到。

由于操作1和操作2没有数据依赖关系，编译器和处理器可以对这两个操作重排序；同样，操作3和操作4没有数据依赖关系，编译器和处理器也可以对这两个操作重排序。让我们先来看看，当操作1和操作2重排序时，可能会产生什么效果？操作1和操作2做了重排 序。程序执行时，线程A首先写标记变量flag，随后线程B读这个变量。由于条件判断为真，线程B将读取变量a。此时，变量a还没有被  线程A写入，这时就会发生错误！

当操作3和操作4重排序时会产生什么效果？

在程序中，操作3和操作4存在控制依赖关系。当代码中存在控制依赖性时，会影响指令序列执行的并行度。为此，编译器和处理器会采用猜测（Speculation）执行来克服控制相关性对并行度的影响。以处理器的猜测执行为例，执行线程B的处理器可以提前读取并计算a*a，然后把计算结果临时保存到一个名为重排序缓冲（Reorder  Buffer，ROB）的硬件缓存中。当操作3的条件判断为真时，就把该计算结果写入变量i中。猜测执行实质上对操作3和4做了重排序，问题在于这时候，a的值还没被线程A赋值。在单线程程序中，对存在控制依赖的操作重排序，不会改变执行结果（这也是as-if-serial语义允许对存在控制依赖的操作做重排序的原因）；但在多线程程序中，对存在控制依赖的操作重排序，可能会改变程序的执行结果。



**4、解决在并发下的问题**

（4.1）内存屏障-禁止重排序(volatile就是这种方式禁止指令重排的)

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTU7yoV3wGM6M4y6goaSJA1vPoy8d7o8OvMxtJbndRibsEFBia2BMFibZgA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



Java编译器在 生成指令序列的适当位置会插入内存屏障指令来禁止特定类型的处理器重排序，从而让程序按我们预想的流程去执行。

1、保证特定操作的执行顺序。

2、影响某些数据（或则是某条指令的执行结果）的内存可见性。



编译器和CPU能够重排序指令，保证最终相同的结果，尝试优化性能。插入一条**Memory Barrier会告诉编译器和CPU：不管什么指令都不能和这条Memory Barrier指令重排序。

Memory Barrier所做的另外一件事是强制刷出各种CPU cache，如一个Write-Barrier（写入屏障）将刷出所有在Barrier之前写入 cache 的数据，因此，任何CPU上的线程都能读取到这些数据的最新版本。

JMM把内存屏障指令分为4类，解释表格，StoreLoad Barriers**是一个“全能型”的屏障，它同时具有其他3个屏障的效果。现代的多处理器大多支持该屏障（其他类型的屏障不一定被所有处理器支持）。



（4.2）临界区(synchronized)

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTTk7el5GXJkYceiahGKSYhskwtrLFUz5deJaMxOKe2vhXh9Jnj2OyVaQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



临界区内的代码可以重排序（但JMM不允许临界区内的代码“逸出”到临界区之外，那样会破坏监视器的语义）。JMM会在退出临界区和进入临界区这两个关键时间点做一些特别处理，虽然线程A在临界区内做了重排序，但由于监视器互斥执行的特性,这里的线程B根本无法“观察”到线程A在临界区内的重排序。这种重排序既提高了执行效率，又没有改变程序的执行结果。



**5、Happens-Before**

用happens-before的概念来阐述操作之间的内存可见性。在JMM中，如果一个操作执行的结果需要对另一个操作可见，那么这两个操作之间必须要存在happens-before关系 。



两个操作之间具有happens-before关系，并不意味着前一个操作必须要在后一个操作之前执行！happens-before仅仅要求前一个操作（执行的结果）对后一个操作可见，且前一个操作按顺序排在第二个操作之前（the first is visible to and ordered before the second） 。



1）如果一个操作happens-before另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。(对程序员来说)



2）两个操作之间存在happens-before关系，并不意味着Java平台的具体实现必须要按照happens-before关系指定的顺序来执行。如果重排序之后的执行结果，与按happens-before关系来执行的结果一致，那么这种重排序是允许的(对编译器和处理器 来说)



在Java  规范提案中为让大家理解内存可见性的这个概念，提出了happens-before的概念来阐述操作之间的内存可见性。对应Java程序员来说，理解happens-before是理解JMM的关键。JMM这么做的原因是：程序员对于这两个操作是否真的被重排序并不关心，程序员关心的是程序执行时的语义不能被改变（即执行结果不能被改变）。因此，happens-before关系本质上和as-if-serial语义是一回事。as-if-serial语义保证单线程内程序的执行结果不被改变，happens-before关系保证正确同步的多线程程序的执行结果不被改变。

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnThOaNy7j7GMNtmEdrjWfLIamLibFtL7oM38XqaeaFC3j962lD2LUSyXw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



Happens-Before规则-无需任何同步手段就可以保证的



1）程序顺序规则：一个线程中的每个操作，happens-before于该线程中的任意后续操作。

2）监视器锁规则：对一个锁的解锁，happens-before于随后对这个锁的加锁。

3）volatile变量规则：对一个volatile域的写，happens-before于任意后续对这个volatile域的读。

4）传递性：如果A happens-before B，且B happens-before C，那么A happens-before C。

5）start()规则：如果线程A执行操作ThreadB.start()（启动线程B），那么A线程的ThreadB.start()操作happens-before于线程B中的任意操作。

6）join()规则：如果线程A执行操作**ThreadB.join()并成功返回，那么线程B中的任意操作happens-before于线程A从ThreadB.join()**操作成功返回。

7 ）线程中断规则:对线程interrupt方法的调用happens-before于被中断线程的代码检测到中断事件的发生。



**二十二. volatile**

1、内存语义



**volatile**变量自身具有下列特性:



可见性: 对一个**volatile变量的读，总是能看到（任意线程）对这个volatile**变量最后的写入。

原子性: 对任意单个**volatile变量的读/写具有原子性，但类似于volatile++**这种复合操作不具有原子性。

**volatile写的内存语义如下：当写一个volatile**变量时，JMM会把该线程对应的本地内存中的共享变量值刷新到主内存.



![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTqIHhAMvM5S5YIhiaYDnAZpOpyS8WoSiaWHcqQsGN8xfJ5iarE4DzOlNwQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**volatile**读的内存语义如下：当读一个volatile变量时，JMM会把该线程对应的本地内存置为无效。线程接下来将从主内存中读取共享变量。

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTesySHRZAVBCNozusgYZ0pQ8VlxuzNXtclLYaNibOdMDWiboq3blUFgUQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





**volatile**重排序规则：

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTicq7QhrI7XHI4icX0I9piactaicBYIqE4F9eXibGRnCMbTjts1WeEfrx6Tg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**volatile内存语义的实现——JMM对volatile的内存屏障插入策略：

在每个volatile写操作的前面插入一个StoreStore屏障。在每个volatile写操作的后面插入一个StoreLoad屏障。

在每个volatile读操作的后面插入一个LoadLoad屏障。在每个volatile读操作的后面插入一个LoadStore**屏障。

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTBQETrM13ZYOaTMRBDRibMGzC3qF0wWOQLsdSjqfGDhrFJfeQAq6rIng/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)





**volatile**的实现原理



有**volatile变量修饰的共享变量进行写操作的时候会使用CPU提供的Lock**前缀指令:



将当前处理器缓存行的数据写回到系统内存

这个写回内存的操作会使其他CPU里缓存了该内存地址的数据无效.



**2、锁的内存语义**

当线程释放锁时，JMM会把该线程对应的本地内存中的共享变量刷新到主内存中。。

当线程获取锁时，JMM会把该线程对应的本地内存置为无效。从而使得被监视器保护的临界区代码必须从主内存中读取共享变量。

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTSm9HODcQOjGuHXNAicsdQPe9GWjN3Oc3MQ5nfBWRsSGrlf9AqSfnJHQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTBfCgOw2LBQwafSX827qgslpVWunTCUrXjQm0fpttzjib80B1r15HbXA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



（1）synchronized的实现原理

使用**monitorenter和monitorexit**指令实现的：



**monitorenter指令是在编译后插入到同步代码块的开始位置，而monitorexit**是插入到方法结束处和异常处

每个**monitorenter必须有对应的monitorexit**与之配对

任何对象都有一个**monitor(监视器)与之关联，当且一个monitor**被持有后，它将处于锁定状态

锁的存放位置:

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTXniazWJwJcP8jkt2GgaCJTQ59byN0fQaw70ibc7rE35QlBS09MH262Gw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



（2）了解各种锁



锁一共有4种状态,级别从低到高依次是: 无锁状态,偏向锁状态,轻量级锁状态和重量级锁状态.



偏向锁: 大多数情况下,锁不仅存在多线程竞争,而且总是由同一线程多次获得,为了让线程获得锁的代价更低而引入偏向锁.无竞争时不需要进行CAS操作来加锁和解锁.



轻量级锁: 无竞争时通过CAS操作来加锁和解锁.(自旋锁-是一种锁的机制,不是状态)



重量级锁: 真正的加锁操作



![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTeRj0ic0ggbTl6JwvdxyKo4wcq8e7dfHHQxXtkjibubsrdEXRObuSzCbw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



**二十三. CAS**

**1、什么是CAS**

CAS是**compare and swap**的缩写,意思是比较工作内存中的值和主存中的值,如果这个值是期望的,那么执行操作! 如果不是就一直循环!

```
// CAS测试类
public class Test {
    public static void main(String[] args) {
        AtomicInteger atomicInteger = new AtomicInteger(2021);

        // 对于我们平时写的SQL: 乐观锁!

        // 期望,更新
        // public final boolean compareAndSet(int expect, int update)
        // 如果我期望的值达到了,那么就更新,否则就不更新. CAS 是CPU的并发原语!
        // =============捣乱的线程==============
        System.out.println(atomicInteger.compareAndSet(2021, 2022));
        System.out.println(atomicInteger.get());

        System.out.println(atomicInteger.compareAndSet(2022, 2021));
        System.out.println(atomicInteger.get());

        // 期望的线程
        System.out.println(atomicInteger.compareAndSet(2021, 6666));
        System.out.println(atomicInteger.get());
    }
}
```



存在一些缺点:



循环会耗时

一次性只能保证一个共享变量的原子性(因为底层是cpu的操作)

ABA问题

**2、解决ABA问题**



使用**AtomicStampedReference**类引入原子引用(对应的思想就是乐观锁!),带版本号的原子操作!!

```
public class Test {
    static AtomicStampedReference<Integer> atomicStampedReferencence = new AtomicStampedReference<>(1, 1);

    public static void main(String[] args) {

        new Thread(()->{
            int stamp = atomicStampedReferencence.getStamp(); // 获得版本号
            System.out.println("a1=>"+stamp);

            try {
                TimeUnit.SECONDS.sleep(1); // 线程睡眠1秒,保证b线程的执行
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            atomicStampedReferencence.compareAndSet(1,2,
                    atomicStampedReferencence.getStamp(),atomicStampedReferencence.getStamp()+1);

            System.out.println("a2=>"+atomicStampedReferencence.getStamp());

            atomicStampedReferencence.compareAndSet(2,4,
                    atomicStampedReferencence.getStamp(),atomicStampedReferencence.getStamp()+1);

            System.out.println("a3=>"+atomicStampedReferencence.getStamp());
        },"a").start();

        new Thread(()->{
            int stamp = atomicStampedReferencence.getStamp(); // 获得版本号

            System.out.println("b1=>"+stamp);

            try {
                TimeUnit.SECONDS.sleep(2); // 线程沉睡2秒,保证a线程先睡醒继续执行
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            atomicStampedReferencence.compareAndSet(1,6,
                    stamp,stamp+1); // 这行代码未执行,因为版本号与预期不符

            System.out.println("b2---=>"+stamp);
            System.out.println("b2=>"+atomicStampedReferencence.getStamp());
        },"b").start();

        // 等待多线程任务全部执行完毕,再执行后续代码
        while (Thread.activeCount()!=2){

        }
        System.out.println(atomicStampedReferencence.getReference()); // 执行的a线程中的cas方法,最终结果4;并未执行b线程中的cas,否则结果应该为6
    }
}
```



**注意:**



Integer使用了对象缓存机制,默认范围是-128~127,推荐使用静态工厂方法**valueOf获取对象实例,而不是new,因为valueOf使用缓存,而new**一定会创建新的对象分配新的内存空间;



阿里手册第七条

[强制] 所有的相同类型的包装类对象之间值的比较,全部使用equals方法比较.

说明: 对于Integer var = ?在-128至127之间的赋值,Integer对象是在IntegerCache.cache产生,

会复用已有对象,这个区间的Integer值可以直接使用==进行判断,但是这个区间之外的所有数据,

都会在堆上产生,并不会复用已有对象,这是一个大坑,推荐使用equals方法进行判断.



**二十四. 各种锁的理解**

**1、公平锁和非公平锁**

公平锁: 线程之间不能插队,必须先来后到. 这样可能会造成优先级失去作用

非公平锁: 线程之间可以插队,**synchronized就是非公平锁;new ReentrantLock()**也是默认创建一个非公平锁

**2、可重入锁**

也叫做递归锁,拿到了最外面的锁,也就可以拿到里面的锁(自动获得的)

```
public class Demo01 {
    public static void main(String[] args) {
        Phone phone = new Phone();
        new Thread(()->{
            phone.sms();
        },"A").start();

        new Thread(()->{
            phone.sms();
        },"B").start();
    }
}

class Phone{
    public synchronized void sms(){
        System.out.println(Thread.currentThread().getName()+"ssm");
        call();
    }

    public synchronized void call(){
        System.out.println(Thread.currentThread().getName()+"call");
    }
}

结果为:
Assm
Acall
Bssm
Bcall
```



**3、自旋锁**

```
// 自定义的自旋锁
// Thread 默认为null
public class SpinLockDemo {

    AtomicReference<Thread> atomicReference = new AtomicReference<>();

    // 加锁
    public void myLock(){
        Thread thread = Thread.currentThread();
        System.out.println(thread.getName()+"===>myLock");

        // 自旋锁
        while (!atomicReference.compareAndSet(null,thread)) {
        }
    }

    // 解锁
    public void myUnLock(){
        Thread thread = Thread.currentThread();
        System.out.println(thread.getName()+"===>myUnLock");
        atomicReference.compareAndSet(thread,null);
    }
}

class Test{
    public static void main(String[] args) throws InterruptedException {

        // 底层使用的自旋锁 CAS(compareAndSet)
        SpinLockDemo spinLockDemo = new SpinLockDemo();

        new Thread(()->{
            spinLockDemo.myLock();
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }finally {
                spinLockDemo.myUnLock();
            }
        },"T1").start();

        TimeUnit.SECONDS.sleep(1);

        new Thread(()->{
            spinLockDemo.myLock();
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }finally {
                spinLockDemo.myUnLock();
            }
        },"T2").start();
    }
}
```



**4、死锁**

```
// 死锁事例
public class DeadLock {
    public static void main(String[] args) {
        String lockA = "lockA";
        String lockB = "lockB";
        new Thread(new MyThread(lockA,lockB)).start();
        new Thread(new MyThread(lockB,lockA)).start();
    }
}

class MyThread implements Runnable{

    private String lockA;
    private String lockB;

    public MyThread(String lockA,String lockB){
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @SneakyThrows
    @Override
    public void run() {
        synchronized (lockA){
            System.out.println(Thread.currentThread().getName()+"lock:"+lockA+"get=>"+lockB);
            TimeUnit.SECONDS.sleep(2);
            synchronized (lockB){
                System.out.println(Thread.currentThread().getName()+"lock:"+lockB+"get=>"+lockA);
            }
        }
    }
}
```



（1）排查出死锁的方案

idea中的Terminal窗口,敲jps -l定位进程号

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnTG1ZlXkdcrSiaOZ5j1KRrgiccWQhkRNX9E2P8e53M7trUFONp950QpcQw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



（2）jstack [进程号],出来的信息下拉到最后,就可以查出陷入死锁的两个线程,从而进行解决

![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnT1fUxUPkzZASPRAP26jgaBQ7aMuoEUCC5wOJvricibhALZTyWfBAGgDRg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



![图片](https://mmbiz.qpic.cn/mmbiz_png/3kMZppQa5zBsVBtajkXZxTrhPubQiaXnT9wJAl8dGz3KHL7Dnggfr443yakXHB18x93L4IEGg0VCAgCsbGvModw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

————————————————

版权声明：本文为CSDN博主「Y new」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。

原文链接：

https://blog.csdn.net/m0_51028853/article/details/115449609