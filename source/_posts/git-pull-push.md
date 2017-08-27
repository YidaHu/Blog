---
title: Git基本使用
date: 2016-03-03 22:27:10
categories: MISC
tags: 
- Git
---

## **克隆远程仓库到本地**

```
git clone git@github.com:huyida/test.git    //克隆到本地11
```

## **推送到远程仓库**

```
git init    //初始化本地仓库
git add .   //添加当前目录下所有文件包括文件夹
git commit -m 'This is a xxx'   //添加描述
git remote add origin git@github.com:huyida/test.git    //建立远程仓库连接
git pull origin master  //拉取远程仓库
git push -u origin master   //将本地仓库的文件提交到地址是origin的地址，master分支下123456123456
```

<!--MORE-->

## 删除远程仓库**

```
git remote rm [别名]  //删除远程仓库
```