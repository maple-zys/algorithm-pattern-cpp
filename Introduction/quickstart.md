原项目中的md：（数据结构是一种数据的表现形式，如链表、二叉树、栈、队列等都是内存中一段数据表现的形式。 算法是一种通用的解决问题的模板或者思路，大部分数据结构都有一套通用的算法模板，所以掌握这些通用的算法模板即可解决各种算法问题。

后面会分专题讲解各种数据结构、基本的算法模板、和一些高级算法模板，每一个专题都有一些经典练习题，完成所有练习的题后，你对数据结构和算法会有新的收获和体会。

先介绍两个算法题，试试感觉~）

## [示例一](https://leetcode-cn.com/problems/implement-strstr/)
```
给定一个  haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从 0 开始)。如果不存在，则返回-1。
```
- 遍历haystack，再从遍历的当前位置判断，是否为needle。
```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (needle.size() == 0) {return 0;}
        int j;
        for (int i = 0; i + needle.size() < haystack.size() + 1; ++i){
            bool flag = true;
            for (int j = 0; j < needle.size(); ++j){
                if (haystack[i + j] != needle[j]){
                    flag = false;
                    break;
                }
            }
            if (flag){
                return i;
            }
        }
        return -1;
    }
};
```
注意：
- 判断时需使用i + needle.size() < haystack.size() + 1，不能用i < haystack.size() - needle.size() + 1，因为.size()返回的是无符号整型，所以当右边计算为负时，它会转换成一个很大的有符号整型。

## [示例二](https://leetcode-cn.com/problems/subsets/)
```
给你一个整数数组 nums ，数组中的元素 互不相同 。返回该数组所有可能的子集（幂集）。

解集 不能 包含重复的子集。你可以按 任意顺序 返回解集。
```
- 这是经典的回溯法。通过不停地选择、撤销选择来遍历所以情况，最后将满足条件的保存，然后返回结果。
```cpp
class Solution {
public:
    vector<vector<int>> res;

    void backtree(vector<int> nums, vector<int>& path, int start){
        res.push_back(path);
        for (int i = start; i < nums.size(); ++i){
            path.push_back(nums[i]);
            backtree(nums, path, i + 1);
            path.pop_back();
        }
    }

    vector<vector<int>> subsets(vector<int>& nums) {
        vector<int> path;
        backtree(nums, path, 0);
        return res;
    }
};
```