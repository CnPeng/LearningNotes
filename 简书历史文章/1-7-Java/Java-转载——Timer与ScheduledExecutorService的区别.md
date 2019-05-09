本文转载自 http://blog.csdn.net/nalw2012/article/details/49633413

java.util.Timer计时器有管理任务延迟执行("如1000ms后执行任务")以及周期性执行("如每500ms执行一次该任务")。但是，Timer存在一些缺陷，因此你应该考虑使用ScheduledThreadPoolExecutor作为代替品.

>* Timer对调度的支持是基于绝对时间,而不是相对时间的，由此任务对系统时钟的改变是敏感的;ScheduledThreadExecutor只支持相对时间。
>
>* Timer的另一个问题在于，如果TimerTask抛出未检查的异常，Timer将会产生无法预料的行为。Timer线程并不捕获异常，所以TimerTask抛出的未检查的异常会终止timer线程。这种情况下，Timer也不会再重新恢复线程的执行了;它错误的认为整个Timer都被取消了。此时，已经被安排但尚未执行的TimerTask永远不会再执行了，新的任务也不能被调度了。

个人总结：timer的bug：60秒执行一次的话，如果用户修改了时间的话 那么时针都会归0，本来是临近10秒执行的时候timer又会重新计时一次 再等60才执行。
 
测试Timer的例子
```
import java.util.Timer;    
import java.util.TimerTask;    
    
public class TimerTest {    
    private Timer timer = new Timer();    
    //启动计时器    
    public void lanuchTimer(){    
        timer.schedule(new TimerTask(){    
            public void run() {    
                throw new RuntimeException();    
            }    
        }, 1000*3, 500);    
    }    
    //向计时器添加一个任务    
    public void addOneTask(){    
        timer.schedule(new TimerTask(){    
            public void run(){    
                System.out.println("hello world");    
            }    
        }, 1000*1,1000*5);    
    }    
        
    public static void main(String[] args) throws Exception {    
        TimerTest test = new TimerTest();    
        test.lanuchTimer();    
        Thread.sleep(1000*5);//5秒钟之后添加一个新任务    
        test.addOneTask();    
    }    
}  
```

运行该程序，Timer会抛出一个RumtimeException和java.lang.IllegalStateException:Timer already cancelled。常言道，真是祸不单行，Timer还将它的问题传染给下一个倒霉的调用者，这个调用者原本试图提交一个TimerTask的，你可能希望程序会一直运行下去，然而实际情况如程序所示5秒钟后就中止了，还伴随着一个异常，异常的消息是"Timer already cancelled"。

ScheduledThreadPoolExector妥善地处理了这个异常的任务，所以说在java5.0或更高的JDK中，几乎没有理由再使用Timer了。
 
用ScheduledThreadPoolExector改进后的例子

```
    
import java.util.concurrent.Executors;    
import java.util.concurrent.ScheduledExecutorService;    
import java.util.concurrent.TimeUnit;    
    
public class ScheduledExecutorTest {    
    //线程池能按时间计划来执行任务，允许用户设定计划执行任务的时间，int类型的参数是设定    
    //线程池中线程的最小数目。当任务较多时，线程池可能会自动创建更多的工作线程来执行任务    
    //此处用Executors.newSingleThreadScheduledExecutor()更佳。  
    public ScheduledExecutorService scheduExec = Executors.newScheduledThreadPool(1);    
    //启动计时器    
    public void lanuchTimer(){    
        Runnable task = new Runnable() {    
            public void run() {    
                throw new RuntimeException();    
            }    
        };    
        scheduExec.scheduleWithFixedDelay(task, 1000*5, 1000*10, TimeUnit.MILLISECONDS);    
    }    
    //添加新任务    
    public void addOneTask(){    
        Runnable task = new Runnable() {    
            public void run() {    
                System.out.println("welcome to china");    
            }    
        };    
        scheduExec.scheduleWithFixedDelay(task, 1000*1, 1000, TimeUnit.MILLISECONDS);    
    }    
        
    public static void main(String[] args) throws Exception {    
        ScheduledExecutorTest test = new ScheduledExecutorTest();    
        test.lanuchTimer();    
        Thread.sleep(1000*5);//5秒钟之后添加新任务    
        test.addOneTask();    
    }    
```
