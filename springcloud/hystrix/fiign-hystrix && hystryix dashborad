####  1.feign-hystrix
feign里引入了hystrix.需要通过yml开启，
feign:
  hystrix:
    enabled: true #k开启feign里的hystrix
####  2.在需要暴露的类上添加相应的注解
```java


@FeignClient(name = "product",fallback = GoodsOrderFeignClient.ProductClientFallBack.class)
public interface GoodsOrderFeignClient {

    @GetMapping("/goods/all")
    public List<Goods> getGoodsList();

    @GetMapping("/goods/one")
    public Goods getGoodsById(Integer id);


    @Component
   public static class ProductClientFallBack implements GoodsOrderFeignClient{

        @Override
        public List<Goods> getGoodsList() {
            Goods goods = new Goods();
            goods.setBatchNo(" 服务降级启动list");
            List<Goods> list = new ArrayList<>();
             list.add(goods);
             return list;
        }

        @Override
        public Goods getGoodsById(Integer id) {
            Goods goods = new Goods();
            goods.setBatchNo(" 服务降级启动one ");
            return goods;
        }
    }
}
```
@FeignClient：name value ,serviceId均为应用名，多个FeignClient可用同一个应用名，
但需要注意bean的重复注入问题,如下
 - Feign报错'xx.FeignClientSpecification', defined in null, could not be registered.
  feign同名引起的报错：  处理方法
  ```java
  服务调用方引入
  spring:
   main:
    allow-bean-definition-overriding: true #用于解决 product.FeignClientSpecification', defined in null,在feign服务同名时产生
