https://wizardforcel.gitbooks.io/100-gdb-tips/content/

1. coredump 调试
2. gdb 调试多线程

```shell
# 可以执行“set print pretty on”命令，这样每行只会显示结构体的一名成员，而且还会根据成员的定义层次进行缩进
set print pretty on
```

#### 查看虚函数表

在 GDB 中还可以直接查看虚函数表，通过如下设置：`set print vtbl on`

之后执行如下命令查看虚函数表：`info vtbl 对象`或者`info vtbl 指针或引用所指向或绑定的对象`



#### 按照派生类打印对象

```
set print object on
```
