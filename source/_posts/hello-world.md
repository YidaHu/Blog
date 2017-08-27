---
title: Hexo配置使用与主题配置
date: 2016-01-03 14:10:45
categories: MISC
tags:
- Hexo
---
Hexo采用本地保存源文件并渲染成静态页面部署的方式, 产生的静态文件只要放到任何支持 html 的空间或者服务器均可访问。

## Quick Start

## 1 安装依赖

Hexo 依赖于 Node.js 和 Git，需要先安装。
[下载 Node.js](https://nodejs.org/zh-cn/)
[下载 Git](https://git-scm.com/download/)



## 2 安装Hexo

`$ npm install hexo-cli -g`

在 blog 目录下初始化 hexo 博客，也可以是任意你想要的名字

`$ hexo init blog`

进入博客根目录，并且安装相关插件依赖等

```
$ cd blog
$ npm install
```

安装完成后需要用一下命令

```
$ hexo g # 渲染 Source 目录下文件问静态页面
$ hexo s # 本地跑一个 server 来看博客效果。
```

然后可以在 `http://localhost:4000/` 查看运行效果。

<!--MORE-->

## 3 更换主题

#### 安装主题

在博客的根目录下克隆主题

```
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```

执行（这里_config.yml为博客根目录下的文件）：

```
$ vim _config.yml
```

将 theme 对应的值进行修改

```
theme: yilia
```

接着就自动部署一下：

```
$ npm install hexo-deployer-git --save
```

最后发布：

```
$ hexo clean && hexo g && hexo d
```



## 4 主题配置

现在主题是更改过来了，但还有许多细节需要处理，比如说你需要修改头像等等。

##### 首先进入到根目录下的 themes\yilia 文件夹，执行

```
$ vim _config.yml
```

##### 就能看到这样信息，我是这样配置的：



```
# Header
menu:
 主页: /
 所有文章: /archives
 # 随笔: /tags/随笔

 # SubNav
subnav:
      github: "https://github.com/huyida"  //github地址
      #weibo: "#"   //微博地址
      rss: "#"  //订阅地址
      #zhihu: "#"    // 下面这些前面带#的,就不显示在主页上,如果有账号,就可以打开
      #douban: "#"
      #mail: "#"
      #facebook: "#"
      #google: "#"
       #twitter: "#"
      #linkedin: "#"

rss: /atom.xml 

# Content
excerpt_link: more
fancybox: true
mathjax: true

# 是否开启动画效果
animate: true

# 是否在新窗口打开链接
open_in_new: false

# Miscellaneous
google_analytics: ''
favicon: /favicon.png

#你的头像url
avatar: "/img/logo.png"  //设置头像图片，可以直接拷贝Github头像链接
#是否开启分享
share_jia: true
share_addthis: false
#是否开启多说评论，填写你在多说申请的项目名称 duoshuo: duoshuo-key
#若使用disqus，请在博客config文件中填写disqus_shortname，并关闭多说评论
duoshuo: true     //使用'多说'评论
#是否开启云标签
tagcloud: false

#是否开启友情链接
#不开启——
#friends: ture
#开启——
friends:     //下面可以设置自定义友情链接

#是否开启“关于我”。
#不开启——
#aboutme: false
#开启——
aboutme:    //介绍
```

##### 配置完成以后，回到 **根目录**，**按顺序执行命令**就OK啦。

```
$ npm install hexo-deployer-git --save    
$ hexo clean && hexo g && hexo d
```



## 5 发布文章

新手推荐使用文章编辑器进行写作，我是使用了[Typora](https://dj9399.github.io/www.typora.io)。

完成后，在blog/source/_posts目录下新建一个以.md为后缀的文件，如test.md，然后用Notepad++打开该文件，并把之前写好的内容复制进去保存。
文章的永久链接格式title属性取自该文件名。如使用默认的永久链接格式:year/:month/:day/:title/，则生成的链接为[http://www.huyidada.com/2017/07/09/uploadWebshell/。](http://www.huyidada.com/2017/07/09/uploadWebshell/)

#### 创建新文章命令：

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/deployment.html)



## 6 小技巧

#### Hexo文章上传图片

如果你的Hexo项目中只有少量图片，那最简单的方法就是将它们放在 `source/images` 文件夹中。然后通过类似于 `![](/images/image.jpg)` 的方法访问它们