# 二进制
## 常见的二进制操作
### 基本操作
a = 0 ^ a = a ^ 0

0 = a ^ a

a = a ^ b ^ b

### 交换两个数
a = a ^ b

b = a ^ b

a = a ^ b

### 判断最后一个是否为1
a = n & 1

### 移除最后一个1
a = n & (n - 1)

### 获取最后一个1
diff = (n & (n - 1)) ^ n

# 常见题目
## [只出现一次的数字1](https://leetcode.cn/problems/single-number/)
```
给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。
```
- 思路：a = a ^ b ^ b
```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        for (int i = 1; i < nums.size(); ++i){
            nums[0] ^= nums[i];
        }
        return nums[0];
    }
};
```

## [只出现一次的数字2](https://leetcode.cn/problems/single-number-ii/)
```
给你一个整数数组 nums ，除某个元素仅出现 一次 外，其余每个元素都恰出现 三次 。请你找出并返回那个只出现了一次的元素。
```
- 思路：较为复杂，详情请见[大佬题解](https://leetcode.cn/problems/single-number-ii/solution/single-number-ii-mo-ni-san-jin-zhi-fa-by-jin407891/)。
```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ones = 0, twos = 0;
        for (auto& num : nums){
            ones = ones ^ num & ~twos;
            twos = twos ^ num & ~ones;
        }
        return ones;
    }
};
```

## [只出现一次的数字3](https://leetcode.cn/problems/single-number-iii/)
```
给定一个整数数组 nums，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。你可以按 任意顺序 返回答案。
```
- 思路：先将两个只出现过一次的数字分到两部分中，然后每一部分都采用第一题的解法
```cpp
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        int mask = 0;
        for (auto& n : nums) {mask ^= n;}
        int right = mask == INT_MIN ? INT_MIN : (mask & (mask - 1)) ^ mask; //防溢出
        int ans = 0;
        for (auto& n : nums){
            if (n & right) {ans ^= n;}
        }
        vector<int> rst = {ans, ans ^ mask};
        return rst;
    }
};
```

## [位1的个数](https://leetcode.cn/problems/number-of-1-bits/)
```
编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 '1' 的个数（也被称为汉明重量）。
```
- 思路：移除最后一个1，n & (n - 1),并计数
```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int count = 0;
        while (n > 0){
            ++count;
            n = n & (n - 1);
        }
        return count;
    }
};
```

## [比特位计数](https://leetcode.cn/problems/counting-bits/)
```
给你一个整数 n ，对于 0 <= i <= n 中的每个 i ，计算其二进制表示中 1 的个数 ，返回一个长度为 n + 1 的数组 ans 作为答案。
```
- 思路：最简答的想法便是重复n+1次上一题的解法，时间复杂度$O(nlogn)$。这里再写几种$O(n)$的动态规划。
```cpp
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> ans(n + 1);
        int highbit = 0;
        for (int i = 1; i < n + 1; ++i){
            if ((i & (i - 1)) == 0){
                highbit = i;
            }
            ans[i] = ans[i - highbit] + 1;
        }
        return ans;
    }
};
```

```cpp
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> ans(n + 1);
        for (int i = 1; i < n + 1; ++i){
            ans[i] = ans[i >> 1] + (i & 1);
        }
        return ans;
    }
};
```

```cpp
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> ans(n + 1);
        for (int i = 1; i < n + 1; ++i){
            ans[i] = ans[i & (i - 1)] + 1;
        }
        return ans;
    }
};
```