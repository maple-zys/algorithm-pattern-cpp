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