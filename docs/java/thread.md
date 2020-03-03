# 线程、同步

## Thread类

### 构造方法：

- public Thread() :分配一个新的线程对象。
- public Thread(String name) :分配一个指定名字的新的线程对象。
- public Thread(Runnable target) :分配一个带有指定目标新的线程对象。
- public Thread(Runnable target,String name) :分配一个带有指定目标新的线程对象并指定名字。

### 常用方法：

- public String getName() :获取当前线程名称。

- public void start() :导致此线程开始执行; Java虚拟机调用此线程的run方法。

- public void run() :此线程要执行的任务在此处定义代码。

- public static void sleep(long millis) :使当前正在执行的线程以指定的毫秒数暂停（暂时停止执行）。

- public static Thread currentThread() :返回对当前正在执行的线程对象的引用。

  ```java
  public static void main(String[] args) {
          //开启多线程
          MyThread mt = new MyThread();
          mt.setName("小强");
          mt.start();
  
          //开启多线程
          new MyThread("旺财").start();
      }
  ```

```java
/*
    设置线程的名称:(了解)
        1.使用Thread类中的方法setName(名字)
            void setName(String name) 改变线程名称，使之与参数 name 相同。
        2.创建一个带参数的构造方法,参数传递线程的名称;调用父类的带参构造方法,把线程名称传递给父类,让父类(Thread)给子线程起一个名字
            Thread(String name) 分配新的 Thread 对象。
 */
public class MyThread extends Thread{

    public MyThread(){}

    public MyThread(String name){
        super(name);//把线程名称传递给父类,让父类(Thread)给子线程起一个名字
    }

    @Override
    public void run() {
        //获取线程的名称
        System.out.println(Thread.currentThread().getName());
    }
}

```

创建多线程程序的第二种方式:实现Runnable接口
    java.lang.Runnable
        Runnable 接口应该由那些打算通过某一线程执行其实例的类来实现。类必须定义一个称为 run 的无参数方法。
    java.lang.Thread类的构造方法
        Thread(Runnable target) 分配新的 Thread 对象。
        Thread(Runnable target, String name) 分配新的 Thread 对象。

### 实现步骤:
​        1.创建一个Runnable接口的实现类
​        2.在实现类中重写Runnable接口的run方法,设置线程任务
​        3.创建一个Runnable接口的实现类对象
​        4.创建Thread类对象,构造方法中传递Runnable接口的实现类对象
​        5.调用Thread类中的start方法,开启新的线程执行run方法
    ### 实现Runnable接口创建多线程程序的好处:
​        1.避免了单继承的局限性
​            一个类只能继承一个类(一个人只能有一个亲爹),类继承了Thread类就不能继承其他的类
​            实现了Runnable接口,还可以继承其他的类,实现其他的接口
​        2.增强了程序的扩展性,降低了程序的耦合性(解耦)
​            实现Runnable接口的方式,把设置线程任务和开启新线程进行了分离(解耦)
​            实现类中,重写了run方法:用来设置线程任务
​            创建Thread类对象,调用start方法:用来开启新线程

```java
//1.创建一个Runnable接口的实现类
public class RunnableImpl implements Runnable{
    //2.在实现类中重写Runnable接口的run方法,设置线程任务
    @Override
    public void run() {
        for (int i = 0; i <20 ; i++) {
            System.out.println(Thread.currentThread().getName()+"-->"+i);
        }
    }
}

```

```java
 public static void main(String[] args) {
        //3.创建一个Runnable接口的实现类对象
        RunnableImpl run = new RunnableImpl();
        //4.创建Thread类对象,构造方法中传递Runnable接口的实现类对象
        //Thread t = new Thread(run);//打印线程名称
        Thread t = new Thread(new RunnableImpl2());//打印HelloWorld
        //5.调用Thread类中的start方法,开启新的线程执行run方法
        t.start();

        for (int i = 0; i <20 ; i++) {
            System.out.println(Thread.currentThread().getName()+"-->"+i);
        }
    }
```

!>扩充：在java中，每次程序运行至少启动2个线程。一个是main线程，一个是垃圾收集线程。因为每当使用
java命令执行一个类的时候，实际上都会启动一个JVM，每一个JVM其实在就是在操作系统中启动了一个进
程。

### 匿名内部类方式实现线程的创建

匿名:没有名字
    内部类:写在其他类内部的类

```java
/**匿名内部类作用:简化代码
    把子类继承父类,重写父类的方法,创建子类对象合一步完成
    把实现类实现类接口,重写接口中的方法,创建实现类对象合成一步完成
匿名内部类的最终产物:子类/实现类对象,而这个类没有名字

格式:
    new 父类/接口(){
        重复父类/接口中的方法
    };
**/
 public static void main(String[] args) {
        //线程的父类是Thread
        // new MyThread().start();
        new Thread(){
            //重写run方法,设置线程任务
            @Override
            public void run() {
                for (int i = 0; i <20 ; i++) {
                    System.out.println(Thread.currentThread().getName()+"-->"+"黑马");
                }
            }
        }.start();

   /**     //线程的接口Runnable
        //Runnable r = new RunnableImpl();//多态
        Runnable r = new Runnable(){
            //重写run方法,设置线程任务
            @Override
            public void run() {
                for (int i = 0; i <20 ; i++) {
                    System.out.println(Thread.currentThread().getName()+"-->"+"程序员");
                }
            }
        };
        new Thread(r).start();
**/
        //简化接口的方式
        new Thread(new Runnable(){
            //重写run方法,设置线程任务
            @Override
            public void run() {
                for (int i = 0; i <20 ; i++) {
                    System.out.println(Thread.currentThread().getName()+"-->"+"传智播客");
                }
            }
        }).start();
    }
```
## 线程安全

如果有多个线程在同时运行，而这些线程可能会同时运行这段代码。程序每次运行结果和单线程运行的结果是一样
的，而且其他的变量的值也和预期的是一样的，就是线程安全的。

!>线程安全问题都是由全局变量及静态变量引起的。若每个线程中对全局变量、静态变量只有读操作，而无写
操作，一般来说，这个全局变量是线程安全的；若有多个线程同时执行写操作，一般都需要考虑线程同步，
否则的话就可能影响线程安全。

### 线程同步

当我们使用多个线程访问同一资源的时候，且多个线程中对资源有写的操作，就容易出现线程安全问题。
要解决上述多线程并发访问一个资源的安全性问题:也就是解决重复票与不存在票问题，Java中提供了同步机制
(synchronized)来解决。

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200302210935.png)

<detail>

<summary>代码明细</summary>

```java
/*
    卖票案例出现了线程安全问题
    卖出了不存在的票和重复的票

    解决线程安全问题的一种方案:使用同步代码块
    格式:
        synchronized(锁对象){
            可能会出现线程安全问题的代码(访问了共享数据的代码)
        }

    注意:
        1.通过代码块中的锁对象,可以使用任意的对象
        2.但是必须保证多个线程使用的锁对象是同一个
        3.锁对象作用:
            把同步代码块锁住,只让一个线程在同步代码块中执行
 */
public class RunnableImpl implements Runnable{
    //定义一个多个线程共享的票源
    private  int ticket = 100;

    //创建一个锁对象
    Object obj = new Object();

    //设置线程任务:卖票
    @Override
    public void run() {
        //使用死循环,让卖票操作重复执行
        while(true){
           //同步代码块
            synchronized (obj){
                //先判断票是否存在
                if(ticket>0){
                    //提高安全问题出现的概率,让程序睡眠
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    //票存在,卖票 ticket--
                    System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                    ticket--;
                }
            }
        }
    }
}

```

</detail>

### 同步方法

<detail>

<summary>代码明细</summary>

```java
/*
    卖票案例出现了线程安全问题
    卖出了不存在的票和重复的票

    解决线程安全问题的二种方案:使用同步方法
    使用步骤:
        1.把访问了共享数据的代码抽取出来,放到一个方法中
        2.在方法上添加synchronized修饰符

    格式:定义方法的格式
    修饰符 synchronized 返回值类型 方法名(参数列表){
        可能会出现线程安全问题的代码(访问了共享数据的代码)
    }
 */
public class RunnableImpl implements Runnable{
    //定义一个多个线程共享的票源
    private static int ticket = 100;


    //设置线程任务:卖票
    @Override
    public void run() {
        System.out.println("this:"+this);//this:com.itheima.demo08.Synchronized.RunnableImpl@58ceff1
        //使用死循环,让卖票操作重复执行
        while(true){
            payTicketStatic();
        }
    }

    /*
        静态的同步方法
        锁对象是谁?
        不能是this
        this是创建对象之后产生的,静态方法优先于对象
        静态方法的锁对象是本类的class属性-->class文件对象(反射)
     */
    public static /*synchronized*/ void payTicketStatic(){
        synchronized (RunnableImpl.class){
            //先判断票是否存在
            if(ticket>0){
                //提高安全问题出现的概率,让程序睡眠
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                //票存在,卖票 ticket--
                System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                ticket--;
            }
        }

    }

    /*
        定义一个同步方法
        同步方法也会把方法内部的代码锁住
        只让一个线程执行
        同步方法的锁对象是谁?
        就是实现类对象 new RunnableImpl()
        也是就是this
     */
    public /*synchronized*/ void payTicket(){
        synchronized (this){
            //先判断票是否存在
            if(ticket>0){
                //提高安全问题出现的概率,让程序睡眠
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                //票存在,卖票 ticket--
                System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                ticket--;
            }
        }

    }
}
```

</detail>

### Lock锁

java.util.concurrent.locks.Lock 机制提供了比synchronized代码块和synchronized方法更广泛的锁定操作,
同步代码块/同步方法具有的功能Lock都有,除此之外更强大,更体现面向对象。
Lock锁也称同步锁，加锁与释放锁方法化了，如下：
public void lock() :加同步锁。
public void unlock() :释放同步锁。

```java
/*
    卖票案例出现了线程安全问题
    卖出了不存在的票和重复的票

    解决线程安全问题的三种方案:使用Lock锁
    java.util.concurrent.locks.Lock接口
    Lock 实现提供了比使用 synchronized 方法和语句可获得的更广泛的锁定操作。
    Lock接口中的方法:
        void lock()获取锁。
        void unlock()  释放锁。
    java.util.concurrent.locks.ReentrantLock implements Lock接口


    使用步骤:
        1.在成员位置创建一个ReentrantLock对象
        2.在可能会出现安全问题的代码前调用Lock接口中的方法lock获取锁
        3.在可能会出现安全问题的代码后调用Lock接口中的方法unlock释放锁
 */
public class RunnableImpl implements Runnable{
    //定义一个多个线程共享的票源
    private  int ticket = 100;

    //1.在成员位置创建一个ReentrantLock对象
    Lock l = new ReentrantLock();

    //设置线程任务:卖票
    @Override
    public void run() {
        //使用死循环,让卖票操作重复执行
        while(true){
            //2.在可能会出现安全问题的代码前调用Lock接口中的方法lock获取锁
            l.lock();

            //先判断票是否存在
            if(ticket>0){
                //提高安全问题出现的概率,让程序睡眠
                try {
                    Thread.sleep(10);
                    //票存在,卖票 ticket--
                    System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                    ticket--;
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    //3.在可能会出现安全问题的代码后调用Lock接口中的方法unlock释放锁
                    l.unlock();//无论程序是否异常,都会把锁释放
                }
            }
        }
    }
}
```

## 线程状态

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200302220337.png)

![](https://cdn.jsdelivr.net/gh/mumozi/Figure_bed/img/20200302215518.png)

```java
/*
    等待唤醒案例:线程之间的通信
        创建一个顾客线程(消费者):告知老板要的包子的种类和数量,调用wait方法,放弃cpu的执行,进入到WAITING状态(无限等待)
        创建一个老板线程(生产者):花了5秒做包子,做好包子之后,调用notify方法,唤醒顾客吃包子

    注意:
        顾客和老板线程必须使用同步代码块包裹起来,保证等待和唤醒只能有一个在执行
        同步使用的锁对象必须保证唯一
        只有锁对象才能调用wait和notify方法

    Obejct类中的方法
    void wait()
          在其他线程调用此对象的 notify() 方法或 notifyAll() 方法前，导致当前线程等待。
    void notify()
          唤醒在此对象监视器上等待的单个线程。
          会继续执行wait方法之后的代码
 */
public class Demo01WaitAndNotify {
    public static void main(String[] args) {
        //创建锁对象,保证唯一
        Object obj = new Object();
        // 创建一个顾客线程(消费者)
        new Thread(){
            @Override
            public void run() {
               //一直等着买包子
               while(true){
                   //保证等待和唤醒的线程只能有一个执行,需要使用同步技术
                   synchronized (obj){
                       System.out.println("告知老板要的包子的种类和数量");
                       //调用wait方法,放弃cpu的执行,进入到WAITING状态(无限等待)
                       try {
                           obj.wait();
                       } catch (InterruptedException e) {
                           e.printStackTrace();
                       }
                       //唤醒之后执行的代码
                       System.out.println("包子已经做好了,开吃!");
                       System.out.println("---------------------------------------");
                   }
               }
            }
        }.start();

        //创建一个老板线程(生产者)
        new Thread(){
            @Override
            public void run() {
                //一直做包子
                while (true){
                    //花了5秒做包子
                    try {
                        Thread.sleep(5000);//花5秒钟做包子
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    //保证等待和唤醒的线程只能有一个执行,需要使用同步技术
                    synchronized (obj){
                        System.out.println("老板5秒钟之后做好包子,告知顾客,可以吃包子了");
                        //做好包子之后,调用notify方法,唤醒顾客吃包子
                        obj.notify();
                        //如果有多个等待线程,随机唤醒一个
                        //obj.notifyAll();//唤醒所有等待的线程
                    }
                }
            }
        }.start();
    }
}

```

