title: UML类图的6种关系
date: 2016-1-2 20:54:32
categories: UML
tags: [oop,UML,关系]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexoUML.png
---

在UML类图中，常见的有以下几种关系:泛化（Generalization）,  实现（Realization）,关联（Association）,聚合（Aggregation）,组合(Composition)，依赖(Dependency)

### 泛化(Generalization)关系
一种继承关系,它指定了子类如何特化父类的所有特征和行为例如：兔子是动物的一种,男人是一类人。

图形：带三角箭头的实线，箭头指向父类

![泛化](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E6%B3%9B%E5%8C%96.gif)


### 实现(Realization)关系
一种类与接口的关系，表示类是接口所有特征和行为的实现

图形：带三角箭头的虚线，箭头指向接口

![实现](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E5%AE%9E%E7%8E%B0.gif)

### 关联(Association)关系
一种拥有的关系,它使一个类知道另一个类的属性和方法；如：老师与学生，丈夫与妻子
关联可以是双向的，也可以是单向的。双向的关联可以有两个箭头或者没有箭头，单向的关联有一个箭头。

图形：带普通箭头的实心线，指向被拥有者

![关联](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E5%85%B3%E8%81%94.gif)

### 聚合(Aggregation)关系
整体与部分的关系.如车和轮胎是整体和部分的关系，聚合关系是关联关系的一种，是强的关联关系；关联和聚合在语法上无法区分，必须考察具体的逻辑关系。

图形：带空心菱形的实心线，菱形指向整体

![聚合](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E8%81%9A%E5%90%88.gif)


### 组合(Composition)关系
整体与部分的关系,没有公司就不存在部门，组合关系是关联关系的一种，是比聚合关系还要强的关系，它要求普通的聚合关系中代表整体的对象负责代表部分的对象的生命周期

图形：带实心菱形的实线，菱形指向整体

![组合](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E7%BB%84%E5%90%88.gif)

### 依赖(Dependency)关系
一种使用关系，特定事物的改变有可能会影响到使用该事物的其他事物，在需要表示一个事物使用另一个事物时使用依赖关系。大多数情况下，依赖关系体现在某个类的方法使用另一个类的对象作为参数。

图形：带箭头的虚线表示，由依赖的一方指向被依赖的一方

![依赖](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E4%BE%9D%E8%B5%96.gif)


### 综合示例

![示例](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E7%BB%BC%E5%90%88.gif)


转载自：[UML类图几种关系的总结](http://blog.csdn.net/tianhai110/article/details/6339565)


