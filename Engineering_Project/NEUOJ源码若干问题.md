---
title: NEUOJ源码若干问题
date: 2017-03-02 00:02:01
categories:
- Engineering_project
tags:
- neuoj
description: 查阅NEUOJ代码时看到的一些东西
---

# NEUOJ源码若干问题
> 本来以为这一部分可写的比较多，之后看了一部分发现代码还是比较简单的。之后的部分会持续跟进，持续更新。

## 2015年（11.25-12.31）
* .gitignore文件
  * 在这个文件中配置不需要加入到版本管理的文件
  * 语法，直接把文件名/目录写入到本文件即可，可以使用通配符
  
        ```
        以斜杠“/”开头表示目录；
        以星号“*”通配多个字符；
        以问号“?”通配单个字符
        以方括号“[]”包含单个字符的匹配列表；
        以叹号“!”表示不忽略(跟踪)匹配到的文件或目录；
        ```
* composer.phar是可执行文件，用来安装composer
* composer.lock文件
  * 在安装依赖后，composer见将把安装时确切的版本号列表写入composer.lock文件。这将锁定改项目的特定版本。
  * 官方提倡把composer.lock和composer.json提交到版本库
* composer.json中包版本管理  
  |名称|实例|描述|
  |---|---|---|
  |确切的版本号|1.0.2|指定包的确切版本|
  |范围|`>=1.0 >=1.0,<2.0 >=1.0,<1.1\|>=1.2`||
  |通配符|1.0.*||
  |赋值运算符|~1.2|下一个重要版本,相当于 >=1.2,<2.0|
* fillable 属性指定哪些属性可以被集体赋值, guarder是fillable的反义词
* Validator门面
    * 直接使用Validator::make()
* php的nl2br() 函数在字符串中的每个新行（`\n`）之前插入 HTML 换行符（`<br> 或 <br />`）

## 2016年(11.25)
* php的mt_rand([min], [max])函数，返回min到max之间的随机数
* php的uniqid([prefix],[more_entropy]) 函数基于以微秒计的当前时间，生成一个唯一的 ID
