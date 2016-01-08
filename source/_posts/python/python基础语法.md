title: python基础语法
date: 2016-1-5 22:00:30
categories: python
tags: [python,脚本语言]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexoPython%E7%9F%A5%E8%AF%86%E7%BB%93%E6%9E%84.png
---
## Python基础语法

### python版本
虽然python3已经发布很长时间了，但现在大部分开发者使用的版本还是python2。主要原因是python3的改动比较大，并且向下兼容性不是很好，但是python3是未来的发展方向，所以作为初学者还是推荐学习python3版本。

```python
[centos@bogon ~]$ python
Python 2.6.6 (r266:84292, Nov 22 2013, 12:16:22) 
[GCC 4.4.7 20120313 (Red Hat 4.4.7-4)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> 
>>> 
>>> 
>>> 
[centos@bogon ~]$ python3
Python 3.4.4 (default, Jan  5 2016, 21:54:11) 
[GCC 4.4.7 20120313 (Red Hat 4.4.7-16)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

### python的安装
下面主要介绍在python2的基础上安装python3(python2与python3版本共存)，因为现在大部分linux发现版本自带的python版本还是python2
1. 安装环境

```bash
yum install gcc 
yum install zlib-devel 
yum install make

```

2. 下载python3版本
进入网站选择python版本：https://www.python.org/ftp/python/
这里选择的是Python-3.4.4.tgz 

```bash
wget http://www.python.org/ftp/python/3.4.4/Python-3.4.4.tgz 

```

3. 解压、编辑和安装

```bash
tar -zxvf Python-3.4.0.tgz 
cd Python-3.4.0 
./configure –prefix=/opt/python3 
make all 
make install 

```

4. 链接到bin目录

```bash
ln -s /opt/python3/bin/python3 /usr/bin/python3
```

**提示：在python交互模式下方向键变字母的问题**

```bash
#安装readline-devel
$yum install readline-devl
#重新安装python
$./configure
$sudo make install

```

### 字符串

#### 三种引号的区别
实例：
```python
>>> 'hello python3'
'hello python3'
>>> 
>>> "this's python3"
"this's python3"
>>> 
>>> 
>>> """hello python3
... this's python3"""
"hello python3\nthis's python3"

```

**提示：具体使用哪种方式根据需求灵活选择**

#### 字符串连接
实例：
```python
>>> "michael"+" "+"jackson"
'michael jackson'
>>> 
>>> "micheal" "jackson"
'michealjackson'
>>> 
>>> "micheal " "jackson"
'micheal jackson'
```

**注意：不推荐使用第二种方式**

#### 格式说明符
实例：
```python
>>> "John %s%s" % ("Every", "Man")
'John EveryMan'
>>>
>>> "%s %s %10s" % ("John" , "Every" , "Man")
'John Every        Man'
>>>
>>> "%-8s %s %10s" % ("John" , "Every" , "Man")
'John     Every        Man'
```

#### 原始字符串与Unicode

原始字符串r:
```python
>>> print('Hello.\npython!')
Hello.
python!
>>> 
>>> print(r'Hello.\npython!')
Hello.\npython!
```

Unicode对象：
python中的普通字符串在内部是以8位的ASCII码形式存储的，而Unicode字符串则存储为16的Unicode字符，这样能够表示更多的字符集。
```python
>>> u'Hello.world!'
'Hello.world!'
>>> 
```

### 数值与运算符
三种数值类型
```python
>>> type(1)
<class 'int'>
>>>
>>> type(1.0)
<class 'float'>
>>>
>>> type(1j)
<class 'complex'>
```

简单数值运算

```python
>>> 1+1
2
>>>
>>> 1+1.0
2.0
>>>
>>> 2e23445*3e45464
inf
>>>
>>> 33/11
3.0
>>>
>>> 4.0/2.0
2.0
>>>
>>> 5/3
1.6666666666666667
>>>
>>> 5%3
2
```

数值格式化

```python
>>> print("%.2f" % 2.322551)
2.32
>>>
>>> print("%0.f" % (343*0.2))
69
```


### 参考
《Python编程入门经典》

### 捐赠
如何觉得本文章对你有帮助，欢迎捐赠

![捐赠](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E6%94%AF%E4%BB%98%E5%AE%9D%E4%BB%98%E6%AC%BE%E4%BA%8C%E7%BB%B4%E7%A0%81%E6%9C%80%E5%B0%8F.png)