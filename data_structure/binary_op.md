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

## [颠倒二进制位](https://leetcode.cn/problems/reverse-bits/)
```
颠倒给定的 32 位无符号整数的二进制位。
```
- 思路1：循环，n右移
```cpp
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        uint32_t res = 0;
        for (int i = 0; i < 32; ++i){
            res = (res << 1) | (n & 1);
            n >>= 1;
        }
        return res;
    }
};
```
- 思路2：分治
```cpp
class Solution {  
private:
    const uint32_t M1 = 0x55555555; // 01010101010101010101010101010101
    const uint32_t M2 = 0x33333333; // 00110011001100110011001100110011
    const uint32_t M4 = 0x0f0f0f0f; // 00001111000011110000111100001111
    const uint32_t M8 = 0x00ff00ff; // 00000000111111110000000011111111

public:
    uint32_t reverseBits(uint32_t n) {
        n = n >> 1 & M1 | (n & M1) << 1;
        n = n >> 2 & M2 | (n & M2) << 2;
        n = n >> 4 & M4 | (n & M4) << 4;
        n = n >> 8 & M8 | (n & M8) << 8;
        return n << 16 | n >> 16;
    }
};
```

## [数字范围按位与](https://leetcode.cn/problems/bitwise-and-of-numbers-range/)
```
给你两个整数 left 和 right ，表示区间 [left, right] ，返回此区间内所有数字 按位与 的结果（包含 left 、right 端点）。
```
- 思路：直接循环是不可取的，会TLE，这里每次将right的最后一个1置0，当right比left小时，right的左边一部分便是这些数的公共前缀，右边一部分是0，即是所求。
```cpp
class Solution {
public:
    int rangeBitwiseAnd(int left, int right) {
        while (left < right){
            right &= (right - 1);
        }
        return right;
    }
};
```

# 题目总结
&#x2705;[只出现一次的数字1](https://leetcode.cn/problems/single-number/)

&#x2705;[只出现一次的数字2](https://leetcode.cn/problems/single-number-ii/)

&#x2705;[只出现一次的数字3](https://leetcode.cn/problems/single-number-iii/)

&#x2705;[位1的个数](https://leetcode.cn/problems/number-of-1-bits/)

&#x2705;[比特位计数](https://leetcode.cn/problems/counting-bits/)

&#x2705;[颠倒二进制位](https://leetcode.cn/problems/reverse-bits/)

&#x2705;[数字范围按位与](https://leetcode.cn/problems/bitwise-and-of-numbers-range/)