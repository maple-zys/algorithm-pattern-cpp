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

## 涨姿势

### 1
无论是vector.size()，还是string.size()等等，返回的都是unsigned int型（64位机器是unsigned __int64）。以下面代码为例：
```cpp
for (int i = 0; i + b.size() < a.size() + 1; ++i) //ok
for (int i = 0; i < a.size() - b.size() + 1; ++i) //not ok
```
上述代码中，如果a.size()为3，b.size()为5，那么第二行的代码for循环中，右边的计算结果便是-1，然而.size()是无符号整型，它便会把-1转换成无符号整型，此时的终止条件就是“i < (unsigned __int64)(-1)”，即“i < 18446744073709551615”，循环便会出错。

所以说，设计到.size()时，尽量不要使得表达式的值为负。

### 2
在C++11中，像vector这类的容器，都多了一个emplace_back()的函数。其功能与push_back()大致一样。不同的用法是，当容器中放入的是自定义的类，emplace_back()可以直接传入构造函数的参数，比如：
```cpp
class New {
    int i;
    string st;
public:
    New(int ii, string s):i(ii) , st(s) { }
    ~New( ) { }
};
 
int main( ) {
    vector<New> vec={ {21, "String"} , New{45 , "tinger"}};
  // 注意：传入的是一个对象 New(**, **)
    vec.push_back( New(34 , "Happy" ) ) ;        // Appending Test object
    vec.push_back( 901 , "Doer" ) ;              // Error!!
    vec.emplace_back( New(78 , "Gomu gomu" ) );  // workfine
    vec.emplace_back( 41 , "Shanks" ) ;          // work fine
}
```
性能比对：
- 通过构造函数像容器内插入对象（例如上述代码），emplace_back()更高效；
- 插入临时对象，二者相同，都调用移动构造函数；
- 插入对象示例，二者相同，都调用拷贝构造函数；

priority_queue、stack等中的push和emplace也是这样。

### 3
优先队列（priority_queue）自定义实现比较函数
1. struct重载运算符()
    ```cpp
    struct cmp{
        bool operator()(vector<int>&a,vector<int>&b){
            return a[0]>b[0]; 
        }
    };
    priority_queue<vector<int>,vector<vector<int>>,cmp> q;//小顶堆
    ```
    这属于**传入函数对象**的方式。
2. class重载运算符()
    ```cpp
    class cmp{
    public:
        bool operator()(vector<int>&a,vector<int>&b){
            return a[0]>b[0]; 
        }
    };
    priority_queue<vector<int>,vector<vector<int>>,cmp> q;//小顶堆
    ```
    这属于**传入函数对象**的方式。
3. 定义函数
    ```cpp
    bool cmp(vector<int>&a,vector<int>&b){
	return a[0]>b[0];
    }
    priority_queue<vector<int>,vector<vector<int>>,decltype(&cmp)> q(cmp);//小顶堆
    ```
    这是属于**传入函数指针**的方式。
    注意，**如果是类成员函数，cmp函数需加static关键字**。
4. lambda表达式
    ```cpp
    auto cmp=[](vector<int>&a,vector<int>&b)->bool{
            return a[0]>b[0];
        };
    priority_queue<vector<int>,vector<vector<int>>,decltype(cmp)> q(cmp);//小顶堆
    ```
    这是属于**传入函数指针**的方式。

### 4
max_element和min_element两个函数，用于返回容器或数组的最大值或最小值的迭代器。若要返回值，可采用*max_element和\*min_element，例如：
```cpp
vector<int> arr_1 = { 1, 34, 43, 2, 3463 };
int arr_2[5] = {1, 34, 43, 2, 3463};
cout << *max_element(arr_1.begin(), arr_1.end()) << endl;
cout << *max_element(arr_2, arr_2 + 5) << endl;;
```
上述代码，cout的两个数均为3463。