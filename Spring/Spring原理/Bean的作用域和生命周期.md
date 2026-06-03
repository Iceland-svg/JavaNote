

Bean默认是单例作用域，不管是从Apliaction获取（getBean方法）还是直接注入都是得到的同一个Bean(使用 == 可知地址也一致)

多例作用域：每次使用Bean都会创建新的实例
Request作用域：每个不同的http请求会创建不同的实例（不同url,不同请求）
session作用域：每个http session的生命周期会创建新的实例
application作用域：每个servletContext生命周期内创建新的实例
websoket：每个websoket生命周期内创建新的实例、


（1）实例化
（2）属性赋值
（3）初始化
执行各种通知
执行初始化方法
（4）使用
（5）销毁


---

# Spring Bean 生命周期

（1）实例化
（2）属性赋值
（3）初始化
执行各种通知
执行初始化方法
（4）使用
（5）销毁

整个过程可以分为几个阶段：

1. 实例化（Instantiation）

· Spring 通过反射调用 Bean 的构造方法或工厂方法，创建一个空的 Bean 对象。
· 此时属性还没有注入，只是个“壳”。

2. 属性填充（Populate Properties）

· 根据 XML、注解或配置，将依赖的 Bean 注入进来（@Autowired、@Value 等）。
· 这个阶段会处理各种 Aware 接口回调，让 Bean 拿到容器相关资源：
  · BeanNameAware → 拿到自己在容器里的名字
  · BeanFactoryAware → 拿到所在的 BeanFactory
  · ApplicationContextAware → 拿到 ApplicationContext

3. 初始化前（PostProcessBeforeInitialization）

· 调用所有 BeanPostProcessor 的 postProcessBeforeInitialization() 方法。
· 这是一个扩展点，可以在初始化前对 Bean 做代理增强或修改。

4. 初始化（Initialization）

· 先执行 @PostConstruct 标注的方法（依赖 CommonAnnotationBeanPostProcessor）。
· 再执行 InitializingBean 接口的 afterPropertiesSet() 方法。
· 最后执行 XML 或 @Bean 中配置的 init-method / initMethod。

5. 初始化后（PostProcessAfterInitialization）

· 调用所有 BeanPostProcessor 的 postProcessAfterInitialization() 方法。
· 这里是 AOP 生成代理对象的关键时机，如果有切面匹配，会返回代理对象代替原始 Bean。

6. 就绪（Ready）

· Bean 完全就绪，可以对外提供服务，放入单例池（singletonObjects）。

7. 销毁（Destruction）

· 容器关闭时，先执行 @PreDestroy 标注的方法。
· 再执行 DisposableBean 接口的 destroy() 方法。
· 最后执行配置的 destroy-method / destroyMethod。

---

一句话总结流程

实例化 → 属性注入 + Aware → BeanPostProcessor 前置处理 → @PostConstruct → InitializingBean → init-method → BeanPostProcessor 后置处理（AOP 代理） → 就绪 → @PreDestroy → DisposableBean → destroy-method

---

常见面试追问：Spring AOP 是在哪个环节介入的？

就是在 初始化后 这个环节，通过 BeanPostProcessor 的后置处理，用动态代理（JDK 或 CGLib）生成代理对象并返回。

