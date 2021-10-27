1.开启一个线程，让它sleep一段时间跑一次这也就达到了定频率的执行run方法，我们只需要将业务写在run方法中即可。
```java
import java.util.Date;
public class ThreadTest {
    public static void main(String[] args) {
        // 设置执行周期
        final long timeInterval = 3000;

        Runnable runnable = new Runnable() {
            public void run() {
                while (true) {
                    System.out.println("Task Run ... " + new Date());

                    try {
                        Thread.sleep(timeInterval);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        };
        Thread thread = new Thread(runnable);
        thread.start();
    }
}
```
2.Timer定时器可以理解为有且仅有一个后台线程对多个业务方法进行定时定频率调度的工具。
实现Timer定时任务需要一个Timer工具类和TimerTask实现类，实现类中用于编写任务的逻辑代码。
Timer定时器中包含一个TimerTask的队列和一个TimerThread后台线程。
实例代码
```java
public class MyTask extends TimerTask{
    private String name;
    public MyTask(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    @Override
    public void run() {
        SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String format = sf.format(new Date());
        System.out.println("exec MyTask ... 当前时间为：" + format);
        System.out.println(this.name +" 正在执行！" + sf.format(new Date()));
    }
    public static void main(String[] args) {
        Timer timer = new Timer();
        TimerTask task1 = new MyTask("Tasks 1");
        TimerTask task2 = new MyTask("Tasks 2");

        Calendar calendar1 = Calendar.getInstance();
        calendar1.add(Calendar.SECOND, 3);
        Calendar calendar2 = Calendar.getInstance();
        calendar2.add(Calendar.SECOND, 5);

        SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String format = sf.format(new Date());
        System.out.println("当前时间为：" + format);

        timer.schedule(task1, calendar1.getTime(), 3000L);
        timer.schedule(task2, calendar2.getTime(), 3000L);
    }
}
```
schedule和scheduleAtFixedRate方法的区别？
分为两种情况：
1.首次执行时间早于当前的时间
schedule:以当前时间为准，然后依次按照时间间隔执行任务。
scheduleAtFixedRate:以首次执行的时间为准，过去的时间没有执行任务的次数会在首次执行的时候补上。
2.任务执行所需时间超过任务执行周期时间
schedule:下一次执行的时间以上一次执行完成的时间为准，会存在一直延后执行的情况。
scheduleFixedRate:下一次执行的时间以上一次执行开始的时间为准，不会延后，但是会存在并发的情况。
Timer的缺陷
1.并发操作时的缺陷，这是因为Timer的后台只有一个执行线程导致的。
2.当任务抛出异常时的缺陷。如果TimerTask抛出RuntimeException，Timer会停止所有任务的执行。