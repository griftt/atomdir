

###Lambda语法demo

```
　Java 8 中的 Stream 是对集合（Collection）对象功能的增强，它专注于对集合对象进行各种非常便利、高效的聚合操作（aggregate operation），或者大批量数据操作 (bulk data operation)。Stream API 借助于同样新出现的 Lambda 表达式，极大的提高编程效率和程序可读性。同时它提供串行和并行两种模式进行汇聚操作，并发模式能够充分利用多核处理器的优势

Stream 不是集合元素，它不是数据结构并不保存数据，它是有关算法和计算的，它更像一个高级版本的 Iterator。获取一个数据源（source）→ 数据转换→执行操作获取想要的结果，每次转换原有 Stream 对象不改变，返回一个新的 Stream 对象（可以有多次转换），这就允许对其操作可以像链条一样排列，变成一个管道
为什么不在集合类实现这些操作，而是定义了全新的Stream API？Oracle官方给出了几个重要原因：

一是集合类持有的所有元素都是存储在内存中的，非常巨大的集合类会占用大量的内存，而Stream的元素却是在访问的时候才被计算出来，这种“延迟计算”的特性有点类似Clojure的lazy-seq，占用内存很少。

二是集合类的迭代逻辑是调用者负责，通常是for循环，而Stream的迭代是隐含在对Stream的各种操作中，例如map()。

对于基本数值型，目前有三种对应的包装类型 Stream：IntStream、LongStream、DoubleStream。


  stream是Java8新增的一大API。官方定义：支持在元素流上支持功能式操作，例如映射减少集合上的转换。

特点：

       不储存。数据流不是存储元素的数据结构；相反，它将元素从源数据结构、数组、生成器函数或输入/输出通道传递到计算操作的管道中。

       功能性。一个流操作产生一个结果，但不修改它的源。例如，从一个集合中过滤得到的数据流产生一个新的流，而不过滤元素，而不是从源集合中移除元素。

        懒惰寻求。许多流操作，如过滤、映射，或去除重复，可以懒洋洋地，暴露的机会，优化。例如，“寻找三个连续的元音字母的第一个字符串”不需要检查所有的输入字符串。流操作分为中间（流生产）操作和终端（价值或副作用生产）操作。中间操作总是懒惰。

       可能无界。虽然集合有一个有限的大小，流不需要。短路操作如极限（n）或findfirst()可以允许无限流计算在有限的时间内完成.

        意为流但与I/O流又有所不同。Stream 是对集合（Collection）对象功能的增强，它专注于对集合对象进行各种非常便利、高效的聚合操作（aggregate operation），或者大批量数据操作 (bulk data operation)。Stream API 借助于同样新出现的 Lambda 表达式，极大的提高编程效率和程序可读性。同时它提供串行和并行两种模式进行汇聚操作，并发模式能够充分利用多核处理器的优势，使用 fork/join 并行方式来拆分任务和加速处理过程。通常编写并行代码很难而且容易出错, 但使用 Stream API 无需编写一行多线程的代码，就可以很方便地写出高性能的并发程序。所以说，Java 8 中首次出现的 java.util.stream 是一个函数式语言+多核时代综合影响的产物。

Lambda语法:

(params) -> expression
(params) -> statement
(params) -> { statements }


关键字:

filter:是一个中间操作,接受一个predicate接口类型的变量，并将所有流对象中的元素进行过滤。filter(s -> s.getState()==State.pay)

map:是一个对于流对象的中间操作，通过给定的方法，它能够把流对象中的每一个元素对应到另外一个对象上。map(s -> s.getPlanNo()) / map(s -> Plan::planNo)  /  价格变成    10倍 map(s -> s.getPrice().multiply(BigDecimal.valueOf(10)))
reduce:把 Stream 元素组合起来。它提供一个起始值（种子），然后依照运算规则（BinaryOperator）,返回单个的结果值，并且reduce操作每处理一个元素总是创建一个新值

          BigDecimal total = stream().reduce(BigDecimal.zero, (a,b) -> a.add(b));   或

          BigDecimal total = stream().reduce(BigDecimal.ZERO, BigDecimal::add)

limit : 返回 Stream 的前面 n 个元素；skip 则是扔掉前 n 个元素

sorted: 一个中间操作，能够返回一个排过序的流对象的视图。流对象中的元素会默认按照自然顺序进行排序，除非你自己指定一个Comparator接口来改变排序规则.

collect: 修改现存的值
Collectors 类的主要作用就是辅助进行各类有用的 reduction 操作
groupingBy  按规则分组:stream().collect(Collectors.groupingBy(p->p.getState()))
partitioningBy 是一种特殊的 groupingBy，它依照条件测试的是否两种结果来构造返回的数据结构，get(true) 和 get(false) 能即为全部的元素对象。

Stream 有三个 match 方法，从语义上说：
allMatch：Stream 中全部元素符合传入的 predicate，返回 true
anyMatch：Stream 中只要有一个元素符合传入的 predicate，返回 true
noneMatch：Stream 中没有一个元素符合传入的 predicate，返回 true


示例,domain

```
public class Plan {````
    private int id;
    private String planNo;
    private BigDecimal price;
    private long total;
    private State state;
    private Calendar createTime;
    private JSONObject features = new JSONObject();
    ```

Sate :noPay(1,"未支付"),     pay(2,"支付"),      settle(3,"结算"),


List<Plan>  planList = initList();
1. 把方案编号planNo转换大写 返回列表

List<String> noList = planList.stream().map(p->p.getPlanNo().toUpperCase()).collect(Collectors.toList());


2,价格由高到低排序

List<Plan> list = planList.stream().sorted((a,b) -> b.getPrice().compareTo(a.getPrice())).collect(Collectors.toList());


3,状态为支付的价格由高到低排序

planList.stream().filter(s -> State.pay == s.getState()).sorted((a,b) -> b.getPrice().compareTo(a.getPrice())).collect(Collectors.toList());
4,求最高价/最低价/总价,   total数量平均,总和
BigDecimal max = planList.stream().max((a,b)->a.getPrice().compareTo(b.getPrice())).get().getPrice();
BigDecimal min = planList.stream().min((a,b)->a.getPrice().compareTo(b.getPrice())).get().getPrice();
BigDecimal total = planList.stream().map(p->p.getPrice()).reduce(BigDecimal.ZERO,(a,b)->a.add(b));

平均 : planList.stream().mapToLong(Plan::getTotal).average().getAsDouble();

总和:planList.stream().mapToLong(Plan::getTotal).sum()
5,总共有多少种状态值

long count = planList.stream().map(p->p.getState()).distinct().count();
long c2 = planList.stream().map(p->p.getState()).collect(Collectors.toSet()).size();


6,方案编号中包含某些字符

List<Plan> list = planList.stream().filter(p->p.getPlanNo().contains("gt")).collect(Collectors.toList());


7,价格前三的方案

List<Plan> topList = planList.stream().sorted((a,b)->b.getPrice().compareTo(a.getPrice())).limit(3).collect(Collectors.toList());


8,按方案状态分组列表

Map<State, List<Plan>> map = planList.stream().collect(Collectors.groupingBy(p->p.getState()));


9,方案分成是否支付二种,查询列表

Map<Boolean, List<Plan>> map = planList.stream().collect(Collectors.partitioningBy(s -> s.getState()==State.noPay));

map.get(true) 是全部 未支付

map.get(false) 是支付 和 结算

10,转换成Map结构 <方案编号 ,  价格>

Map<String, BigDecimal> map = planList.stream().collect(Collectors.toMap(p->p.getPlanNo(), Plan::getPrice));


11,转换数据结构 ,  list转成数组
Plan[] ps = planList.stream().toArray(Plan[]::new);


12,按状态算数量的总和/平均数

平均:Map<State, Double> map = planList.stream().collect(Collectors.groupingBy(Plan::getState, Collectors.averagingLong(Plan::getTotal)));

求和:Map<State, Long> sum = planList.stream().collect(Collectors.groupingBy(Plan::getState, Collectors.summingLong(Plan::getTotal)));


Map<State, LongSummaryStatistics> sumMap =  planList.stream().collect(Collectors.groupingBy(Plan::getState, Collectors.summarizingLong(Plan::getTotal)));

LongSummaryStatistics描述流中元素的各种摘要数据,求 count, min, max, sum, and average.
```
