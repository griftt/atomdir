1。用于全局异常拦截注解，  demo


    /**
     * 拦截业务异常
     */
    //拦截的异常类型
    @ExceptionHandler(ServiceException.class)
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    @ResponseBody
    public ErrorResponseData bussiness(ServiceException e) {
        LogManager.me().executeLog(LogTaskFactory.exceptionLog(ShiroKit.getUser().getId(), e));
        getRequest().setAttribute("tip", e.getMessage());
        log.error("后台业务异常:", e);
        return new ErrorResponseData(e.getCode(), e.getMessage());
    }
出现的问题：将异常信息返回到前端时，线上报504超时，本地没问题，
