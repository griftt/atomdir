
1. ####通过代码获取到当前环境的方法：
 ```java
  @Autowired
    DuridConfigProperties duridConfigProperties;
  String profile = context.getEnvironment().getActiveProfiles()[0]; //获取到当前环境

 ```
 2. ####通过@profile实现根据环境的不同做配置。
  @profile
  ```java
  @Configuration
public class ProfileConfig {

    @Bean
    @Profile("upper")
    public UpperAction upperAction1(){
        return  new UpperAction("Tom");
    }

    @Bean
    @Profile("upper1")
    public UpperAction upperAction2(){
        return  new UpperAction("Jack");
    }

    @Bean
    @Profile("环境名")
    public LowerAction lowerAction1(){
        return new LowerAction();
    }
  ```
  @ActiveProfile
  用于在测试中使用，指定某个环境
  ```java
  @RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes =ProfileConfig.class)
@ActiveProfiles("upper")
public class Test4_UsingProfile {

    @Autowired
    Action action;

    @Test
    public void test3() {
        action.execute("wushuohanaaaaaa");
    }
}
  ```
