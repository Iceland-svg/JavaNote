添加依赖和配置

```
<dependency>  
    <groupId>com.baomidou</groupId>  
    <artifactId>mybatis-plus-spring-boot4-starter</artifactId>  
    <version>3.5.15</version>  
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

任意创建实体类,继承BaseMapper<>

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


