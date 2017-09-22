## hexoBlog-generate


- 常见命令
``` 
    hexo new "postName" #新建文章
    hexo new page "pageName" #新建页面
    hexo generate #生成静态页面至public目录
    hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
    hexo deploy #部署到GitHub
    hexo help  # 查看帮助
    hexo version  #查看Hexo的版本
```

- 缩写

```` 
  hexo n == hexo new
  hexo g == hexo generate
  hexo s == hexo server
  hexo d == hexo deploy
````

- 组合命令

``` 
  hexo s -g #生成并本地预览
  hexo d -g #生成并上传
```

-  直接执行hexo d的话一般会报如下错误：

``` 
  需要安装插件 npm install hexo-deployer-git --save
```

- 一般完整格式如下:

```  

title: postName #文章页面上的显示名称，一般是中文
date: 2013-12-02 15:30:16 #文章生成时间，一般不改，当然也可以任意修改
categories: 默认分类 #分类
tags: [tag1,tag2,tag3] #文章标签，可空，多标签请用格式，注意:后面有个空格
description: 附加一段文章摘要，字数最好在140字以内，会出现在meta的description里面

```
- 如何让博文列表不显示全部内容

``` 
  答案是在合适的位置加上<!--more-->即可
```

[参考](http://www.cnblogs.com/liuxianan/p/build-blog-website-by-hexo-github.html)