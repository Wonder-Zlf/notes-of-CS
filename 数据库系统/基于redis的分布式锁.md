# 基于redis的分布式锁

```
// 对请求插入的数据用redis保存，存入作为加锁，使用后删除作为解锁
boolean lockRepetitive = redisHelper.save(lockKey, serviceId, CompositionConstant.Digit.EXPIRE_TIME_60);
Result<String> result;
if (lockRepetitive) {
    ....
    redisHelper.delete(lockKey, serviceId);
}

// redis的保存方法，setIfAbsent如果键不存在则新增,存在则不改变已经有的值
public boolean save(String key, String value, long time) {
    Boolean result = redisTemplate.opsForValue().setIfAbsent(key, value, time, TimeUnit.SECONDS);
    return null == result ? false : result;
}
```

业务背景，某个项目的数据库中不允许插入重复的任务数据。

观察上面代码，在对数据库做插入操作前，这里先使用redis保存插入数据的特定key。当有重复的插入请求时，redis保存重复的key会报失败，无法继续访问数据库。

由于这个保存方法中调用的setIfAbsent是串行的，这样就保障不会出现并发请求插入了两个重复数据的情况。

以上的redis加锁方法适用秒杀、抢任务等多个并发控制场景。

