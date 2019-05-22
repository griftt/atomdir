####javax项目路径的获取
```java
在jsp和class文件中调用的相对路径不同：

在jsp里，根目录是WebRoot 

在class文件中，根目录是WebRoot/WEB-INF/classes 

当然你也可以用System.getProperty("user.dir")获取你工程的绝对路径。

1.jsp中取得路径：
    以工程名为TEST为例：
    (1)得到包含工程名的当前页面全路径：request.getRequestURI()
        结果：/TEST/test.jsp

    (2)得到工程名：request.getContextPath()
        结果：/TEST

    (3)得到当前页面所在目录下全名称：request.getServletPath()
        结果：如果页面在jsp目录下 /TEST/jsp/test.jsp

    (4)得到页面所在服务器的全路径：application.getRealPath("页面.jsp")
         结果：D:\resin\webapps\TEST\test.jsp

    (5)得到页面所在服务器的绝对路径：

        absPath=new java.io.File(application.getRealPath(request.getRequestURI())).getParent();
        结果：D:\resin\webapps\TEST

2.在类中取得路径：
    (1)类的绝对路径：Class.class.getClass().getResource("/").getPath()
        结果：/D:/TEST/WebRoot/WEB-INF/classes/pack/

    (2)得到工程的路径：System.getProperty("user.dir")
        结果：D:\TEST

3.在Servlet中取得路径：
    (1)得到工程目录：request.getSession().getServletContext().getRealPath("") 参数可具体到包名。
        结果：E:\Tomcat\webapps\TEST

    (2)得到IE地址栏地址：request.getRequestURL()
        结果：http://localhost:8080/TEST/test

    (3)得到相对地址：request.getRequestURI()
        结果：/TEST/test

1.如何获得当前文件路径

    常用：
    (1).Test.class.getResource("")
        得到的是当前类FileTest.class文件的URI目录。不包括自己！
    (2).Test.class.getResource("/")
        得到的是当前的classpath的绝对URI。
    (3).Thread.currentThread().getContextClassLoader().getResource("")
        得到的也是当前ClassPath的绝对URI。
    (4).Test.class.getClassLoader().getResource("")
        得到的也是当前ClassPath的绝对URI。
    (5).ClassLoader.getSystemResource("")
        得到的也是当前ClassPath的绝对URI。
        尽量不要使用相对于System.getProperty("user.dir")当前用户目录的相对路径。
    (6) new File("").getAbsolutePath()也可用。

       注：若这些路径中出现空格，并且空格被%20代替，则可以使用path = java.net.URLDecoder.decode(path,"utf-8")     进行转化;
       
2.Web服务器
    (1).Tomcat
         在类中输出System.getProperty("user.dir");显示的是%Tomcat_Home%/bin
    (2).Resin
        不是你的JSP放的相对路径，是JSP引擎执行这个JSP编译成Servlet的路径为根.比如用新建文件法测试File f = new         File("a.htm");
        这个a.htm在resin的安装目录下 
    (3).如何读文件
        使用ServletContext.getResourceAsStream()就可以
    (4).获得文件真实路径

        String   file_real_path=ServletContext.getRealPath("mypath/filename");  
        不建议使用request.getRealPath("/"); 
---------------------
作者：星空_强
来源：CSDN
原文：https://blog.csdn.net/sinat_32759831/article/details/79640269
版权声明：本文为博主原创文章，转载请附上博文链接！

```
```

```
