## 快速入门
### 1 引入依赖

```XML
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

### 2 创建aspect包 ,写一个TimeRecord

```
@Slf4j  
//标识，告诉Spring这是一个切面类  
@Aspect  
//把类交给Spring管理  
@Component  
public class TimeAspect {  
    //指定方法生效范围，Around指定执行时机是方法前后都有执行  
    @Around()  
    //ProceedingJoinPoint是目标方法  
    public Object timeRecord(ProceedingJoinPoint joinPoint) throws Throwable {  
        //用long来存毫秒级时间戳  
        long startTime = System.currentTimeMillis();  
        //执行目标方法，给返回值  
        Object result = joinPoint.proceed();  
        //记录花费时间  
        long cost = System.currentTimeMillis() - startTime;  
        //打印日志  
        log.info(joinPoint.getSignature() + "耗时: {}",cost);  
        //返回目标方法执行结果  
        return result;  
    }  
}
```

单独写切面的方法

```java
@Slf4j  
@Aspect  
@Component  
public class AspectDemo1 {  
    @Pointcut("execution(* com.example.aop_demo.controller.*.*(..))")  
    private void pt(){};  
    @Around("pt()")  
    public void testArount(ProceedingJoinPoint joinPoint) throws Throwable {  
       log.info("执行Around前");  
       long startTime = System.currentTimeMillis();  
       Object result = joinPoint.proceed();  
       long cost = System.currentTimeMillis() - startTime;  
       log.info(joinPoint.getSignature()+"耗时：{}ms",cost);  
    }  
    @Before("pt()")  
    public void teatBefore(){  
        log.info("执行Before");  
    }  
    @After("pt()")  
    public void testAfter(){  
        log.info("执行After");  
    }  
    @AfterReturning("pt()")  
    public void testAfterReturning(){  
        log.info("执行AfterReturning");  
    }  
    @AfterThrowing("pt()")  
    public void testAfterThrowing(){  
        log.info("执行testAfterThrowing");  
    }  
  
}
```

自定义注解

```java
//定义注解只能加在方法上  
@Target(ElementType.METHOD)  
//定义自定义注解的生命周期是运行时  
@Retention(RetentionPolicy.RUNTIME)  
public @interface TimeRecord {  
  
}
```

给测试代码添加自定义注解

```java
@RestController  
@RequestMapping("/test")  
public class ControllerTest {  
    @TimeRecord  
    @RequestMapping("/t1")  
    public String test1(){  
        return  "t1";  
    }  
    @RequestMapping("/t2")  
    public Boolean test2(){  
        return true;  
    }  
}
```

利用AOP为这个注解实现记录方法执行时间的特定功能  -   *也可以添加现有的注解来达到让添加了这个注解的方法记录执行的时间的功能*

```java
@Slf4j  
@Component  
@Aspect  
public class TimeRecordAspect {  
    //此处注解的意思是对于所有加了TimeRecord注解的方法生效  
    @Around("@annotation(com.example.aop_demo.aspect.TimeRecord)")  
    public void testArount(ProceedingJoinPoint joinPoint) throws Throwable {  
        log.info("执行Around前");  
        long startTime = System.currentTimeMillis();  
        Object result = joinPoint.proceed();  
        long cost = System.currentTimeMillis() - startTime;  
        log.info(joinPoint.getSignature()+"耗时：{}ms",cost);  
    }  
}
```
		
### AOP的一些概念

（1）切点

（2）连接点

具体的方法

（3）通知类型

类似@Around这种，通知执行时机的注解，就是通知的一种类型，类似还有
@Before
前置通知，通知方法在目标方法前执行
@After
后置通知，通知方法在目标方法后执行
@AfterReturning
返回后通知，通知方法在目标方法后执行，无论是否有异常都会执行
@AfterThrowing
异常后通知，通知方法发生异常后执行

（4）通知事情

@Around注解下的方法

（5）切面

@Around注解和其注解方法就是切面
