title: python闭包与装饰器
date: 2016-1-8 22:48:25
categories: python
tags: [python,闭包,脚本语言]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexoPython%E7%9F%A5%E8%AF%86%E7%BB%93%E6%9E%84.png
---

## python闭包与装饰器

### 闭包
如果在一个内部函数里，对在外部作用域（但不是在全局作用域）的变量进行引用，那么内部函数就被认为是闭包（closure）。它只不过是个“内层”的函数，由一个名字（变量）来指代，而这个名字（变量）对于“外层”包含它的函数而言，是本地变量。
实例：
```python
def set_passline(passline):
    def cmp(val):
        if val >= passline:
            print ('pass')
        else:
            print ('failed')
    return cmp

f_100 = set_passline(60)
f_100(89)

f_150 = set_passline(90)
f_150(89)
```

### 装饰器
简单地来说装饰器就是在不改变原有函数的前提下，为函数加入新的功能。

实例：
```python
def dec(func):
    def in_dec(*arg):
        if len(arg) == 0:
            return 0
        for val in arg:
            if not isinstance(val , int):
                return 0
        
        return func(*arg)
    return in_dec

@dec
def my_sum(*arg):
    return sum(arg)

def my_average(*arg):
    return sum(arg)/len(arg)
#my_sum = dec(my_sum)
my_average = dec(my_average)

print (my_sum(1,2,5,4,5))

print (my_sum(1,2,3,4,5,'6'))

```