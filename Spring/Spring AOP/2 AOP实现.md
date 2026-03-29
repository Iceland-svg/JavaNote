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
