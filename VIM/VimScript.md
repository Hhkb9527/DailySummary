# 变量

```shell
let     # 对变量进行初始化或者赋值
unlet   # 删除一个变量
unlet!  # 删除变量，但是会忽略诸如变量不存在的错误提示

g:var # - 全局,所有脚本文件公用
s:var # - 局部,当前脚本内可见
a:var # - 函数参数
l:var # - 函数局部变量
b:var # - buffer 局部变量
w:var # - window 局部变量
t:var # - tab 局部变量
v:var # - Vim 预定义的内部变量

# 可以通过 $name 的模式读取或者改写环境变量,用 &option 的方式来读写 vim 内部的设置值
```