# 动态规划
## 背景
先从一条题目开始，[三角形最小路径和](https://leetcode.cn/problems/triangle/):
```
给定一个三角形 triangle ，找出自顶向下的最小路径和。
每一步只能移动到下一行中相邻的结点上。相邻的结点 在这里指的是 下标 与 上一层结点下标 相同或者等于 上一层结点下标 + 1 的两个结点。也就是说，如果正位于当前行的下标 i ，那么下一步可以移动到下一行的下标 i 或 i + 1 。
```

```cpp
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = triangle.size();
        vector<vector<int>> dp(n, vector<int> (n));
        dp[0][0] = triangle[0][0];
        for (int i = 1; i < n; ++i){
            dp[i][0] = dp[i - 1][0] + triangle[i][0];
            for (int j = 1; j < i; ++j){
                dp[i][j] = min(dp[i - 1][j - 1], dp[i - 1][j]) + triangle[i][j];
            }
            dp[i][i] = dp[i - 1][i - 1] + triangle[i][i];
        }
        return *min_element(dp[n - 1].begin(), dp[n - 1].end());
    }
};
```
- 空间优化
```cpp
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = triangle.size();
        vector<int> dp(n), temp(n);
        dp[0] = triangle[0][0];
        for (int i = 1; i < n; ++i){
            temp[0] = dp[0] + triangle[i][0];
            for (int j = 1; j < i; ++j){
                temp[j] = min(dp[j - 1], dp[j]) + triangle[i][j];
            }
            temp[i] = dp[i - 1] + triangle[i][i];
            dp = temp;
        }
        return *min_element(dp.begin(), dp.end());
    }
};
```

**动态规划就是把大问题变成小问题，并解决了小问题重复计算的方法。**
## 使用场景
- 满足以下条件之一：
  - 求最大的或最小值
  - 求是否可行
  - 求可行解的个数
- 满足不能排序或交换，比如[最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/)位置可以交换，便不能使用动态规划。

## 四点要素
1. 状态(state)：存储小规模问题的结果；
2. 方程(function)：状态之间的转移，怎么通过小规模的状态来计算大规模的状态；
3. 初始化(intialization)：最极限的小状态的定义，起点；
4. 答案(answer)：最大的状态，终点；

## 常见的四种类型
1. Matrix DP(10%);
2. Sequence(40%);
3. Two Sequence DP(40%);
4. Backpack(10%);

## Matrix DP(10%)

### [最小路径和](https://leetcode.cn/problems/minimum-path-sum/)
```
给定一个包含非负整数的 m x n 网格 grid ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。
```
```cpp
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> dp(m, vector<int> (n));
        dp[0][0] = grid[0][0];
        for (int i = 1; i < m; ++i){
            dp[i][0] = dp[i - 1][0] + grid[i][0];
        }
        for (int i = 1; i < n; ++i){
            dp[0][i] = dp[0][i - 1] + grid[0][i];
        }
        for (int i = 1; i < m; ++i){
            for (int j = 1; j < n; ++j){
                dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];
            }
        }
        return dp[m - 1][n - 1];
    }
};
```
- 空间优化，二维dp数组转一维dp数组
```cpp
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<int> dp(n);
        dp[0] = grid[0][0];
        for (int i = 1; i < n; ++i){
            dp[i] = dp[i - 1] + grid[0][i];
        }
        for (int i = 1; i < m; ++i){
            dp[0] += grid[i][0];
            for (int j = 1; j < n; ++j){
                dp[j] = min(dp[j - 1], dp[j]) + grid[i][j];
            }
        }
        return dp[n - 1];
    }
};
```

### [不同路径1](https://leetcode.cn/problems/unique-paths/)
```
一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。
机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。
问总共有多少条不同的路径？
```
- 空间优化版本
```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<int> dp(n, 1);
        for (int i = 1; i < m; ++i){
            for (int j = 1; j < n; ++j){
                dp[j] += dp[j - 1];
            }
        }
        return dp[n - 1];
    }
};
```

### [不同路径2](https://leetcode.cn/problems/unique-paths-ii/)
```
一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。
机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish”）。
现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？
网格中的障碍物和空位置分别用 1 和 0 来表示。
```
- 思路：空间优化版，且第一行初始化时，如果遇到了障碍，那么右边的均初始化为0，到达不了。
```cpp
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size(), n = obstacleGrid[0].size();
        vector<int> dp(n, 0);
        for (int i = 0; i < n; ++i){
            if (obstacleGrid[0][i] == 0){
                dp[i] = 1;
            }else{
                break;
            }
        }
        for (int i = 1; i < m; ++i){
            if (obstacleGrid[i][0] == 1){
                dp[0] = 0;
            }
            for (int j = 1; j < n; ++j){
                if (obstacleGrid[i][j] == 1){
                    dp[j] = 0;
                }else{
                    dp[j] += dp[j - 1];
                }
            }
        }
        return dp[n - 1];
    }
};
```

### [最大正方形](https://leetcode.cn/problems/maximal-square/)
```
在一个由 '0' 和 '1' 组成的二维矩阵内，找到只包含 '1' 的最大正方形，并返回其面积。
```
- 思路：状态转移方程为：$dp[i][j] = min(dp[i - 1][j - 1], dp[i - 1][j], dp[i][j - 1]) + 1$
```cpp
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        int m = matrix.size(), n = matrix[0].size(), ans = 0;
        vector<vector<int>> dp(m, vector<int> (n));
        for (int i = 0; i < m; ++i){
            for (int j = 0; j < n; ++j){
                if (matrix[i][j] == '1'){
                    if (i == 0 || j == 0){
                        dp[i][j] = 1;
                    }else{
                        dp[i][j] = min(min(dp[i - 1][j - 1], dp[i][j - 1]), dp[i - 1][j]) + 1;
                    }
                    ans = max(ans, dp[i][j]);
                }
            }
        }
        return ans * ans;
    }
};
```

## Sequence(40%)
这一类的一部分题目中，初始化时需要n+1的dp数组。
### [爬楼梯](https://leetcode.cn/problems/climbing-stairs/)
```
假设你正在爬楼梯。需要 n 阶你才能到达楼顶。
每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？
```
```cpp
class Solution {
public:
    int climbStairs(int n) {
        if (n == 1) {return 1;}
        if (n == 2) {return 2;}
        vector<int> dp(n + 1);
        dp[1] = 1;
        dp[2] = 2;
        for (int i = 3; i <= n; ++i){
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
};
```

### [跳跃游戏1](https://leetcode.cn/problems/jump-game/)
```
给定一个非负整数数组 nums ，你最初位于数组的 第一个下标 。
数组中的每个元素代表你在该位置可以跳跃的最大长度。
判断你是否能够到达最后一个下标。
```
- 思路：维护一个变量，当前能到达的最右端下标
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int max_index = 0;
        for (int i = 0; i < nums.size(); ++i){
            if (i > max_index) {return false;}
            max_index = max(max_index, i + nums[i]);
            if (max_index >= nums.size() - 1) {return true;}
        }
        return true;
    }
};
```

### [跳跃游戏2](https://leetcode.cn/problems/jump-game-ii/)
```
给你一个非负整数数组 nums ，你最初位于数组的第一个位置。
数组中的每个元素代表你在该位置可以跳跃的最大长度。
你的目标是使用最少的跳跃次数到达数组的最后一个位置。
假设你总是可以到达数组的最后一个位置。
```
- 思路1：维护一个dp数组，遍历每一个位置i，再遍历所有能从位置i跳到的位置，若跳跃次数更少，则更新
```cpp
class Solution {
public:
    int jump(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n, INT_MAX);
        dp[0] = 0;
        for (int i = 0; i < n; ++i){
            for (int j = 1; j <= nums[i]; ++j){
                if (i + j < n){
                    dp[i + j] = min(dp[i] + 1, dp[i + j]);
                }
            }
        }
        return dp[n - 1];
    }
};
```
- 思路2：维护三个变量，cur_max代表当前能到达的最右端下标，step_max代表当前步数能到达的最右端下标，step代表当前步数。
```cpp
class Solution {
public:
    int jump(vector<int>& nums) {
        int cur_max = 0, step_max = 0, step = 0;
        for (int i = 0; i < nums.size(); ++i){
            if (cur_max < i) {return INT_MAX;} // 无法到达
            if (step_max < i){
                // 若当前步数无法到达位置i，则多跳一次
                ++step;
                step_max = cur_max;
            }
            cur_max = max(cur_max, i + nums[i]);
        }
        return step;
    }
};
```

### [分割回文串2](https://leetcode.cn/problems/palindrome-partitioning-ii/)
```
给你一个字符串 s，请你将 s 分割成一些子串，使每个子串都是回文。
返回符合要求的 最少分割次数 。
```
- 思路：两次DP。第一次DP先求出任意子串`s[i...j]`是否为回文串。第二次DP的转移方程为：当`s[0...i]`是回文串时，`f[i] = 0`，若不是，则$f[i]=\min _{0 \leq j<i}\{f[j]\}+1\}$，其中`s[j+1...i]`是回文串。
```cpp
class Solution {
public:
    int minCut(string s) {
        int n = s.size();
        vector<vector<int>> g(n, vector<int>(n, true));
        for (int i = n - 1; i >= 0; --i){
            for (int j = i + 1; j < n; ++j){
                g[i][j] = (s[i] == s[j]) && g[i + 1][j - 1];
            }
        }
        vector<int> f(n, INT_MAX);
        for (int i = 0; i < n; ++i){
            if (g[0][i]){
                f[i] = 0;
            }else{
                for (int j = 0; j < i; ++j){
                    if (g[j + 1][i]){
                        f[i] = min(f[i], f[j] + 1);
                    }
                }
            }
        }
        return f[n - 1];
    }
};
```

### [分割回文串3](https://leetcode.cn/problems/palindrome-partitioning-iii/)
```
给你一个由小写字母组成的字符串 s，和一个整数 k。
请你按下面的要求分割字符串：
首先，你可以将 s 中的部分字符修改为其他的小写英文字母。
接着，你需要把 s 分割成 k 个非空且不相交的子串，并且每个子串都是回文串。
请返回以这种方式分割字符串所需修改的最少字符数。
```
- 思路1：转移方程为：$f[i][j] = min(f[pos][j - 1] + cost(S, i0 + 1, i))$，其中pos为从j-1开始枚举的第j个回文字符串的左边界。时间复杂度：$O(N^3K)$，空间复杂度：$O(NK)$。
```cpp
class Solution {
public:
    int cost(string& s, int left, int right){
        int ret = 0;
        for (int i = left, j = right; i < j; ++i, --j){
            if (s[i] != s[j]){
                ++ret;
            }
        }
        return ret;
    }

    int palindromePartition(string s, int k) {
        int n = s.size();
        vector<vector<int>> f(n + 1, vector<int> (k + 1, INT_MAX));
        f[0][0] = 0;
        for (int i = 1; i <= n; ++i){
            for (int j = 1; j <= min(k, i); ++j){
                if (j == 1){
                    f[i][j] = cost(s, 0, i - 1);
                }else{
                    for (int pos = j - 1; pos < i; ++pos){
                        f[i][j] = min(f[i][j], f[pos][j - 1] + cost(s, pos, i - 1));
                    }
                }
            }
        }
        return f[n][k];
    }
};
```
- 思路2：上述代码中的cost函数其实也可以提前采用DP将每一个`cost[i, j]`求出来。这样的话，时间复杂度即为$O(N^2K)$，空间复杂度为$O(N^2 + NK)$。
```cpp
class Solution {
public:
    int palindromePartition(string s, int k) {
        int n = s.size();

        vector<vector<int>> cost(n, vector<int> (n));
        for (int span = 2; span <= n; ++span){
            for (int i = 0; i <= n - span; ++i){
                int j = i + span - 1;
                cost[i][j] = cost[i + 1][j - 1] + (s[i] == s[j] ? 0 : 1);
            }
        }

        vector<vector<int>> f(n + 1, vector<int> (k + 1, INT_MAX));
        f[0][0] = 0;
        for (int i = 1; i <= n; ++i){
            for (int j = 1; j <= min(k, i); ++j){
                if (j == 1){
                    f[i][j] = cost[0][i - 1];
                }else{
                    for (int pos = j - 1; pos < i; ++pos){
                        f[i][j] = min(f[i][j], f[pos][j - 1] + cost[pos][i - 1]);
                    }
                }
            }
        }
        return f[n][k];
    }
};
```

### [最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)
```
给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。
```
- 思路1：动态规划，时间复杂度：$O(N^2)$，空间复杂度：$O(N)$。转移方程为：$d p[i]=\max (d p[j])+1$, 其中 $0 \leq j<i$ 且 $n u m[j]<n u m[i]$
```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        int ans = 1;
        vector<int> dp(n, 1);
        for (int i = 1; i < n; ++i){
            for (int j = 0; j < i; ++j){
                if (nums[i] > nums[j]){
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
            ans = max(dp[i], ans);
        }
        return ans;
    }
};
```
- 思路2：贪心+二分查找，时间复杂度：$O(NlogN)$，空间复杂度：$O(N)$。维护`d[i]`为，长度为i的严格递增子序列的最后一个元素最小可能为多少。从前往后遍历数组`nums`时，如果nums $[i]>d[$ len $]$，则直接加入数组d的末尾，最长严格递增子序列长度加1；否则在数组d中寻找第一个比`nums[i]`小的数字`d[k]`，并更新$d[k+1]=n u m s[i]$。
```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size(), ans = 1;
        if (n == 0){
            return 0;
        }
        vector<int> d(n + 1, 0);
        d[ans] = nums[0];
        for (int i = 0; i < n; ++i){
            if (nums[i] > d[ans]){
                d[++ans] = nums[i];
            }else{
                int left = 1, right = ans; // 二分搜索
                while (left < right){
                    int mid = left + ((right - left) >> 1);
                    if (d[mid] < nums[i]){
                        left = mid + 1;
                    }else{
                        right = mid;
                    }
                }
                d[left] = nums[i];
                // 上述的二分搜索可以采用C++中二分查找内置函数
                // int pos = lower_bound(d.begin() + 1, d.begin() + ans + 1, nums[i]) - d.begin();
                // d[pos] = nums[i];
            }
        }
        return ans;
    }
};
```

### [单词拆分](https://leetcode.cn/problems/word-break/)
```
给你一个字符串 s 和一个字符串列表 wordDict 作为字典。请你判断是否可以利用字典中出现的单词拼接出 s 。
注意：不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。
```
- 思路：转移方程为：$d p[i]=d p[j] \& \& \operatorname{check}(s[j . . i-1])$，其中的check表示字符串`s[j...i - 1]`是否出现在字典中。并且如果分割点j到i的距离大于了字典中最长的单词的长度，则直接停止枚举（剪枝）。
```cpp
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> hs;
        int max_len = 1;
        for (auto word : wordDict){
            hs.insert(word);
            max_len = max((int)word.size(), max_len);
        }
        int n = s.size();
        vector<bool> dp(n + 1);
        dp[0] = true;
        for (int i = 1; i <= n; ++i){
            for (int j = i; j >= max(0, i - max_len); --j){
                if (dp[j] && hs.find(s.substr(j, i - j)) != hs.end()){
                    dp[i] = true;
                    break;
                }
            }
        }
        return dp[n];
    }
};
```

## Two Sequences(40%)

### [最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/)
```
给定两个字符串 text1 和 text2，返回这两个字符串的最长 公共子序列 的长度。如果不存在 公共子序列 ，返回 0 。
一个字符串的 子序列 是指这样一个新的字符串：它是由原字符串在不改变字符的相对顺序的情况下删除某些字符（也可以不删除任何字符）后组成的新字符串。
例如，"ace" 是 "abcde" 的子序列，但 "aec" 不是 "abcde" 的子序列。
```
- 思路1：转移方程为：$d p[i][j]= \begin{cases}d p[i-1][j-1]+1, & \text { text }[i-1]=\operatorname{text}_{2}[j-1] \\ \max (d p[i-1][j], d p[i][j-1]), & \text { text }_{1}[i-1] \neq \operatorname{text}_{2}[j-1]\end{cases}$
```cpp
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        if (text1.size() == 0 or text2.size() == 0) {return 0;}
        int n1 = text1.size(), n2 = text2.size();
        vector<vector<int>> dp(n1 + 1, vector<int> (n2 + 1));
        for (int i = 1; i <= n1; ++i){
            for (int j = 1; j <= n2; ++j){
                if (text1[i - 1] == text2[j - 1]){
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                }else{
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[n1][n2];
    }
};
```
- 思路2：空间优化版本
```cpp
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        if (text1.size() == 0 or text2.size() == 0) {return 0;}
        if (text1.size() < text2.size()) {swap(text1, text2);} // 这句可有可无
        int n1 = text1.size(), n2 = text2.size();
        vector<int> dp(n2 + 1), temp(n2 + 1);
        for (int i = 1; i <= n1; ++i){
            for (int j = 1; j <= n2; ++j){
                if (text1[i - 1] == text2[j - 1]){
                    temp[j] = dp[j - 1] + 1;
                }else{
                    temp[j] = max(dp[j], temp[j - 1]);
                }
            }
            dp = temp;
        }
        return dp[n2];
    }
};
```

### [编辑距离](https://leetcode.cn/problems/edit-distance/)
```
给你两个单词 word1 和 word2， 请返回将 word1 转换成 word2 所使用的最少操作数  。
你可以对一个单词进行如下三种操作：
插入一个字符
删除一个字符
替换一个字符
```
- 思路：转移方程，当word1的下标i和word2的下标j的字符不同时，$dp[i][j]=1+\min (dp[i][j-1], dp[i-1][j], dp[i-1][j-1])$；相同时，$dp[i][j] =1+\min (dp[i][j-1], dp[i-1][j], dp[i-1][j-1]-1)$。需要注意的是，相同的时候，`dp[i][j]`并不是直接相等于`dp[i - 1][j - 1]`，也是有可能从`dp[i][j - 1]`或`dp[i - 1][j]`转移而来的。
```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        int n1 = word1.size(), n2 = word2.size();
        vector<vector<int>> dp(n1 + 1, vector<int> (n2 + 1));
        for (int i = 1; i <= n1; ++i){
            dp[i][0] = i;
        }
        for (int i = 1; i <= n2; ++i){
            dp[0][i] = i;
        }
        for (int i = 1; i <= n1; ++i){
            for (int j = 1; j <= n2; ++j){
                if (word1[i - 1] == word2[j - 1]){
                    dp[i][j] = 1 + min(min(dp[i - 1][j - 1] - 1, dp[i][j - 1]), dp[i - 1][j]);
                }else{
                    dp[i][j] = 1 + min(min(dp[i - 1][j - 1], dp[i][j - 1]), dp[i - 1][j]);
                }
            }
        }
        return dp[n1][n2];
    }
};
```
- 思路2：空间优化版本
```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {、
        if (word1.size() < word2.size()) {swap(word1, word2);}
        int n1 = word1.size(), n2 = word2.size();
        vector<int> dp(n2 + 1), temp(n2 + 1);
        for (int i = 1; i <= n2; ++i){
            dp[i] = i;
        }
        for (int i = 1; i <= n1; ++i){
            temp[0] = i;
            for (int j = 1; j <= n2; ++j){
                if (word1[i - 1] == word2[j - 1]){
                    temp[j] = 1 + min(min(dp[j - 1] - 1, temp[j - 1]), dp[j]);
                }else{
                    temp[j] = 1 + min(min(dp[j - 1], temp[j - 1]), dp[j]);
                }
            }
            dp = temp;
        }
        return dp[n2];
    }
};
```

### [交错字符串](https://leetcode.cn/problems/interleaving-string/)
```
给定三个字符串 s1、s2、s3，请你帮忙验证 s3 是否是由 s1 和 s2 交错 组成的。
两个字符串 s 和 t 交错 的定义与过程如下，其中每个字符串都会被分割成若干 非空 子字符串：
s = s1 + s2 + ... + sn
t = t1 + t2 + ... + tm
|n - m| <= 1
交错 是 s1 + t1 + s2 + t2 + s3 + t3 + ... 或者 t1 + s1 + t2 + s2 + t3 + s3 + ...
注意：a + b 意味着字符串 a 和 b 连接。
```
- 思路1：转移方程为：$f(i, j)=\left[f(i-1, j)\right.$ and $\left.s_{1}(i-1)=s_{3}(p)\right]$ or $\left[f(i, j-1)\right.$ and $\left.s_{2}(j-1)=s_{3}(p)\right]$，其中$p = i + j - 1$，是s3中的下标
```cpp
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        int n1 = s1.size(), n2 = s2.size(), n3 = s3.size();
        if (n1 + n2 != n3) {return false;}
        vector<vector<int>> dp(n1 + 1, vector<int> (n2 + 1, false));
        dp[0][0] = true;
        for (int i = 0; i <= n1; ++i){
            for (int j = 0; j <= n2; ++j){
                int pos = i + j - 1;
                if (i){
                    dp[i][j] |= (dp[i - 1][j] && s1[i - 1] == s3[pos]);
                }
                if (j){
                    dp[i][j] |= (dp[i][j - 1] && s2[j - 1] == s3[pos]);
                }
            }
        }
        return dp[n1][n2];
    }
};
```
- 思路2：空间优化版本
```cpp
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        int n1 = s1.size(), n2 = s2.size(), n3 = s3.size();
        if (n1 + n2 != n3) {return false;}
        vector<int> dp(n2 + 1);
        dp[0] = 1;
        for (int i = 0; i <= n1; ++i){
            for (int j = 0; j <= n2; ++j){
                int pos = i + j - 1;
                if (i){
                    dp[j] &= (s1[i - 1] == s3[pos]);
                }
                if (j){
                    dp[j] |= (dp[j - 1] && s2[j - 1] == s3[pos]);
                }
            }
        }
        return dp[n2];
    }
};
```

### [不同的子序列](https://leetcode.cn/problems/distinct-subsequences/)
```
给定一个字符串 s 和一个字符串 t ，计算在 s 的子序列中 t 出现的个数。
字符串的一个 子序列 是指，通过删除一些（也可以不删除）字符且不干扰剩余字符相对位置所组成的新字符串。（例如，"ACE" 是 "ABCDE" 的一个子序列，而 "AEC" 不是）
```
- 思路：转移方程为：$d p[i][j]= \begin{cases}d p[i-1][j-1]+d p[i-1][j], & s[i - 1]=t[j - 1] \\ d p[i-1][j], & s[i - 1] \neq t[j - 1]\end{cases}$。
```cpp
class Solution {
public:
    using ull = unsigned long long;
    int numDistinct(string s, string t) {
        int n1 = s.size(), n2 = t.size();
        if (n1 < n2) {return 0;}
        vector<vector<ull>> dp(n1 + 1, vector<ull> (n2 + 1));
        for (int i = 0; i <= n1; ++i){
            dp[i][0] = 1;
        }
        for (int i = 1; i <= n1; ++i){
            for (int j = 1; j <= n2; ++j){
                if (s[i - 1] == t[j - 1]){
                    dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
                }else{
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return dp[n1][n2];
    }
};
```
- 思路2：空间优化版本，需要注意的是，第二层的j循环需要倒序。因为计算j的时候要用到旧的j-1的信息，所以要先更新j，再更新j-1。
```cpp
class Solution {
public:
    using ull = unsigned long long;
    int numDistinct(string s, string t) {
        int n1 = s.size(), n2 = t.size();
        if (n1 < n2) {return 0;}
        vector<ull> dp(n2 + 1);
        dp[0] = 1;
        for (int i = 1; i <= n1; ++i){
            dp[0] = 1;
            for (int j = n2; j >= 1; --j){
                if (s[i - 1] == t[j - 1]){
                    dp[j] += dp[j - 1];
                }
            }
        }
        return dp[n2];
    }
};
```

## Backpack(10%)

### [零钱兑换](https://leetcode.cn/problems/coin-change/)
```
给你一个整数数组 coins ，表示不同面额的硬币；以及一个整数 amount ，表示总金额。
计算并返回可以凑成总金额所需的 最少的硬币个数 。如果没有任何一种硬币组合能组成总金额，返回 -1 。
你可以认为每种硬币的数量是无限的。
```
- 思路：i代表钱数，j代表数组中的硬币。转移方程：$dp[i]=\min _{j=0 . . n-1} dp\left[i-c_{j}\right]+1$
```cpp
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount + 1, INT_MAX - 1);
        dp[0] = 0;
        for (int i = 1; i <= amount; ++i){
            for (int j = 0; j < coins.size(); ++j){
                if (i - coins[j] >= 0){
                    dp[i] = min(dp[i], dp[i - coins[j]] + 1);
                }
            }
        }
        return dp[amount] == INT_MAX - 1 ? -1 : dp[amount];
    }
};
```

### [零钱兑换2](https://leetcode.cn/problems/coin-change-2/)
```
给你一个整数数组 coins 表示不同面额的硬币，另给一个整数 amount 表示总金额。
请你计算并返回可以凑成总金额的硬币组合数。如果任何硬币组合都无法凑出总金额，返回 0 。
假设每一种面额的硬币有无限个。
```
- 思路1：假定`dp[i][j]`代表前i种硬币组成金额j的种类，那么，当不适用第i种硬币时，$dp[i][j] += dp[i-1][j]$，当使用时，$dp[i][j] += \sum_{k=1}^{\lfloor j / v a l\rfloor} dp[i-1][j-k * v a l], \text { val }=coin s[i-1]$
```cpp
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int n = coins.size();
        vector<vector<int>> dp(n + 1, vector<int> (amount + 1));
        dp[0][0] = 1;
        for (int i = 1; i <= n; ++i){
            int value = coins[i - 1];
            for (int j = 0; j <= amount; ++j){
                dp[i][j] = dp[i - 1][j];
                for (int k = 1; k * value <= j; k++){
                    dp[i][j] += dp[i - 1][j - k * value];
                }
            }
        }
        return dp[n][amount];
    }
};
```
- 思路2：空间优化版本
```cpp
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        vector<int> dp(amount + 1);
        dp[0] = 1;
        for (auto c : coins){
            for (int j = c; j <= amount; ++j){
                dp[j] += dp[j - c];
            }
        }
        return dp[amount];
    }
};
```


### [背包问题](https://www.lintcode.com/problem/92/description)
```
在 n 个物品中挑选若干物品装入背包，最多能装多满？假设背包的大小为m，每个物品的大小为Ai
（每个物品只能选择一次且物品大小均为正整数）
```
- 思路1：转移方程：i为第i个物品，j为背包大小。当$a[i-1]>j$时，肯定放不下，此时$dp[i][j] = dp[i-1][j]$；否则，可以选择放与不放两种情况，$dp[i][j] = max(dp[i-1][j], dp[i-1][j-a[i-1]] + a[i-1]$。
```cpp
class Solution {
public:
    int backPack(int m, vector<int> &a) {
        int n = a.size();
        vector<vector<int>> dp(n + 1, vector<int> (m + 1));
        for (int i = 1; i <= n; ++i){
            for (int j = 1; j <= m; ++j){
                if (a[i - 1] > j){
                    dp[i][j] = dp[i - 1][j];
                }else{
                    dp[i][j] = max(dp[i - 1][j], dp[i - 1][j - a[i - 1]] + a[i - 1]);
                }
            }
        }
        return dp[n][m];
    }
};
```
- 思路2：空间优化版本，注意倒序。
```cpp
class Solution {
public:
    int backPack(int m, vector<int> &a) {
        int n = a.size();
        vector<int> dp(m + 1);
        for (int i = 0; i < n; ++i){
            for (int j = m; j >= 1; --j){
                if (a[i] <= j){
                    dp[j] = max(dp[j], dp[j - a[i]] + a[i]);
                }
            }
        }
        return dp[m];
    }
};
```

### [背包问题2](https://www.lintcode.com/problem/125/description)
```
有 n 个物品和一个大小为 m 的背包. 给定数组 A 表示每个物品的大小和数组 V 表示每个物品的价值.
问最多能装入背包的总价值是多大?
```
- 思路1：和上题一样。
```cpp
class Solution {
public:
    int backPackII(int m, vector<int> &a, vector<int> &v) {
        int n = a.size();
        vector<vector<int>> dp(n + 1, vector<int> (m + 1));
        for (int i = 1; i <= n; ++i){
            for (int j = 1; j <= m; ++j){
                dp[i][j] = max(dp[i - 1][j], a[i - 1] > j ? 0 : dp[i - 1][j - a[i - 1]] + v[i - 1]);
            }
        }
        return dp[n][m];
    }
};
```
- 思路2：空间优化，注意倒序。
```cpp
class Solution {
public:
    int backPackII(int m, vector<int> &a, vector<int> &v) {
        vector<int> dp(m + 1);
        for (int i = 0; i < a.size(); ++i){
            for (int j = m; j >= 1; --j){
                dp[j] = a[i] > j ? dp[j] : max(dp[j], dp[j - a[i]] + v[i]);
            }
        }
        return dp[m];
    }
};
```

### [分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)
```
给你一个 只包含正整数 的 非空 数组 nums 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。
```
- 思路：即是否能从数组中选取若干个数字，其和为数组总和的一半。
```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = accumulate(nums.begin(), nums.end(), 0);
        if (sum % 2 == 1) {return false;}
        int target  = sum / 2;
        if (*max_element(nums.begin(), nums.end()) > target) {return false;}
        vector<int> dp(target + 1);
        dp[0] = 1;
        for (int i = 0; i < nums.size(); ++i){
            for (int j = target; j >= nums[i]; --j){
                dp[j] |= dp[j - nums[i]];
            }
        }
        return dp[target];
    }
};
```

### [目标和](https://leetcode.cn/problems/target-sum/)
```
给你一个整数数组 nums 和一个整数 target 。
向数组中的每个整数前添加 '+' 或 '-' ，然后串联起所有整数，可以构造一个 表达式 ：
例如，nums = [2, 1] ，可以在 2 之前添加 '+' ，在 1 之前添加 '-' ，然后串联起来得到表达式 "+2-1" 。
```
- 思路：讲题目转换为，数组中是否有若干个数的和为数组总和与target的差的一半。
```cpp
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int sum = accumulate(nums.begin(), nums.end(), 0);
        if (sum < target || (sum - target) % 2 == 1) {return 0;}
        int new_target = (sum - target) / 2;
        vector<int> dp(new_target + 1);
        dp[0] = 1;
        for (int i = 0; i < nums.size(); ++i){
            for (int j = new_target; j >= nums[i]; --j){
                dp[j] += dp[j - nums[i]];
            }
        }
        return dp[new_target];
    }
};
```

### [一和零](https://leetcode.cn/problems/ones-and-zeroes/)
```
给你一个二进制字符串数组 strs 和两个整数 m 和 n 。
请你找出并返回 strs 的最大子集的长度，该子集中 最多 有 m 个 0 和 n 个 1 。
```
- 思路：背包的容量有两种，需要三维动态规划。转移方程为：$d p[i][j][k]= \begin{cases}d p[i-1][j][k], & j<\text { zeros } \mid k<\text { ones } \\ \max (d p[i-1][j][k], d p[i-1][j-\text { zeros }][k-\text { ones }]+1), & j \geq \text { zeros } \& k \geq \text { ones }\end{cases}$
```cpp
class Solution {
public:
    vector<int> count_zero_and_one(string& s){
        vector<int> ans(2);
        int l = s.size();
        for (int i = 0; i < l; ++i){
            ans[s[i] - '0']++;
        }
        return ans;
    }

    int findMaxForm(vector<string>& strs, int m, int n) {
        int length = strs.size();
        vector<vector<vector<int>>> dp(length + 1, vector<vector<int>> (m + 1, vector<int> (n + 1)));
        for (int i = 1; i <= length; ++i){
            vector<int> ans = count_zero_and_one(strs[i - 1]);
            int zeros = ans[0], ones = ans[1];
            for (int j = 0; j <= m; ++j){
                for (int k = 0; k <= n; ++k){
                    dp[i][j][k] = dp[i - 1][j][k];
                    if (j >= zeros && k >= ones){
                        dp[i][j][k] = max(dp[i][j][k], dp[i - 1][j - zeros][k - ones] + 1);
                    }
                }
            }
        }
        return dp[length][m][n];
    }
};
```

### [组合总和4](https://leetcode.cn/problems/combination-sum-iv/)
```
给你一个由 不同 整数组成的数组 nums ，和一个目标整数 target 。请你从 nums 中找出并返回总和为 target 的元素组合的个数。
题目数据保证答案符合 32 位整数范围。请注意，顺序不同的序列被视作不同的组合。
```
- 思路：由于这题顺序不同的序列会被视为不同的组合，所以在循环的时候，对target的循环在外，对nums的循环在内。
```cpp
class Solution {
public:
    int combinationSum4(vector<int>& nums, int target) {
        vector<int> dp(target + 1);
        dp[0] = 1;
        for (int i = 1; i <= target; ++i){
            for (int j = 0; j < nums.size(); ++j){
                if (nums[j] <= i && dp[i] < INT_MAX - dp[i - nums[j]]){
                    dp[i] += dp[i - nums[j]];
                }
            }
        }
        return dp[target];
    }
};
```

## 补充

### [乘积最大子数组](https://leetcode.cn/problems/maximum-product-subarray/)
```
给你一个整数数组 nums ，请你找出数组中乘积最大的非空连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。
```
- 思路：因为数组中有负数，所以最大值也可能从最小值得来。
```cpp
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp_max(n), dp_min(n);
        dp_max[0] = nums[0];
        dp_min[0] = nums[0];
        for (int i = 1; i < n; ++i){
            dp_max[i] = max(dp_max[i - 1] * nums[i], max(nums[i], dp_min[i - 1] * nums[i]));
            dp_min[i] = min(dp_min[i - 1] * nums[i], min(nums[i], dp_max[i - 1] * nums[i]));
        }
        return *max_element(dp_max.begin(), dp_max.end());
    }
};
```

### [解码方法](https://leetcode.cn/problems/decode-ways/)
```
一条包含字母 A-Z 的消息通过以下映射进行了 编码 ：'A' -> "1",'B' -> "2",...,'Z' -> "26"。
要 解码 已编码的消息，所有数字必须基于上述映射的方法，反向映射回字母（可能有多种方法）。例如，"11106" 可以映射为：
"AAJF" ，将消息分组为 (1 1 10 6)
"KJF" ，将消息分组为 (11 10 6)
注意，消息不能分组为  (1 11 06) ，因为 "06" 不能映射为 "F" ，这是由于 "6" 和 "06" 在映射中并不等价。
给你一个只含数字的 非空 字符串 s ，请计算并返回 解码 方法的 总数 。
```
- 思路1：当i和i-1不在(0, 26]内时，$dp[i] = dp[i-1]$；否则，$dp[i] = dp[i-1] + dp[i-2]$。
```cpp
class Solution {
public:
    int numDecodings(string s) {
        int n = s.size();
        vector<int> dp(n + 1);
        dp[0] = 1;
        for (int i = 1; i <= n; ++i){
            if (s[i - 1] != '0'){
                dp[i] = dp[i - 1];
            }
            if (i > 1 && s[i - 2] != '0' && (s[i - 2] - '0') * 10 + s[i - 1] - '0' <= 26){
                dp[i] += dp[i - 2];
            }
        }
        return dp[n];
    }
};
```
- 思路2：空间优化版本，可以只采用三个变量
```cpp
class Solution {
public:
    int numDecodings(string s) {
        int n = s.size();
        // a = dp[i - 2], b = dp[i - 1], c = dp[i]
        int a = 0, b = 1, c;
        for (int i = 1; i <= n; ++i){
            c = 0;
            if (s[i - 1] != '0'){
                c += b;
            }
            if (i > 1 && s[i - 2] != '0' && (s[i - 2] - '0') * 10 + s[i - 1] - '0' <= 26){
                c += a;
            }
            a = b;
            b = c;
        }
        return c;
    }
};
```

### [最佳买卖股票时机含冷冻期](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/)
```
给定一个整数数组prices，其中第  prices[i] 表示第 i 天的股票价格 。​
设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:
卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。
注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
```
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int f0 = -prices[0];
        int f1 = 0;
        int f2 = 0;
        for (int i = 0; i < prices.size(); ++i){
            int new_f0 = max(f0, f2 - prices[i]);
            int new_f1 = f0 + prices[i];
            int new_f2 = max(f1, f2);
            f0 = new_f0;
            f1 = new_f1;
            f2 = new_f2;
        }
        return max(f1, f2);
    }
};
```

### [单词拆分2](https://leetcode.cn/problems/word-break-ii/)
```
给定一个字符串 s 和一个字符串字典 wordDict ，在字符串 s 中增加空格来构建一个句子，使得句子中所有的单词都在词典中。以任意顺序 返回所有这些可能的句子。
注意：词典中的同一个单词可能在分段中被重复使用多次。
```

```cpp
class Solution {
private:
    unordered_map<int, vector<string>> ans;
    unordered_set<string> wordset;

public:
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        wordset = unordered_set(wordDict.begin(), wordDict.end());
        backtrack(s, 0);
        return ans[0];
    }

    void backtrack(const string& s, int index){
        if (!ans.count(index)){
            if (index == s.size()){
                ans[index] = {""};
                return;
            }
            ans[index] = {};
            for (int i = index + 1; i <= s.size(); ++i){
                string word = s.substr(index, i - index);
                if (wordset.count(word)){
                    backtrack(s, i);
                    for (const string& ss:ans[i]){
                        ans[index].push_back(ss.empty() ? word : word + " " + ss);
                    }
                }
            }
        }
    }
};
```

### [戳气球](https://leetcode.cn/problems/burst-balloons/)
```
有 n 个气球，编号为0 到 n - 1，每个气球上都标有一个数字，这些数字存在数组 nums 中。
现在要求你戳破所有的气球。戳破第 i 个气球，你可以获得 nums[i - 1] * nums[i] * nums[i + 1] 枚硬币。 这里的 i - 1 和 i + 1 代表和 i 相邻的两个气球的序号。如果 i - 1或 i + 1 超出了数组的边界，那么就当它是一个数字为 1 的气球。
求所能获得硬币的最大数量。
```
- 思路：将过程倒过来，当做每次添加一个气球。令solve(i, j)表示开区间(i, j)内最大的硬币数。那么，当$i≥j-1$时，$solve(i, j)=0$；否则，$solve(i, j) = max(val[i] * val[mid] * val[j] + solve(i, mid) + solve(mid, j))$，mid为i+1到j-1的循环。
```cpp
class Solution {
public:
    vector<vector<int>> rec;
    vector<int> val;

public:
    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        val.resize(n + 2);
        for (int i = 1; i <= n; ++i){
            val[i] = nums[i - 1];
        }
        val[0] = val[n + 1] = 1;
        rec.resize(n + 2, vector<int> (n + 2, -1));
        return solve(0, n + 1);
    }

    int solve (int left, int right){
        if (left >= right - 1) {return 0;}
        if (rec[left][right] != -1) {return rec[left][right];}
        for (int i = left + 1; i < right; ++i){
            int sum = val[left] * val[i] * val[right];
            sum += solve(left, i) + solve(i, right);
            rec[left][right] = max(rec[left][right], sum);
        }
        return rec[left][right];
    }
};
```

# 题目总结

## Matrix
&#x2705;[三角形最小路径和](https://leetcode.cn/problems/triangle/)

&#x2705;[最小路径和](https://leetcode.cn/problems/minimum-path-sum/)

&#x2705;[不同路径1](https://leetcode.cn/problems/unique-paths/)

&#x2705;[不同路径2](https://leetcode.cn/problems/unique-paths-ii/)

&#x2705;[最大正方形](https://leetcode.cn/problems/maximal-square/)

## Sequence
&#x2705;[爬楼梯](https://leetcode.cn/problems/climbing-stairs/)

&#x2705;[跳跃游戏1](https://leetcode.cn/problems/jump-game/)

&#x2705;[跳跃游戏2](https://leetcode.cn/problems/jump-game-ii/)

&#x2705;[分割回文串2](https://leetcode.cn/problems/palindrome-partitioning-ii/)

&#x2705;[分割回文串3](https://leetcode.cn/problems/palindrome-partitioning-iii/)

&#x2705;[最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)

&#x2705;[单词拆分](https://leetcode.cn/problems/word-break/)

## Two Swquence
&#x2705;[最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/)

&#x2705;[编辑距离](https://leetcode.cn/problems/edit-distance/)

&#x2705;[交错字符串](https://leetcode.cn/problems/interleaving-string/)

&#x2705;[不同的子序列](https://leetcode.cn/problems/distinct-subsequences/)

## Backtrack
&#x2705;[零钱兑换](https://leetcode.cn/problems/coin-change/)

&#x2705;[零钱兑换2](https://leetcode.cn/problems/coin-change-2/)

&#x2705;[背包问题](https://www.lintcode.com/problem/92/description)

&#x2705;[背包问题2](https://www.lintcode.com/problem/125/description)

&#x2705;[分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)

&#x2705;[目标和](https://leetcode.cn/problems/target-sum/)

&#x2705;[一和零](https://leetcode.cn/problems/ones-and-zeroes/)

&#x2705;[组合总和4](https://leetcode.cn/problems/combination-sum-iv/)

## 补充
&#x2705;[乘积最大子数组](https://leetcode.cn/problems/maximum-product-subarray/)

&#x2705;[解码方法](https://leetcode.cn/problems/decode-ways/)

&#x2705;[最佳买卖股票时机含冷冻期](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

&#x2705;[单词拆分2](https://leetcode.cn/problems/word-break-ii/)

&#x2705;[戳气球](https://leetcode.cn/problems/burst-balloons/)