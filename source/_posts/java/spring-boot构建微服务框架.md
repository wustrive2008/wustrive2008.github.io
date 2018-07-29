title: spring boot构建微服务框架
date: 2017-07-11 21:32:31
categories: java
tags: [java,spring boot,微服务,spring]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexojava知识结构.png
---

### 微服务

微服务的概念相信很多人已经很熟悉了，也有很多公司在使用或者渐渐的把原有项目改造为微服务。微服务的优点相信使用过的朋友也深有体会，首先是单一功能独立出来后，复杂度变的可控了，还有最重要的就是比较灵活，调整比较方便。但是当我们把一个整体的系统进行功能拆分后，会出现一个项目变成了很多个项目，一个是很多基础的类库需要在多个项目中共享，还有每个项目都要单独的搭建框架，似乎工作量比拆分之前多了很多，因此我们需要一个能够快速构建项目的框架，spring boot正是这样的一个框架。spring boot 最大的优点就是快。

### spring boot
spring boot就不详细介绍了，不了解的朋友可以先在网上查查了解一下。今天主要介绍一个使用spring boot搭建的一个微服务框架，集成了常用的功能，可以用来作为基础框架，根据业务需求抽取需要的功能搭建微服务，为我们节省大量时间。

![](http://7xifb5.com1.z0.glb.clouddn.com/spring-boot.png)



### 项目地址
[spring boot构建微服务框架](https://github.com/wustrive2008/base-framework)