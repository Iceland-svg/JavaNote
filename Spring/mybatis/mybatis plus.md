添加依赖和配置

```
<!-- Source: https://mvnrepository.com/artifact/com.baomidou/mybatis-plus-boot-starter -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.12</version>
    <scope>compile</scope>
</dependency>
```

```
spring:  
  datasource:  
    url: jdbc:mysql://127.0.0.1:3306/w?characterEncoding=utf8&useSSL=false  
    username: root  
    password: root  
    driver-class-name: com.mysql.cj.jdbc.Driver  
  
mybatis:  
  configuration: # 配置打印 MyBatis⽇志  
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl  
    map-underscore-to-camel-case: true #配置驼峰⾃动转换
```

创建Mapper并添加@MapperScan("com.example.plus.mapper")  扫描和Mapper注解二选一

```java
@MapperScan("com.example.plus.mapper")  
@SpringBootApplication  
public class PlusApplication {  
  
    public static void main(String[] args) {  
        SpringApplication.run(PlusApplication.class, args);  
    }  
  
}
```

任意创建实体类,继承BaseMapper<>（一定别忘了两种方法都要用！！！！！）

```java
public interface UserInfoMapper extends BaseMapper<UserInfo> {}
```

此时生成测试代码

```java
@SpringBootTest  
class UserInfoMapperTest {  
    @Autowired  
    private UserInfoMapper userInfoMapper;  
    @Test  
    public void testSelect(){  
        List<UserInfo> userInfoList = userInfoMapper.selectList(null);  
    }  
}
```

会发现自动就生成了这么多方法，我们当然没有写但是我们可以在BaseMapper里找到这些方法

![](assets/mybatis%20plus/file-20260324215619536.png)

可以写出以下测试代码

```java
@SpringBootTest  
class UserInfoMapperTest {  
    @Autowired  
    private UserInfoMapper userInfoMapper;  
    @Test  
    public void testSelect(){  
        List<UserInfo> userInfoList = userInfoMapper.selectList(null);  
    }  
    @Test  
    void selectById(){  
        UserInfo userInfo = userInfoMapper.selectById(1);  
    }  
    @Test  
    void selectByIds(){  
        List<UserInfo> userInfoList = userInfoMapper.selectByIds(List.of(1,2));  
    }  
    @Test  
    void testInsert(){  
    //必传信息设置一下
        UserInfo userInfo = new UserInfo();  
        userInfo.setAge(20);  
        userInfo.setPassword("123456");  
        userInfo.setUsername("ww");  
        userInfoMapper.insert(userInfo);  
    }
    @Test  
    void testUpdate(){  
    //传更新后的数据
    UserInfo userInfo = new UserInfo();  
    userInfo.setId(6);  
    userInfo.setUsername("lisi");  
    userInfoMapper.updateById(userInfo);  
    }  
    @Test  
	void testDelete(){  
    userInfoMapper.deleteById(5);  
	}
}
```


插入时如何自增id-@TableId(type = IdType.AUTO) 

```java
@Data  
public class UserInfo {  
    @TableId(type = IdType.AUTO)  
    private Integer id;  
    private String username;  
    private String password;  
    private Integer age;  
    private Integer deleteFlag;  
    private Integer gender;  
    private String phone;  
    private Date createTime;  
    private Date updateTime;  
}
```

mybatis进行的一些推断

表名：根据实体类名（不匹配可以用@TableName）
主键：id(@TableId)
字段名: 根据实体类属性名（@TableField）

---

## 条件构造器

### QueryWrapper

```java
@Test  
void testQueryWrapper(){  
    QueryWrapper<UserInfo> queryWrapper = new QueryWrapper<>();  
    queryWrapper.select("id","username","password","age")  
            .eq("age",19)  
                    .like("username","min");  
    List<UserInfo> userInfos =userInfoMapper.selectList(queryWrapper);  
    userInfos.forEach(System.out::println);  
}
@Test  
void testQueryWrapper2(){  
    QueryWrapper<UserInfo> queryWrapper = new QueryWrapper<>();  
    queryWrapper.eq("age" ,18);  
    userInfoMapper.delete(queryWrapper);  
}
@Test  
void testQueryWrapper3(){  
    QueryWrapper<UserInfo> queryWrapper = new QueryWrapper<>();  
    queryWrapper.lt("age",20);  
    UserInfo userInfo = new UserInfo();  
    userInfo.setDeleteFlag(1);  
    userInfoMapper.update(userInfo,queryWrapper);  
}
```

### UpdateWrapper

```java
@Test  
void  testUpdateWrapper(){  
    UpdateWrapper<UserInfo> updateWrapper = new UpdateWrapper<>();  
    updateWrapper.set("age",20).set("delete_flag",0).in("id",List.of(6));  
    userInfoMapper.update(updateWrapper);  
}  
@Test  
void testUpdateWrapper2(){  
    UpdateWrapper<UserInfo> updateWrapper = new UpdateWrapper<>();  
    updateWrapper.setSql("age = age + 10").in("id",List.of(6,7,8));  
    userInfoMapper.update(updateWrapper);  
}
```


LambdaQueryWrapper

```java
@Test  
void testLambdaQueryWrapper(){  
    //LambdaQueryWrapper<UserInfo> lambdaQueryWrapper = new LambdaQueryWrapper<>();  
    QueryWrapper<UserInfo> queryWrapper = new QueryWrapper<>();  
    queryWrapper.lambda()  
            .select(UserInfo::getGender,UserInfo::getId,UserInfo::getPassword)  
            .eq(UserInfo::getAge,18);  
    userInfoMapper.selectList(queryWrapper);  
}
```

LambdaUpdateWrapper

```java
@Test  
void testLambdaUpdateWrapper(){  
    UpdateWrapper<UserInfo> updateWrapper = new UpdateWrapper<>();  
    updateWrapper.lambda()  
            .set(UserInfo::getAge,20)  
            .set(UserInfo::getDeleteFlag,0)  
            .in(UserInfo::getId,List.of(6));  
}
```

自定义sql配合Wrapper

```java
@Mapper  
public interface UserInfoMapper extends BaseMapper<UserInfo> {  
  
    @Select("select age,username,password from user_info ${ew.customSqlSegment} ")  
    List<UserInfo> testSelect(@Param(Constants.WRAPPER) Wrapper<UserInfo> wrapper);  
    List<UserInfo> testSelect1(@Param(Constants.WRAPPER) Wrapper<UserInfo> wrapper);  
}
```



```xml
<?xml version="1.0" encoding="UTF-8"?>  
  
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"  
  
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">  
  
<mapper namespace="com.example.plus.mapper.UserInfoMapper">  
  
    <select id="testSelect1" resultType="com.example.plus.model.UserInfo">  
        select username,age from user_info ${ew.customSqlSegment};  
    </select>  
</mapper>
```



```java
@Test  
void  testCutom(){  
    UpdateWrapper<UserInfo> updateWrapper = new UpdateWrapper<>();  
    updateWrapper  
            .eq("age",20)  
            .eq("delete_flag",0)  
            .in("id",List.of(6));  
    userInfoMapper.testSelect(updateWrapper);  
}  
@Test  
void testCustom1(){  
    UpdateWrapper<UserInfo> updateWrapper = new UpdateWrapper<>();  
    updateWrapper  
            .eq("age",20)  
            .eq("delete_flag",0)  
            .in("id",List.of(6));  
    userInfoMapper.testSelect1(updateWrapper);  
}
```

带参传wrapper

```java
@Update("update user_info set age = age + #{age} ${ew.customSqlSegment}")  
    void updateByCustom(@Param("age") Integer age,@Param(Constants.WRAPPER) Wrapper<UserInfo> wrapper);  
}
```

测试

```java
@Test  
void testUpdate1(){  
    UpdateWrapper<UserInfo> updateWrapper = new UpdateWrapper<>();  
    updateWrapper  
            .eq("age",20)  
            .eq("delete_flag",0)  
            .in("id",List.of(6,7,8));  
    userInfoMapper.updateByCustom(10,updateWrapper);  
}
```