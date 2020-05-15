# SETNX 和 SETEX

## 图解

![](http://q8sats5bw.bkt.clouddn.com/img/image-20200409090924771.png)

## 代码实现

```java
package com.topdraw.utils;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import javax.servlet.http.HttpServletRequest;
import java.util.concurrent.TimeUnit;

/**
 * @author wangliang
 * @date 2020-04-09-9:15
 */
@Slf4j
public class RedisSchedule {

    @Autowired
    private StringRedisTemplate redisTemplate;

    private static final String publicKey = "CommonKey";

    Boolean lock = false;

    HttpServletRequest request = RequestHolder.getHttpServletRequest();

    @Scheduled(cron="*/5 * * * * *")
    public void setNx() {
        System.out.println("TimeUnit.SECONDS" + TimeUnit.SECONDS);
        try {
            HttpServletRequest request = RequestHolder.getHttpServletRequest();
            String ipConfig = StringUtils.getIp(request) + ":" + request.getLocalPort();
            lock = redisTemplate.opsForValue().setIfAbsent(publicKey,ipConfig);
            if(!lock) {
                String value = redisTemplate.opsForValue().get(publicKey);
                log.warn("key have exist belong to :" + value);
            } else {
                redisTemplate.opsForValue().set(publicKey,ipConfig,60,TimeUnit.SECONDS);
                log.info("start lock lockNxExJob success");
                Thread.sleep(500);
            }
        } catch (InterruptedException e) {
            log.error("sexNx error");
            e.printStackTrace();
        } finally {
            // 释放锁
            redisTemplate.delete(publicKey);
        }

    }
}
```

# Lua实现的分布式锁

分布式锁setnx,setex的缺陷，在setnx 和 setex 中间发生了服务的宕机，那么key将没有超时时间，会一直存在，新的请求永远进不来

  *  从 Redis 2.6.0 版本开始，通过内置的 Lua 解释器，可以使用 EVAL 命令对 Lua 脚本进行求值。
        　　* Redis 使用单个 Lua 解释器去运行所有脚本，并且， Redis 也保证脚本会以原子性(atomic)的方式执行：当某个脚本正在运行的时候，不会有其他脚本或 Redis 命令被执行。这和使用 MULTI / EXEC 包围的事务很类似。在其他别的客户端看来，脚本的效果(effect)要么是不可见的(not visible)，要么就是已完成的(already completed)。

## 图解

![](http://q8sats5bw.bkt.clouddn.com/img/image-20200409095917684.png)

由于setnx 和 setex 是分步进行的，那我们将两步和成一步，放在同一个原子中即可

## 代码实现

Lau 脚本

1**.设置锁时，使用set命令，其中包括了setnx 和 expire的功能，起到了原子操作的结果**

```java
SET key_name my_random_value NX PX 30000 
// NX 表示if not exist 就设置并且返回为true,否则不设置并且返回为false,px表示过期时间 毫秒级别 3000 代表过期时间
```

2.**`在获取锁完成相关业务之后，需要删除自己设置的锁`**

删除原因：保证服务资源的高效利用，不用等到锁自动过期才去删除

删除方法最好使用lua脚本:最好使用Lua脚本删除（redis保证执行此脚本时不执行其他操作，保证操作的原子性），代码如下；逻辑是 先获取key，如果存在并且值是自己设置的就删除此key;否则就跳过；

```lua
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

## Lua脚本结合redisTemplate 实战演练

```lua
local lockKey = KEYS[1]
local lockTime = KEYS[2]
local lockValue = KEYS[3]

-- setnx info
local result_1 = redis.call('SETNX', lockKey, lockValue)
if result_1 == 1
then
local result_2= redis.call('SETEX', lockKey,lockTime, lockValue)
return result_2
else
return 'faild'
end
```

```java
package com.topdraw.utils;

import lombok.extern.slf4j.Slf4j;
import org.springframework.core.io.ClassPathResource;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.script.DefaultRedisScript;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.scripting.support.ResourceScriptSource;

import javax.servlet.http.HttpServletRequest;
import java.util.ArrayList;
import java.util.List;
/**
 * Created by zhangjiawen on 2019/8/13.
 */
@Slf4j
public class LuaRedisSchedulePlus {
    private final  RedisTemplate redisTemplate;

    public LuaRedisSchedulePlus(RedisTemplate redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    private static final String publicKey="DEMO_COMMON_KEY";
    Boolean lock=false;
    private DefaultRedisScript<String> lockScript;

    @Scheduled(cron="*/5 * * * * *")
    public void setNx() {
        HttpServletRequest request = RequestHolder.getHttpServletRequest();
        String ipConfig = null;
        try {
            ipConfig= StringUtils.getIp(request) + ":" + request.getRemotePort();
            lock=  luaExpress(publicKey,"30",ipConfig);
            if(!lock){
                String value=(String) redisTemplate.opsForValue().get(publicKey);
                log.warn("key have exist belong to:"+value);
            }else{
                //获取锁成功
                log.info("start lock lockNxExJob success");
                // Thread.sleep(5000);
            }
        }catch (Exception e){
            log.error("setNx error!");
            e.printStackTrace();
        }finally {
            // redisTemplate.delete(publicKey);
            releaseLock(publicKey,ipConfig);
        }
    }


    /**
     * 获取lua结果
     * @param key
     * @param value
     * @return
     */
    public Boolean luaExpress(String key,String  time,String value) {
        lockScript = new DefaultRedisScript<String>();
        lockScript.setScriptSource(
                new ResourceScriptSource(new ClassPathResource("add.lua")));
        lockScript.setResultType(String.class);
        // 封装参数
        List<Object> keyList = new ArrayList<Object>();
        keyList.add(key);
        keyList.add(time);
        keyList.add(value);
        String result= (String)redisTemplate.execute(lockScript, keyList);
        System.out.println(result);
        log.info("redis set result："+result);
        if (!"ok".equals(result.toLowerCase())){
            return false;
        }
        return true;
    }

    /**
     * 释放锁操作
     * @param key
     * @param value
     * @return
     */
    private boolean releaseLock(String key, String value) {
        DefaultRedisScript<Boolean> booleanDefaultRedisScript = new DefaultRedisScript<>();
        booleanDefaultRedisScript.setScriptSource(
                new ResourceScriptSource(new ClassPathResource("unlock.lua")));
        booleanDefaultRedisScript.setResultType(Boolean.class);
        // 封装参数
        List<Object> keyList = new ArrayList<Object>();
        keyList.add(key);
        keyList.add(value);
        Boolean result = (Boolean) redisTemplate.execute(booleanDefaultRedisScript, keyList);
        return result;
    }
}
```

# ReaLock

**多节点redis实现的分布式锁算法(RedLock):有效防止单点故障**

假设有5个完全独立的redis主服务器

1.获取当前时间戳

2.client尝试按照顺序使用相同的key,value获取所有redis服务的锁，在获取锁的过程中的获取时间比锁过期时间短很多，这是为了不要过长时间等待已经关闭的redis服务。并且试着获取下一个redis实例。

  比如：TTL为5s,设置获取锁最多用1s，所以如果一秒内无法获取锁，就放弃获取这个锁，从而尝试获取下个锁

3.client通过获取所有能获取的锁后的时间减去第一步的时间，这个时间差要小于TTL时间并且至少有3个redis实例成功获取锁，才算真正的获取锁成功

4.如果成功获取锁，则锁的真正有效时间是 TTL减去第三步的时间差 的时间；比如：TTL 是5s,获取所有锁用了2s,则真正锁有效时间为3s(其实应该再减去时钟漂移);

5.如果客户端由于某些原因获取锁失败，便会开始解锁所有redis实例；因为可能已经获取了小于3个锁，必须释放，否则影响其他client获取锁

## 图解

![](http://q8sats5bw.bkt.clouddn.com/img/image-20200409114526056.png)

具体详情参考 ： https://www.cnblogs.com/rgcLOVEyaya/p/RGC_LOVE_YAYA_1003days.html