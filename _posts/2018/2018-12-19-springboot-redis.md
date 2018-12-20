---
layout: post
title: springboot2.0整合redis
category: springboot
tags: [springboot]
---

### 需要引入redis依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### 设置连接redis的配置

Redis配置
```
spring.redis.port=6379
spring.redis.host=your host
spring.redis.password=your password
```

### 配置redis连接
```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<Object, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(redisConnectionFactory);

        // 使用Jackson2JsonRedisSerialize 替换默认的jdkSerializeable序列化
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);

        ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        objectMapper.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);

        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);

        // 设置value的序列化规则和 key的序列化规则
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
        redisTemplate.afterPropertiesSet();
        return redisTemplate;
    }
}
```


###开始使用

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootRedisApplicationTests {

	@Autowired
	private StringRedisTemplate stringRedisTemplate;
	@Test
	public void contextLoads() {

		stringRedisTemplate.opsForValue().set("name","hansn");
		String s = stringRedisTemplate.opsForValue().get("name");
		System.out.println(s);

		//设置过期时间
		stringRedisTemplate.opsForValue().set("name1","hansn007",10,TimeUnit.MINUTES);
		String s1 = stringRedisTemplate.opsForValue().get("name1");
		System.out.println(s1);
	}

}
```