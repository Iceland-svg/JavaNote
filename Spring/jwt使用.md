```java
public class JwtUtilsTest {  
    @Test  
    void genJwt(){  
        //放置令牌数据  
        //设置签名  
        String secreatString = "ffsfgsbbiodbmsvndrrbniorwsbsbdsbbxsvrvvsscavsvsvsvsvsvssbdosmkmkl";  
        Key key = Keys.hmacShaKeyFor(secreatString.getBytes(StandardCharsets.UTF_8));  
        Map<String,Object> map = new HashMap<>();  
        map.put("id",13);  
        map.put("name","zhangsan");  
        //生成令牌  
        String compact = Jwts.builder()  
                .setClaims(map)  
                .signWith(key, SignatureAlgorithm.HS256)  
                .compact();  
        System.out.println(compact);  
    }  
}
```