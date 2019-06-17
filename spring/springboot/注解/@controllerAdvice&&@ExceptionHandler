类注解@ControllerAdvice:
  用于声明一个全局的异常处理类
方法注解  @ExceptionHanler:
  用于确定拦截哪个类
  demo:
```java
//@RestControllerAdvice
@ControllerAdvice
public class GlobalExceptionHandler extends BaseControllerExceptionHandler {

    private Logger log = LoggerFactory.getLogger(this.getClass());
    /**
     * 拦截jwt相关异常
     */

        @ExceptionHandler(JwtException.class)
        @ResponseStatus(HttpStatus.BAD_REQUEST)
        @ResponseBody
        public ErrorTip jwtException(JwtException e) {

            return new ErrorTip(BizExceptionEnum.TOKEN_ERROR.getCode(), BizExceptionEnum.TOKEN_ERROR.getMessage());
        }


        //参数验证错误时返回的信息
        @ExceptionHandler(BindException.class)  // 自拦截BindException
        @ResponseStatus(HttpStatus.BAD_REQUEST)
        @ResponseBody
        public ServerResponse bindExceptionFount(Exception e) {
            StringBuilder stringBuilder = new StringBuilder();
            BindException bindException = (BindException) e;
            for (ObjectError objectError : bindException.getAllErrors()) { // 获取异常信息
                stringBuilder.append(objectError.getDefaultMessage()).append(" ");// 提取定义的错误信息,并组装返回给前端
            }
            return ServerResponse.creatByErrorCodeMsg(500, stringBuilder.toString());
        }

        
      @ExceptionHandler(value = Exception.class)
		public ModelAndView defaultErrorHandler(HttpServletRequest req, Exception e) throws Exception {
			ModelAndView mav = new ModelAndView();
			mav.addObject("exception", e);
			mav.addObject("url", req.getRequestURL());
			mav.setViewName(DEFAULT_ERROR_VIEW);
			return mav;
		}

}
```
