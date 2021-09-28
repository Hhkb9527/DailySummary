## 概念

通过hash算法计算出元素存储的位置，从而实现O(1)的时间复杂度插入/获取元素，常见的哈希函数：

1. 直接定制法：`Hash(Key)=A*Key+B`，利用线性函数计算散列地址
2. 除留余数法：设散列表中允许的地址数为m，取一个不大于m，但最接近或者等于m的质数p作为除数，按照哈希函数`Hash(key) = key%p`将关键码转换成哈希地址
3. 平方取中法
4. 折叠法
5. 随机数法
6. 数学分析法

## 解决哈希冲突

不同的元素经过哈希计算得到相同的哈希地址，这种现象称为哈希冲突或哈希碰撞，这样的元素称为"同义词"

### 1. 闭散列（开放地址法）

当发生哈希冲突的时候，将元素存储在冲突位置的下一个"空位置"中

1. 线性探测查找：发生冲突后依次先后寻找，直到找到一个空位置，但这样也会导致冲突数据会堆积在一起，搜索效率降低
2. 二次探测：`Hi = (H0 + i^2) % m`或`Hi = (H0 - i^2) % m`，H0是对元素进行哈希运算得到的结果

**注意：**采用闭散列解决哈希冲突时，不能直接物理删除哈希表中已有的元素，因为这样会给查找带来麻烦，因此线性探测采用标记的伪删除法来删除一个元素

```cpp
// 哈希表每个空间给个标记
// EMPTY标记此位置空， EXIST标记此位置已经有元素， DELETE标记元素已经删除
enum State{EMPTY, EXIST, DELETE}
```

**哈希负载因子：**随着哈希表中的数据增多，出现哈希冲突的概率也就会越大，因此我们为了减少哈希冲突应该保持哈希表中的有效元素少于总容量的70%左右，`0.7`就是哈希负载因子

### 2. 开散列（链式地址法）

将发生哈希冲突的所有元素装入一个桶中，桶中的所有元素通过链表连接，桶的头结点则存储在哈希表中

极端情况下，一个桶下的元素可能非常多，导致搜索效率降低，最好的情况是一个桶下只有一个元素，当元素个数等于桶个数的时候考虑扩容

### 3. 再哈希

当发生哈希冲突后，更换哈希函数重新计算地址，直到不发生哈希冲突为止，这种方式不易产生元素聚集，但是会增加计算量

### 4. 建立溢出区

当发生哈希冲突时，将所有冲突数据放入溢出区

### 5. 多阶哈希

TODO