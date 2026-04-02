Bean默认是单例作用域，不管是从Apliaction获取（getBean方法）还是直接注入都是得到的同一个Bean(使用 == 可知地址也一致)

多例作用域：每次使用Bean都会创建新的实例
Request作用域：每个不同的http请求会创建不同的实例（controller）