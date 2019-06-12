  ###@cacheable
1. 该注解可用于方法和类，用于类则代表该类下的所有方法都支持缓存，用于方法则代表该方法的返回值将被缓存
  但是否缓存也取决与key等参数的配置
2. demo:
```java
    //@Cacheable(value = CacheConstant.EcacheName.SYS_PARAM, key = "'key_'+#key")
    @Cacheable(value = "cachename", key = "#key")
    public Sysparam loadSetting(String key) {
        QueryWrapper<Sysparam> wrapper = new QueryWrapper<>();
        wrapper.eq("param_key", key)
                .eq("record_status", ApplicationConstant.recordStatus.normal);
        return this.getOne(wrapper);
    }
    ```java
参数详解：
value:所使用的缓存的名字，此处使用的ehcahe,
key:相当于该缓存内数据的key，
key规则如下：
        - 使用方法参数作为key,多个参数用 '_' 隔开，如："#参数名_#参数名"
![key 可选参数类型](images/2019/05/20180807164005629.png)
3.
