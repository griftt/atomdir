
####一. 使用git的webhook刷新项目的配置文件
#####   1. 在存放配置文件的git仓库的webhook上配置通知的接口
   -http://e6u35d.natappfree.cc/actuator/bus-refresh
   本地的话需要做内网穿透

#####  2.   服务端需要在yml文件加的配置 ,暴露bus-refresh接口,使其能收到git发送的通知
  ```java
management:
  endpoints:
    web:
      exposure:
        include:  bus-refresh # 暴露bus-refresh为了接到通知
  ```
 #####3. 由于git的发送的息中包含很多其他的信息，会导致json转换的异常，报403，需要做相应的处理
    (1)过滤 bus-refresh这个请求
  ```java
  @WebFilter(filterName = "webhook",urlPatterns = "/*")
@Component
public class WebHookFilter implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest httpServletRequest = (HttpServletRequest)servletRequest;
        HttpServletResponse httpServletResponse = (HttpServletResponse)servletResponse;

        String url = new String(httpServletRequest.getRequestURI());

        //只过滤/actuator/bus-refresh请求
        if (!url.endsWith("/bus-refresh")) {
            filterChain.doFilter(servletRequest, servletResponse);
            return;
        }
        System.err.println("处理/actuator/bus-refresh请求");

        //获取原始的body
        //String body = getParm(httpServletRequest);

        //log.info("original body:{}", body);

        //使用HttpServletRequest包装原始请求达到修改post请求中body内容的目的
        CustometRequestWrapper requestWrapper = new CustometRequestWrapper(httpServletRequest);

        filterChain.doFilter(requestWrapper, servletResponse);

    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.err.println("初始化webhook filter");
    }

    @Override
    public void destroy() {

    }
}
  ```
    (2)对这个请求的内容做处理 ,如下为直接进无用的信息全部清理掉

```java
    public class CustometRequestWrapper extends HttpServletRequestWrapper {

    public CustometRequestWrapper(HttpServletRequest request) {
        super(request);
    }
    @Override
    public ServletInputStream getInputStream() throws IOException {
        byte[] bytes = new byte[0];
        ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(bytes);

        return new ServletInputStream() {
            @Override
            public boolean isFinished() {
                return byteArrayInputStream.read() == -1 ? true:false;
            }

            @Override
            public boolean isReady() {
                return false;
            }

            @Override
            public void setReadListener(ReadListener readListener) {

            }

            @Override
            public int read() throws IOException {
                return byteArrayInputStream.read();
            }
        };
    }

}
    ```
=======
####1./bus-refresh
不起作用问题，需要先暴露该接口


####2.请求 /bus-refresh 后文件已拉取但不生效问题，

  1.需要refreshScope 需要确定刷新哪个配置
  2.暴露接口后需要通过git或gitee上的webhook让他们在更新后向应用发送消息
