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

```