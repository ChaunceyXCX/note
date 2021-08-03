# SpringBoot实现定时任务

## 当前定时任务的实现方式

- Timer: Java自带的java.util.Timer类,这个类允许调度一个java.util.TimerTask任务(按频率执行不是按时间执行)
- Spring Task:Spring3.0以后自带的Task可以将它看成一个轻量级Quartz,使用起来相对简单许多
- Quartz:这是一个功能强大的调度器,可以按固定时间执行,按固定频率执行,等,配置相对复杂

## Spring 内置task实现

1. 启动类实现定时任务
```
@EnableScheduling //使能任务调度
@SpringBootApplication
public class App {
	public static void main(String[] args) {
		SpringApplication.run(App.class, args);
	}
}
```

2. 创建任务类

```
@Slf4j
@Component
public class CronSchedulerJob
{
    private int count = 0;
    
    @Scheduled(cron = "*/5 * * * * ?")
    private void process()
    {
        log.info("CronSchedulerJob runing @{} - {}", TimeUtil.nowDate(), count++);
    }
}
```

3. 设置调度周期

- 通过cron表达式

> cron共有七位,springboot只需要设置前六为即可,因为最后以为是年份极少用到

    1. 第一位,秒: 取值0-59
    2. 第二位,分: 取值0-59
    3. 第三位,小时:取值0-23
    4. 第四位,天/日: 1-31
    5. 第五位,月分: 1-12
    6. 第六位,星期: 1-7
    7. 第七位,年份: 1970-2099,可留空
  
>cron还有一些特殊符号

    1. 星号(*): 可以理解为每,每秒,每分,...
    2. 问号(?): 问号只用于日期和星期,因为星期和日期冲突,所以问号表示不指定(如,1月1号表示周一的话再指定星期为周二就冲突)
    3. 减号(-): 表示范围(如:小时"10-12",表示10点到12点,即10,11,12)
    4. 逗号(,)表达列表值(如:星期"1,3,5",表示周一周三周五)
    5. 斜杠(/): 如x/y,x是开始值,y是步长,(如:秒位:0/10,表示从0开始,每10秒执行一次,即:0,10,20,30,40,50,60),另:*/y,就是0/y

> 范例: @Scheduled(cron = "0 0 7 * * ?")  //每天7:00

- 使用定时器进行规定周期调度
  
    1. @Scheduled(fixedRate = 6000):上一次开始执行时间点之后6秒再执行
    2. @Scheduled(fixedDelay = 6000):上一次执行完毕时间点之后6秒
    3. @Scheduled(initialDelay = 1000,fixedRate = 6000): 第一次延迟一秒后执行,之后按(1.)的周期


## Quartz

### Quartz体系结构

quartz核心概念:

- Job(任务): 为一个接口,只定义一个方法execute(JobExecutionContext context),定义任务要执行的具体方法
- JobDetail(任务信息):表示一个可以执行的调度程序,Job是这个程序所要执行的内容,另外JobDetail还包含了这个任务调度的方案和策略
- Trigger(触发器):描述Job执行的 时间触发规则,主要有SimpleTrigger和CronTrigger两个子类
- Scheduler:是一个调度容器,JobDetail和Trigger可以注册到其中
- JobBuilder: 定义和创建JobDetail实例
- TriggerBuilder: 定义和创建Trigger实例
- JobDataMap: 是Map接口的一个实现可以赋予job一些额外属性
  - 在创建jobdetail时存入key,value:usingJobData("key","value")
  - 在调度job执行里面的内容时可以获取之前放入的属性

集群Quartz应用

- 伸缩性
- 负载均衡
- 高可用
- Quartz可以通过关系数据库做持久化存储支持集群
- Terracotta扩展quartz提供集群功能而不需要数据库支持

### 创建流程(只做springboot的整合)

1. 导入依赖:

```







