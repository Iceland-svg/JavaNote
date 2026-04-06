生成令牌，设置标签
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

设置时间
实现（服务器）校验
```java
public class JwtUtilsTest {  
    //设置签名  
    String secreatString = "MASxsT336ZApiFCR8G9otSGkVjZ3MGrgI730Sx9z+uY=";  
    Key key = Keys.hmacShaKeyFor(secreatString.getBytes(StandardCharsets.UTF_8));  
    //设置时间  
    long EXPIRATION_TIME = 7*24*60*60*1000;  
    @Test  
    void genJwt(){  
        //放置令牌数据  
        Map<String,Object> map = new HashMap<>();  
        map.put("id",13);  
        map.put("name","zhangsan");  
        //生成令牌  
        String compact = Jwts.builder()  
                .setClaims(map)  
                .signWith(key, SignatureAlgorithm.HS256)  
                .setExpiration(new Date(System.currentTimeMillis()+EXPIRATION_TIME))  
                .compact();  
        System.out.println(compact);  
    }  
    @Test  
    void genKey(){  
        SecretKey secretKey = Keys.secretKeyFor(SignatureAlgorithm.HS256);  
        String encode = Encoders.BASE64.encode(secretKey.getEncoded());  
        System.out.println(encode);  
    }  
  
    /**  
     * 校验  
     */  
    @Test  
    void check(){  
        String token = "eyJhbGciOiJIUzI1NiJ9.eyJuYW1lIjoiemhhbmdzYW4iLCJpZCI6MTMsImV4cCI6MTc3NjA3MDM5NX0.VUFhbvktThSxPUn9OwPuh8KaV9vI8jth9rHuOdeupqQ";  
        JwtParser build = Jwts.parserBuilder().setSigningKey(key).build();  
        Claims body = build.parseClaimsJws(token).getBody();  
        System.out.println(body);  
    }  
}
```