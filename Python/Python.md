# Python

## 虚拟环境

1. **安装软件：**

   ```shell
   pip install virtualenv && virtualenvwrapp
   ```

2. **配置环境变量：**

   ```shell
   export WORKON_HOME=$HOME/.virtualenvs
   source $(which virtualenvwrapper.sh)
   export VIRTUALENVWRAPPER_PYTHON=$(which python)
   ```

3. **使用方法**

   ```shell
   mkvirtualenv --python=python3.6 WORK  # 创建虚拟环境
   workon       	 												# 列出所有虚拟环境
   workon WORK														# 进入虚拟环境
   deactivate														# 退出虚拟环境
   rmvirtualenv WORK                     # 删除虚拟环境
   ```

## 迭代器，可迭代对象和生成器

**Iterable**

实现了`__iter__()`方法的对象，都是都是可迭代的（尽管实现错误），`__iter()__`应返回一个实现了`__next()__`的对象，可以使用`isinstance()`进行检查一个对象是否是可迭代的。

1. 集合或序列类型
2. 文件对象
3. 在类中定义了`__iter()__`方法的对象，但是不保证正确性。想要在`for`循环中正确使用，就需要保证`__iter()__`实现正确（即可以通过内置`iter()`函数转成`Iterator`对象）。
4. `for`循环通过`iter()`将对象转化成迭代器，从而调用`__next__()`进行遍历。能够for循环不一定是可迭代对象，也可能是实现了`__getitem__()`方法。

**Iterator**

一个对象实现了`__iter__()`和`__next__()`方法，那么它就是一个迭代器对象。

1. 常见的集合和序列对象都是可迭代的，但是它们并不是迭代器。
2. 文件对象是迭代器
3. 一个迭代器对象不仅可以在`for`循环中使用，同样可以通过内置函数`next()`函数进行调用。

**Generator**

一个生成器同时满足`Iterable`和`Iterator`两个条件。

1. 列表生成式
2. 使用`yeild`关键字定义生成器函数

**for循环原理**

`for循环`优先调用`iter()`函数，利用对象的`__iter__()`返回一个实现了`__next__()`函数的对象，从而逐步获取元素，这里的`__iter__()`只会调用一次。如果没有实现`__iter__()`函数，则直接调用`__getitem__()`函数来获取元素（调用多次）。可以通过将`__iter__=None`，设置对象不可迭代（不会调用`__getitem__`）

**使用in判断元素是否存在**

优先调用`__contains__`函数来判断元素是否存在，如果不存在，但是对象是可迭代的则迭代查找（效率低下）。

## super()深入理解

**以下代码：**

```python
class Base(object):
    def __init__(self):
        print('enter base')
        print('leave base')

class A(Base):
    def __init__(self):
        print('enter A')
        super(A, self).__init__()
        print('leave A')

class B(Base):
    def __init__(self):
        print('enter B')
        super(B, self).__init__()
        print('leave B')

class C(A, B):
    def __init__(self):
        print('enter C')
        super(C, self).__init__()
        print('leave C')

c = C()
print(C.mro())
```

**运行结果：**

```shell
enter C
enter A
enter B
enter base
leave base
leave B
leave A
leave C
[<class '__main__.C'>, <class '__main__.A'>, <class '__main__.B'>, <class '__main__.Base'>, <class 'object'>]
```

**结果分析：**

`super()`函数通常被认为是获取父类的时候使用，然而实际上`super()`和父类并没有必然关联。`super(cls, self)`做了两件事：

- 获取`self`实例的MRO列表
- 查找`cls`在MRO列表中的位置，并返回下一个位置类

此外，当调用`super()`的时候，`self`仍旧指代的是子类对象，而不是父类对象。

由此可知，当代码执行了`super(A, self).__init__()`之后，紧接着就执行了`super(B, self).__init__()`而没有执行`A`类的初始化函数，这和`C`的MRO列表是直接相关的（因为整个过程中`self`没有发生改变，始终获取的都是`C`的MRO列表）。

## Method Resolution Order

### 旧式类MRO算法

```python
class A:
    def method(self):
      	print("CommonA")
    
class B(A):
    pass

class C(A):
    def method(self):
    	print("CommonC")
    
class D(B, C):
    pass

print(D().method())
```

分析上述代码，可以得出4个类是一个**菱形**继承的关系，当使用`D`类对象访问`method()`方法时，根据深度优先算法，搜索顺序为：`D > B > A > C > A`，因此使用旧MRO算法最先得到的是基类`A`中的`method()`方法，这个结果显然不是我们想要的，我们想要的是`C`类中的`method()`方法。

### 新式类MRO算法

为解决旧式MRO算法存在的问题，Python 2.2 版本推出了新的计算新式类MRO的方法，它仍然采用从左至右的深度优先遍历，但是如果遍历中出现重复的类，只保留最后一个。

仍以上面程序为例，通过深度优先遍历，其搜索顺序为`D->B->A->C->A`，由于此顺序中有2个A，因此仅保留后一个，简化后得到最终的搜索顺序为`D->B->C->A`。新式类可以直接通过 `类名.__mro__` 的方式获取类的 MRO列表，也可以通过 `类名.mro() `的形式。

这种MRO解决了菱形继承的问题，但是可能会违反单调性原则。

> 单调性原则：在类存在多继承时，子类不能改变基类的MRO搜索顺序，否则会导致程序发生异常。

有如下程序：

```python
class X(object): pass
class Y(object): pass

class A(X,Y): pass
class B(Y,X): pass

class C(A,B): pass
```

通过进行深度遍历，得到搜索顺序为 `C->A->X->object->Y->object->B->Y->object->X->object`，再进行简化（相同取后者），得到 `C->A->B->Y->X->object`。

- 对于 A，其搜索顺序为 A->X->Y->object；
- 对于 B，其搜索顺序为 B->Y->X->object；
- 对于 C，其搜索顺序为 C->A->B->X->Y->object。

可以看到，在B和C中，X、Y 的搜索顺序是相反。在继承过程中C类改变了B类的搜索顺序，这违反了单调性原则。

### MRO C3

为解决 Python 2.2 中 MRO 所存在的问题，Python 2.3 采用了 C3 方法来确定方法解析顺序。同样用以上程序做示例：

> 类 A：L[A] = merge(A , object)
>
> 类 B：L[B] = [B] + merge(L[A] , [A])
>
> 类 C：L[C] = [C] + merge(L[A] , [A])
>
> 类 D：L[D] = [D] + merge(L[A] , L[B] , [A] , [B])

`merge`的运算方式如下：

1. 检查第一个列表的头元素，记作H。
2. 若H未出现在merge中其它列表的尾部，则将其输出，并将其从所有列表中删除，然后回到步骤1。否则，取出下一个列表的头部记作H，继续该步骤。

重复上述步骤，直到列表为空或者不能再找出可以输出的元素，如果是前一种情况，则算法结束；如果是后一种情况，Python会抛出异常。

由此，可以计算出类 B 的 MRO，其计算过程为：

```shell
L[B] = [B] + merge(L[A],[A])
     = [B] + merge([A,object],[A])
     = [B,A] + merge([object])
     = [B,A,object]
```

## 反射机制

反射就是通过字符串的形式，导入模块；通过字符串的形式，去模块寻找指定函数，并执行。利用字符串的形式去对象（模块）中操作（查找/获取/删除/添加）成员，一种基于字符串的事件驱动！

### 四个内置函数

1. **getattr(object, name[, default])**

   ```python
   object  -- 对象
   name    -- 字符串，对象属性
   default -- 默认返回值
   ```

2. **hasattr(object, name)**

   ```python
   object -- 对象。
   name   -- 字符串，对象属性
   ```

3. **setattr(object, name, value)**

   ```python
   object -- 对象
   name   -- 字符串，对象属性
   value  -- 属性值
   ```

4. **delattr(object, name)**

   ```python
   object -- 对象
   name   -- 字符串，对象属性
   ```

### 动态导包

```python
__import__("test_handler")
```

## Code 技巧

1. 返回出现次数最多的元素

   ```python
   my_list = [1, 2, 3, 1, 1, 4, 2, 1]                                                                                                                                                                                                                                                        
   most_frequent = max(set(my_list), key=my_list.count)
   ```

2. 合并处理list

   ```python
   number1 = [1, 2, 3]                                                                                                                                                                                                                                                                       
   number2 = ["one", "two", "three"]                                                                                                                                                                                                                                                         
   for chinese, english in zip(number1, number2):                                                                                                                                                                                                                                            
       print(chinese, english)
   ```

## threading模块

### setDaemon && join && 线程退出问题

### 创建线程 && threading.Thread()参数

### timer

#### 内存泄漏

objgraph

#### 原理分析，递归会不会导致线程清理不干净

子线程先于孙子线程退出 子线程的资源会不会被回收，何时回收

## 垃圾回收机制

## 以守护进程的方式运行

### 重定向问题



## logging模块

## 单例模式python版本

## requests

源码阅读

## asyncio

### 子线程中使用协程get_event_loop()获取不到loop 的原因

### 同一个线程重复创建loop?

## 统计程序用时

Time.clock()

## 装饰器

```
@functools.wraps(func)
```

## python 版本

## pytest单元测试

### 用例规则

1. 模块名必须以`test_`开头或者`_test`结尾
2. 测试类必须以`Test`开头，并且不能有`init`方法
3. 测试方法必须以`test`开头

### 运行方式

1. 主函数模式

2. 命令行模式

   ```shell
   -v         详细信息
   -s         显示打印信息
   -n 2       指定多线程运行
   -x         一个测试用例失败则终止测试
   --reruns 2 失败重跑
   ```

3. 通过读取`pytest.ini`配置文件运行

## 异常处理

常见的异常

traceback模块
自定义异常类

## requests