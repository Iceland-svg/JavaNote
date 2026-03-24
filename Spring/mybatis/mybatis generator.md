1 添加依赖

```
<plugin>  
    <groupId>org.mybatis.generator</groupId>  
    <artifactId>mybatis-generator-maven-plugin</artifactId>  
    <version>1.3.6</version>  
    <executions>
            <execution>
            <id>Generate MyBatis Artifacts</id>  
            <phase>deploy</phase>  
            <goals>
                <goal>generate</goal>  
            </goals>
            </execution>
    </executions>
    <configuration> 
        <!--generator配置文件所在位置-->  
        <configurationFile>src/main/resources/generator/generatorConfig.xml</configurationFile>  
        <!-- 允许覆盖生成的文件, mapxml不会覆盖, 采用追加的方式-->  
        <overwrite>true</overwrite>  
        <verbose>true</verbose>  
        <!--将当前pom的依赖项添加到生成器的类路径中-->  
        <includeCompileDependencies>true</includeCompileDependencies>  
    </configuration>
    <dependencies>
    <!-- Source: https://mvnrepository.com/artifact/mysql/mysql-connector-java -->  
        <dependency>  
            <groupId>mysql</groupId>  
            <artifactId>mysql-connector-java</artifactId>  
            <version>8.0.33</version>  
            <scope>compile</scope>  
        </dependency>    
    </dependencies>
</plugin>
```

2 写generatorConfig.xml文件

修改配置

```
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE generatorConfiguration  
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"  
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">  
<!-- 配置生成器 -->  
<generatorConfiguration>  
    <!-- 一个数据库一个context -->  
    <context id="MysqlTables" targetRuntime="MyBatis3">  
        <!--禁用自动生成的注释-->  
        <commentGenerator>  
            <property name="suppressDate" value="true"/>  
            <property name="suppressAllComments" value="true" />  
        </commentGenerator>        <!--数据库连接信息-->  
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"  
                        connectionURL="jdbc:mysql://127.0.0.1:3306/book_test?serverTimezone=Asia/Shanghai&amp;nullCatalogMeansCurrent=true"  
                        userId="root"  
                        password="123456">  
        </jdbcConnection>        <!-- 生成实体类, 配置路径 -->  
        <javaModelGenerator targetPackage="com.example.generator.model" targetProject="src/main/java" >  
            <property name="enableSubPackages" value="false"/>  
            <property name="trimStrings" value="true"/>  
        </javaModelGenerator>        <!-- 生成mapxml文件 -->  
        <sqlMapGenerator targetPackage="generatorMapper" targetProject="src/main/resources" >  
            <property name="enableSubPackages" value="false" />  
        </sqlMapGenerator>        <!-- 生成mapxml对应client，也就是接口dao -->  
        <javaClientGenerator targetPackage="com.example.generator.mapper" targetProject="src/main/java" type="XMLMAPPER" >  
            <property name="enableSubPackages" value="false" />  
        </javaClientGenerator>        <!-- table可以有多个,tableName表示要匹配的数据库表 -->  
        <table tableName="user_info" domainObjectName="UserInfo" enableSelectByExample="true"  
               enableDeleteByExample="true" enableDeleteByPrimaryKey="true" enableCountByExample="true"  
               enableUpdateByExample="true">  
            <!--   类的属性是否用数据库中的真实字段名做为属性名, 不指定这个属性会自动转换 _ 为驼峰命名规则         -->  
            <property name="useActualColumnNames" value="false" />  
            <!-- 数据库表主键 -->  
            <generatedKey column="id" sqlStatement="Mysql" identity="true" />  
        </table>  
        <table tableName="book_info" domainObjectName="BookInfo" enableSelectByExample="true"  
               enableDeleteByExample="true" enableDeleteByPrimaryKey="true" enableCountByExample="true"  
               enableUpdateByExample="true">  
            <!--   类的属性是否用数据库中的真实字段名做为属性名, 不指定这个属性会自动转换 _ 为驼峰命名规则         -->  
            <property name="useActualColumnNames" value="false" />  
            <!-- 数据库表主键 -->  
            <generatedKey column="id" sqlStatement="Mysql" identity="true" />  
        </table>  
    </context></generatorConfiguration>
```

在com.xxx.yyy的与yyy同一层的路径创建generator包

、![](assets/mybatis%20generator/file-20260324205735051.png)![438](assets/mybatis%20generator/file-20260324205735051.png)

利用maven生成