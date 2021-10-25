## Redis实现库存秒杀

分布式锁使用场景：多个服务键+保证同一时刻内+同一用户只能有一个请求（防止关键业务出现数据冲突和并发错误）

POM文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.5</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>boot_redis01</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>boot_redis01</name>
    <description>boot_redis01</description>
    <properties>
        <java.version>11</java.version>
    </properties>
    <dependencies>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--springboot Redis整合依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <!--jedis-->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
        </dependency>
        <!--pool2-->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
        </dependency>
        <!--Springboot-aop-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
        <!--redisson-->
        <dependency>
            <groupId>org.redisson</groupId>
            <artifactId>redisson</artifactId>
            <version>3.12.5</version>
        </dependency>
        <!--通用基础配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <!--热部署-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

YML

```yml
server.port=1111
spring.redis.database=0
spring.redis.host=127.0.0.1
spring.redis.port=6380
spring.redis.password=123456
spring.redis.lettuce.pool.max-active=8
spring.redis.lettuce.pool.max-wait=-1
spring.redis.lettuce.pool.max-idle=8
spring.redis.lettuce.pool.min-idle=0
```

Config

```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<String, Serializable> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Serializable> redisTemplate = new RedisTemplate<>();
        // 注入连接工厂
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        // key系列化
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        // value系列化
        redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        //hash类型，key序列化
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        // hash 类型 value序列化
        redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        return redisTemplate;
    }
}
```



Controller：

```java
@RestController
public class GoodsController {
    @Autowired
    private StringRedisTemplate redisTemplate;
    @Value("${server.port}")
    private String serverPort;//读取配置文件端口，相当写死了
    @GetMapping("/buy_goods")
    public String buy_Goods() {
        //查看库存的数量相当于redis的get key
        String result = redisTemplate.opsForValue().get("goods:001");
        int goodsNumber = result == null ? 0 : Integer.parseInt(result);
        if (goodsNumber > 0) {
            int realNumber = goodsNumber - 1;
            redisTemplate.opsForValue().set("goods:001",String.valueOf(realNumber));
            System.out.println("成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort);
            return "成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort;
        } else {
            System.out.println("商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort);
        }
        return "商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort;
    }
}

```

Version1 

```java
@RestController
public class GoodsController {
    @Autowired
    private StringRedisTemplate redisTemplate;
    @Value("${server.port}")
    private String serverPort;//读取配置文件端口，相当写死了
    @GetMapping("/buy_goods")
    public String buy_Goods() {
        synchronized (this) {
            //查看库存的数量相当于redis的get key
            String result = redisTemplate.opsForValue().get("goods:001");
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001",String.valueOf(realNumber));
                System.out.println("成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort);
                return "成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort;
            } else {
                System.out.println("商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort);
            }
            return "商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort;
        }
    }
}

```

Version2.0

使用nginx，这样访问时先访问nginx——>（1111和2222）

```java
upstream mynginx{
      server 192.168.5.7:1111 weight=1
      server 192.168.5.7:2222 weight=1
   }
location / {
            #root   html;
            proxy_pass http://mynginx;
            index  index.html index.htm;
        }
```

Version3.0

使用Redis分布式锁setnx

```java
@RestController
public class GoodsController {
    //定义锁
     public static final String REDIS_LOCK = "r_lock";
    @Autowired
    private StringRedisTemplate redisTemplate;
    @Value("${server.port}")
    private String serverPort;//读取配置文件端口，相当写死了
    @GetMapping("/buy_goods")
    public String buy_Goods() {
        String value = UUID.randomUUID().toString()+Thread.currentThread().getName();
        try {
            //加锁
            Boolean flag = redisTemplate.opsForValue().setIfAbsent(REDIS_LOCK, value);
            if (!flag) {
                return "失败";
            }
            //查看库存的数量相当于redis的get key
            String result = redisTemplate.opsForValue().get("goods:001");
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001",String.valueOf(realNumber));
                System.out.println("成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort);
                return "成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort;
            } else {
                System.out.println("商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort);
            }
            return "商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort;
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //释放锁
            redisTemplate.delete(REDIS_LOCK);
        }
        
    }
}
```

version 4.0

上面还是有问题的，如果没有执行到finally就宕机了，那肯定释放不了锁的，那么怎么办？给key设置过期时间

```java
@RestController
public class GoodsController {
    //定义锁
     public static final String REDIS_LOCK = "r_lock";
    @Autowired
    private StringRedisTemplate redisTemplate;
    @Value("${server.port}")
    private String serverPort;//读取配置文件端口，相当写死了
    @GetMapping("/buy_goods")
    public String buy_Goods() {
        String value = UUID.randomUUID().toString()+Thread.currentThread().getName();
        try {
            //加锁
            Boolean flag = redisTemplate.opsForValue().setIfAbsent(REDIS_LOCK, value);
            //这是过期时间
            redisTemplate.expire(REDIS_LOCK,10L, TimeUnit.SECONDS);
            if (!flag) {
                return "失败";
            }
            //查看库存的数量相当于redis的get key
            String result = redisTemplate.opsForValue().get("goods:001");
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001",String.valueOf(realNumber));
                System.out.println("成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort);
                return "成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort;
            } else {
                System.out.println("商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort);
            }
            return "商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort;
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //释放锁
            redisTemplate.delete(REDIS_LOCK);
        }

    }
}
```

Version 5.0

上面还是有问题，我们没有考虑原子性问题

```java
//加锁并且设置过期时间(必须同时一行，才能保证原子性)
Boolean flag = redisTemplate.opsForValue().setIfAbsent(REDIS_LOCK,value,10L,TimeUnit.SECONDS);
```

Version 6.0

上面版本也有问题，会隐患误删别人的锁，所以必须判断属于自己的锁才能删除

```java
finally {
 if (redisTemplate.opsForValue().get(REDIS_LOCK).equalsIgnoreCase(value)) {
 //释放锁
 redisTemplate.delete(REDIS_LOCK);
            }
```



Version 7.0 

删除问题是：finally块判断+del删除操作不是原子性，如何解决？使用lua脚本，如果不用脚本如何解决？还可以使用redis自身的事务。建议使用Lua脚本，一下使用事务解决

```java
@RestController
public class GoodsController {
    //定义锁
     public static final String REDIS_LOCK = "r_lock";
    @Autowired
    private StringRedisTemplate redisTemplate;
    @Value("${server.port}")
    private String serverPort;//读取配置文件端口，相当写死了
    @GetMapping("/buy_goods")
    public String buy_Goods() {
        String value = UUID.randomUUID().toString()+Thread.currentThread().getName();
        try {
            //加锁并且设置过期时间(必须同时一行，才能保证原子性)
            Boolean flag = redisTemplate.opsForValue().setIfAbsent(REDIS_LOCK, value,10L,TimeUnit.SECONDS);
            if (!flag) {
                return "失败";
            }
            //查看库存的数量相当于redis的get key
            String result = redisTemplate.opsForValue().get("goods:001");
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0) {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001",String.valueOf(realNumber));
                System.out.println("成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort);
                return "成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort;
            } else {
                System.out.println("商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort);
            }
            return "商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort;
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            while (true) {
                redisTemplate.watch(REDIS_LOCK);
                if (redisTemplate.opsForValue().get(REDIS_LOCK).equalsIgnoreCase(value)) {
                    redisTemplate.setEnableTransactionSupport(true);
                    redisTemplate.multi();
                    redisTemplate.delete(REDIS_LOCK);
                    List<Object> list = redisTemplate.exec();
                    if (list == null) {
                        continue;
                    }
                }
                redisTemplate.unwatch();
                break;
            }
        }

    }
}
```

version 8.0

问题：

* 确保RedisLock过期时间大于业务执行时间的问题，因为很多时间网络出现延迟等等情况

* 以上都是单机的，在高并发是有问题的，需要解决集群问题

面试题：Redis分布式锁如何实现续期？



在集群环境下，我们直接使用RedLock的Redisson的来实现

config文件

```java
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate<String, Serializable> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
        RedisTemplate<String, Serializable> redisTemplate = new RedisTemplate<>();
        // 注入连接工厂
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        // key系列化
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        // value系列化
        redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        //hash类型，key序列化
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        // hash 类型 value序列化
        redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        return redisTemplate;
    }
    @Bean
    public Redisson redisson() {
        Config config = new Config();
        config.useSingleServer().setAddress("redis://127.0.0.1:6380").setDatabase(0);
        return (Redisson) Redisson.create(config);
    }
}

```

controller

```java
@RestController
public class GoodsController {
    //定义锁
    public static final String REDIS_LOCK = "r_lock";
    @Autowired
    private StringRedisTemplate redisTemplate;
    @Value("${server.port}")
    private String serverPort;//读取配置文件端口，相当写死了
    private Redisson redisson;

    @GetMapping("/buy_goods")
    public String buy_Goods() throws Exception{
        String value = UUID.randomUUID().toString()+Thread.currentThread().getName();
        RLock redissonLock = redisson.getLock(REDIS_LOCK);
        redissonLock.lock();
        try {
            //查看库存的数量相当于redis的get key
            String result = redisTemplate.opsForValue().get("goods:001");
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0)
            {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001",String.valueOf(realNumber));
                System.out.println("成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort);
                return "成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort;
            } else {
                System.out.println("商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort);
            }
            return "商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort;
        } finally {
            redissonLock.unlock();
        }
    }
}
```

再次修改

```java
@RestController
public class GoodsController {
    //定义锁
    public static final String REDIS_LOCK = "r_lock";
    @Autowired
    private StringRedisTemplate redisTemplate;
    @Value("${server.port}")
    private String serverPort;//读取配置文件端口，相当写死了
    private Redisson redisson;

    @GetMapping("/buy_goods")
    public String buy_Goods() throws Exception{
        String value = UUID.randomUUID().toString()+Thread.currentThread().getName();
        RLock redissonLock = redisson.getLock(REDIS_LOCK);
        redissonLock.lock();
        try {
            //查看库存的数量相当于redis的get key
            String result = redisTemplate.opsForValue().get("goods:001");
            int goodsNumber = result == null ? 0 : Integer.parseInt(result);
            if (goodsNumber > 0)
            {
                int realNumber = goodsNumber - 1;
                redisTemplate.opsForValue().set("goods:001",String.valueOf(realNumber));
                System.out.println("成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort);
                return "成功买到商品：" + realNumber + "件" + "\t 提供服务端口" + serverPort;
            } else {
                System.out.println("商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort);
            }
            return "商品已售完，欢迎下次光临" + "\t 提供服务端口" + serverPort;
        } finally {
            if (redissonLock.isLocked()) {
                if (redissonLock.isHeldByCurrentThread()) {
                    redissonLock.unlock();
                }
            }
        }
    }
}
```

总结：

Redis 分布式锁，增加过期时间，必须setnx+过期时间必须同一行，这样才能实现原子操作
