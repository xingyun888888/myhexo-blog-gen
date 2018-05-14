---
title: java文件上传知识点总结
date: 2018-01-10 15:54:12
categories: java
tags: java
---


1.[文件上传到局域网另一台计算机(针对window)参考链接](https://www.cnblogs.com/ricciozhang/articles/4830427.html)

2.[springmvc文件上传](https://juejin.im/post/594b31da1b69e60062a199fa)

3.[tomcat项目部署的方式](https://www.cnblogs.com/jiangxifanzhouyudu/p/6859879.html?utm_source=itdadao&utm_medium=referral)


- 碰到的问题总结

``` 
  如何获取服务器文件夹的路径:
  request.getSession().getServletContext().getRealPath("/");
  
  获取项目名称:
  contextPath = request.getContextPath();   
  
  如何获得服务器项目名称:
  request.getServletContext().getContextPath();
  
  如何服务器访问路径:
  request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+contextPath+"/";
```


<!--more-->

- SpringMVC 单文件上传与多文件上传

```
 springmvc.xml配置 添加一下注解
 
<!-- 注意：CommonsMultipartResolver的id是固定不变的，一定是multipartResolver，不可修改 -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 如果上传后出现文件名中文乱码可以使用该属性解决 -->
        <property name="defaultEncoding" value="utf-8"/>
        <!-- 单位是字节，不设置默认不限制总的上传文件大小，这里设置总的上传文件大小不超过1M（1*1024*1024） -->
        <property name="maxUploadSize" value="1048576"/>
        <!-- 跟maxUploadSize差不多，不过maxUploadSizePerFile是限制每个上传文件的大小，而maxUploadSize是限制总的上传文件大小 -->
        <property name="maxUploadSizePerFile" value="1048576"/>
    </bean>

    <!-- 设置一个简单的异常解析器，当文件上传超过大小限制时跳转 -->
    <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <property name="defaultErrorView" value="/error.jsp"/>
    </bean>
    
    控制器
    
    @Controller
    @RequestMapping("/test")
    public class MyController {
    
        @RequestMapping(value = "/upload.do", method = RequestMethod.POST)
        // 这里的MultipartFile[] imgs表示前端页面上传过来的多个文件，imgs对应页面中多个file类型的input标签的name，但框架只会将一个文件封装进一个MultipartFile对象，
        // 并不会将多个文件封装进一个MultipartFile[]数组，直接使用会报[Lorg.springframework.web.multipart.MultipartFile;.<init>()错误，
        // 所以需要用@RequestParam校正参数（参数名与MultipartFile对象名一致），当然也可以这么写：@RequestParam("imgs") MultipartFile[] files。
        public String upload(@RequestParam MultipartFile[] imgs, HttpSession session)
                throws Exception {
            for (MultipartFile img : imgs) {
                if (img.getSize() > 0) {
                    String path = session.getServletContext().getRealPath("images");
                    String fileName = img.getOriginalFilename();
                    if (fileName.endsWith("jpg") || fileName.endsWith("png")) {
                        File file = new File(path, fileName);
                        img.transferTo(file);
                    }
                }
            }
            return "/success.jsp";
        }
    }
    如果是多种文件上传
    @Controller
    @RequestMapping("/test")
    public class MyController {
    
        @RequestMapping(value = "/upload.do", method = RequestMethod.POST)
        public String upload(@RequestParam MultipartFile[] imgs1,@RequestParam MultipartFile[] imgs2,@RequestParam MultipartFile[] imgs3, HttpSession session)
                throws Exception {
            String path = session.getServletContext().getRealPath("images");
            for (MultipartFile img : imgs1) {
                uploadFile(path, img);
            }
            for (MultipartFile img : imgs2) {
                uploadFile(path, img);
            }
            for (MultipartFile img : imgs3) {
                uploadFile(path, img);
            }
            return "/success.jsp";
        }
    
        private void uploadFile(String path, MultipartFile img) throws IOException {
            if (img.getSize() > 0) {
                String fileName = img.getOriginalFilename();
                if (fileName.endsWith("jpg") || fileName.endsWith("png")) {
                    File file = new File(path, fileName);
                    img.transferTo(file);
                }
            }
        }
    }
    

```
- multipartFile类的常用方法

``` 
String getContentType()//获取文件MIME类型
InputStream getInputStream()//获取文件流
String getName() //获取表单中文件组件的名字
String getOriginalFilename() //获取上传文件的原名
long getSize()  //获取文件的字节大小，单位byte
boolean isEmpty() //是否为空
void transferTo(File dest) 

```

- CommonsMultipartResolver的属性解析

``` 
defaultEncoding：表示用来解析request请求的默认编码格式，当没有指定的时候根据Servlet规范会使用默认值ISO-8859-1。当request自己指明了它的编码格式的时候就会忽略这里指定的defaultEncoding。
uploadTempDir：设置上传文件时的临时目录，默认是Servlet容器的临时目录。
maxUploadSize：设置允许上传的总的最大文件大小，以字节为单位计算。当设为-1时表示无限制，默认是-1。
maxUploadSizePerFile：跟maxUploadSize差不多，不过maxUploadSizePerFile是限制每个上传文件的大小，而maxUploadSize是限制总的上传文件大小。
maxInMemorySize：设置在文件上传时允许写到内存中的最大值，以字节为单位计算，默认是10240。
resolveLazily：为true时，启用推迟文件解析，以便在UploadAction中捕获文件大小异常。
```
