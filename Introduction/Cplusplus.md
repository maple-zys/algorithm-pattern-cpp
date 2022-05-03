# Cplusplus
C++编码笔记

## STL容器简介
| 类别 | 容器 | 底层数据结构 | 描述 | 头文件 | 
| ---- | ---- | ---- | ---- | ---- |
|序列容器| | | 描述线性的数据结构|
||向量(vector)|数组|元素连续存储、直接任意访问，从后部进行快速插入和删除操作| \<vector> |
||列表(list)|双向链表| 由节点组成的双向链表，在任意位置进行快速插入和删除操作|\<list>|
||双端队列(deque)|一个中央控制器和多个缓冲区|元素直接任意访问，从前部或后部进行快速插入和删除操作|\<deque>|
|关联容器|||描述非线性的元素，通常可以快速锁定其中的元素||
||集合(set)|红黑树|快速查找，无重复元素|\<set>|
||多重集合(multiset)|红黑树|快速查找，可重复元素|\<set>|
||映射(map)|红黑树|一对一映射，无重复元素，基于关键字快速查找|\<map>|
||多重映射(multimap)|红黑树|一对一映射，可有重复元素，基于关键字快速查找|\<map>|
||无序映射(unordered_map)|哈希表|一对一映射，无重复元素，无插入顺序，基于关键字快速查找|\<unordered_map>|
|容器适配器|||通过序列容器的部分功能得到||
||栈(stack)|一般用list或deque|后进先出的值的排列|\<stack>|
||队列(queue)|一般用list或deque|先进先出的值的排列|\<queue>|
||优先队列(priority_queue)|一般用vector实现|后进先出的值的排列|\<queue>|
|近容器|||||
||数组||C类型基于指针的数组||
||字符串||字符序列||
||bitset||存放标志位||
||valarry||进行高速向量运算||

## 易错点

### 1
无论是vector.size()，还是string.size()等等，返回的都是unsigned int型（64位机器是unsigned __int64）。以下面代码为例：
```cpp
for (int i = 0; i + b.size() < a.size() + 1; ++i) //ok
for (int i = 0; i < a.size() - b.size() + 1; ++i) //not ok
```
上述代码中，如果a.size()为3，b.size()为5，那么第二行的代码for循环中，右边的计算结果便是-1，然而.size()是无符号整型，它便会把-1转换成无符号整型，此时的终止条件就是“i < (unsigned __int64)(-1)”，即“i < 18446744073709551615”，循环便会出错。

所以说，设计到.size()时，尽量不要使得表达式的值为负。