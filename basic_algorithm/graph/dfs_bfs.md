# 例题

## [腐烂的橘子](https://leetcode.cn/problems/rotting-oranges/)
```
在给定的 m x n 网格 grid 中，每个单元格可以有以下三个值之一：
值 0 代表空单元格；
值 1 代表新鲜橘子；
值 2 代表腐烂的橘子。
每分钟，腐烂的橘子 周围 4 个方向上相邻 的新鲜橘子都会腐烂。
返回 直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 -1 。
```
- 思路：以层为单位搜索，即搜索不带权的最短路径
```cpp
class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        int cnt = 0, ans = -1;
        queue<pair<int, int>> q;
        for (int i = 0; i < m; ++i){
            for (int j = 0; j < n; ++j){
                if (grid[i][j] == 1){
                    ++cnt;
                }else if (grid[i][j] == 2){
                    q.push(make_pair(i, j));
                }
            }
        }
        if (!cnt) {return 0;}
        int d_x[4] = {0, 1, 0, -1}, d_y[4] = {1, 0, -1, 0};
        while (!q.empty()){
            int size = q.size();
            ++ans;
            for (int k = 0; k < size; ++k){
                int x_cur = q.front().first, y_cur = q.front().second;
                q.pop();
                for (int i = 0; i < 4; ++i){
                    int x_new = x_cur + d_x[i], y_new = y_cur + d_y[i];
                    if (x_new < 0 || x_new >= m || y_new < 0 || y_new >= n || grid[x_new][y_new] != 1){
                        continue;
                    }
                    grid[x_new][y_new] = 2;
                    q.push(make_pair(x_new, y_new));
                    --cnt;
                }
            }
            
        }
        return cnt ? -1 : ans;
    }
};
```

