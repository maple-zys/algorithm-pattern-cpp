# 图相关算法

图 (graph) 是一种相当复杂的数据结构，相关算法也多种多样，以下总结一些比较基础且常见的图算法。

以下是一些例题：

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

## [最短的桥](https://leetcode.cn/problems/shortest-bridge/)
```
在给定的二维二进制数组 A 中，存在两座岛。（岛是由四面相连的 1 形成的一个最大组。）
现在，我们可以将 0 变为 1，以使两座岛连接起来，变成一座岛。
返回必须翻转的 0 的最小数目。（可以保证答案至少是 1 。）
```
- 思路：先用dfs将某一座小岛全部加入bfs的队列中，然后进行多源bfs，求到达另一座岛的最短路径，按层bfs
```cpp
class Solution {
private:
    queue<pair<int, int>> q;
    vector<vector<int>> visited;
    int m, n;
    int d_x[4] = {0, 1, 0, -1};
    int d_y[4] = {1, 0, -1, 0};

public:
    void dfs(int x, int y, vector<vector<int>>& grid){
        if (x >= 0 && x < m && y >= 0 && y < n && !visited[x][y] && grid[x][y] == 1){
            visited[x][y] = 1;
            q.push({x, y});
            for (int i = 0; i < 4; ++i) {dfs(x + d_x[i], y + d_y[i], grid);}
        }
    }

    int shortestBridge(vector<vector<int>>& grid) {
        m = grid.size(), n = grid[0].size();
        int x_s = 0, y_s = 0;
        for (int i = 0; i < m; ++i){
            for (int j = 0; j < n; ++j){
                if (grid[i][j] == 1){
                    x_s = i;
                    y_s = j;
                    break;
                }
            }
        }
        vector<int> temp (n);
        visited.resize(m, temp);
        dfs(x_s, y_s, grid);
        int ans = -1;
        while (!q.empty()){
            ++ans;
            int size = q.size();
            for (int i = 0; i < size; ++i){
                int x = q.front().first, y = q.front().second;
                q.pop();
                for (int j = 0; j < 4; ++j){
                    int new_x = x + d_x[j], new_y = y + d_y[j];
                    if (new_x >= 0 && new_x < m && new_y >= 0 && new_y < n && !visited[new_x][new_y]){
                        if (grid[new_x][new_y] == 0){
                            visited[new_x][new_y] = 1;
                            q.push({new_x, new_y});
                        }else if (grid[new_x][new_y] == 1){
                            return ans;
                        }
                    }
                }
            }
        }
        return ans;
    }
};
```

## [滑动谜题](https://leetcode.cn/problems/sliding-puzzle/)
```
在一个 2 x 3 的板上（board）有 5 块砖瓦，用数字 1~5 来表示, 以及一块空缺用 0 来表示。一次 移动 定义为选择 0 与一个相邻的数字（上下左右）进行交换.
最终当板 board 的结果是 [[1,2,3],[4,5,0]] 谜板被解开。
给出一个谜板的初始状态 board ，返回最少可以通过多少次移动解开谜板，如果不能解开谜板，则返回 -1 。
```
- 思路1：广度优先，入队时入的是状态+步数
```cpp
class Solution {
private:
    vector<vector<int>> neighbors = {{1, 3}, {0, 2, 4}, {1, 5}, {0, 4}, {1, 3, 5}, {2, 4}}; //每个位置的邻居下标

public:
    int slidingPuzzle(vector<vector<int>>& board) {
        string initial;
        for (int i = 0; i < 2; ++i){
            for (int j = 0; j < 3; ++j){
                initial += char(board[i][j] + '0');
            }
        } //用字符串表示状态，方便set和queue的使用
        if (initial == "123450") {return 0;}
        queue<pair<string, int>> q;
        q.push({initial, 0});
        unordered_set<string> seen = {initial};
        auto get_next = [&] (string& status) -> vector<string> {
            vector<string> ret;
            int x = status.find('0');
            for (int y : neighbors[x]){
                swap(status[x], status[y]);
                ret.push_back(status);
                swap(status[x], status[y]);
            }
            return ret;
        }; //得到所有可能的下一个状态
        while (!q.empty()){ //bfs
            string status = q.front().first;
            int step = q.front().second;
            q.pop();
            for (auto && next_status: get_next(status)){
                if (!seen.count(next_status)){
                    if (next_status == "123450"){
                        return step + 1;
                    }
                    q.push({next_status, step + 1});
                    seen.insert(next_status);
                }
            }
        }
        return -1;
    }
};
```
- 思路2：A*算法，$F(x)=H(x)+G(x)$，其中，$G(x)$是步长，$H(x)$是每个点实际位置与期望位置的曼和顿距离的和。
```cpp
struct AStar{
    vector<vector<int>> mdist = {
        {0, 1, 2, 1, 2, 3},
        {1, 0, 1, 2, 1, 2},
        {2, 1, 0, 3, 2, 1},
        {1, 2, 3, 0, 1, 2},
        {2, 1, 2, 1, 0, 1},
        {3, 2, 1, 2, 1, 0}
    };

    int H(const string& status){
        int ans = 0;
        for (int i = 0; i < 6; ++i){
            if (status[i] != '0'){
                ans += mdist[i][status[i] - '1'];
            }
        }
        return ans;
    }

    AStar(const string& status, int g): status_{status}, g_{g}, h_{H(status)}{
        f_ = g_ + h_;
    }

    string status_;
    int f_, g_, h_;

    bool operator < (const AStar& a) const {
        return f_ > a.f_;
    }
};

class Solution {
private:
    vector<vector<int>> neighbors = {{1, 3}, {0, 2, 4}, {1, 5}, {0, 4}, {1, 3, 5}, {2, 4}};

public:
    int slidingPuzzle(vector<vector<int>>& board) {
        string initial;
        for (int i = 0; i < 2; ++i){
            for (int j = 0; j < 3; ++j){
                initial += char(board[i][j] + '0');
            }
        }
        if (initial == "123450") {return 0;}
        priority_queue<AStar> q;
        q.push({initial, 0});
        unordered_set<string> seen = {initial};
        auto get_next = [&] (string& status) -> vector<string> {
            vector<string> ret;
            int x = status.find('0');
            for (int y : neighbors[x]){
                swap(status[x], status[y]);
                ret.push_back(status);
                swap(status[x], status[y]);
            }
            return ret;
        };
        while(!q.empty()){
            AStar a = q.top();
            q.pop();
            for (auto&& next_status : get_next(a.status_)){
                if (!seen.count(next_status)){
                    if (next_status == "123450"){
                        return a.g_ + 1;
                    }
                    q.push({next_status, a.g_ + 1});
                    seen.insert(next_status);
                }
            }
        }
        return -1;
    }
};
```

## [网络延迟时间](https://leetcode.cn/problems/network-delay-time/)
```
有 n 个网络节点，标记为 1 到 n。
给你一个列表 times，表示信号经过 有向 边的传递时间。 times[i] = (ui, vi, wi)，其中 ui 是源节点，vi 是目标节点， wi 是一个信号从源节点传递到目标节点的时间。
现在，从某个节点 K 发出一个信号。需要多久才能使所有节点都收到信号？如果不能使所有节点收到信号，返回 -1 。
```
- 思路：求解单源最短路径问题可以用Dijkstra算法
```cpp
class Solution {
public:
    int networkDelayTime(vector<vector<int>>& times, int n, int k) {
        vector<vector<pair<int, int>>> edges(n + 1);
        for (int i = 0; i < times.size(); ++i){
            edges[times[i][0]].emplace_back(make_pair(times[i][2], times[i][1]));
        }
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> q;
        vector<int> dist(n + 1, INT_MAX);
        vector<bool> visited(n + 1, false);
        q.push(make_pair(0, k));
        dist[k] = 0;
        while (!q.empty()){
            int p = q.top().second;
            q.pop();
            if (visited[p]) {continue;}
            visited[p] = true;
            for (auto& e:edges[p]){
                int to = e.second;
                dist[to] = min(dist[to], dist[p] + e.first);
                if (!visited[to]) {q.push(make_pair(dist[to], to));}
            }
        }
        int ans = *max_element(dist.begin() + 1, dist.end());
        return ans == INT_MAX ? -1 : ans;
    }
};
```

## [K站中转内最便宜的航班](https://leetcode.cn/problems/cheapest-flights-within-k-stops/)
```
有 n 个城市通过一些航班连接。给你一个数组 flights ，其中 flights[i] = [fromi, toi, pricei] ，表示该航班都从城市 fromi 开始，以价格 pricei 抵达 toi。
现在给定所有的城市和航班，以及出发城市 src 和目的地 dst，你的任务是找到出一条最多经过 k 站中转的路线，使得从 src 到 dst 的 价格最便宜 ，并返回该价格。 如果不存在这样的路线，则输出 -1。
```
- 思路1：在标准的单源最短路径问题上限制了路径的边数，因此需要同时维护当前 SPT 内每个结点最短路径的边数，当遇到边数更小的路径 (边权和可以更大) 时结点需要重新入堆，以更新后继在边数上限内没达到的结点。
```cpp
class Solution {
public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        vector<vector<pair<int, int>>> edges(n);
        for (int i = 0; i < flights.size(); ++i) {
            edges[flights[i][0]].emplace_back(make_pair(flights[i][2], flights[i][1]));
        }
        unordered_map<int, int> prices, steps;
        auto cmp = [](const vector<int>& e1, const vector<int>& e2) {return e1[0] > e2[0]; };
        priority_queue<vector<int>, vector<vector<int>>, decltype(cmp)> q(cmp);
        q.push({ 0, 0, src });
        while (!q.empty()) {
            int price = q.top()[0], step = q.top()[1], node = q.top()[2];
            q.pop();
            if (node == dst) { return price; }
            if (prices.find(node) == prices.end()) { prices[node] = price; }
            steps[node] = step;
            if (step <= k) {
                ++step;
                for (auto& e : edges[node]) {
                    int nn = e.second, pp = e.first;
                    if ((prices.find(nn) == prices.end()) || step < steps[nn]) {
                        q.push({ pp + price, step, nn });
                    }
                }
            }
        }
        return -1;
    }
};
```
- 思路2：动态规划，用$f[t][i]$表示通过恰好t次航班，从出发城市src到城市i的最小花费。转移方程为：$f[t][i] = min(f[t-1][j]+cost(j,i)),(j,i) \in flights$。最终答案即为$f[1][dst],f[2][dst],..,f[k+1][dst]$的最小值。
```cpp
class Solution {
public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        vector<int> f(n, 101 * 10000 + 1);
        f[src] = 0;
        vector<int> new_f(n, 101 * 10000 + 1);
        for (int t = 1; t <= k + 1; ++t){
            for (auto&& flight : flights){
                int j = flight[0], i = flight[1], cost = flight[2];
                new_f[i] = min(new_f[i], f[j] + cost);
            }
            f = new_f;
        }
        int ans = 101 * 10000 + 1;
        for (int t = 1; t <= k + 1; ++t){
            ans = min(ans, f[dst]);
        }
        return ans == 101 * 10000 + 1 ? -1 : ans;
    }
};
```

## [单词接龙](https://leetcode.cn/problems/word-ladder/)
```
字典 wordList 中从单词 beginWord 和 endWord 的 转换序列 是一个按下述规格形成的序列 beginWord -> s1 -> s2 -> ... -> sk：
- 每一对相邻的单词只差一个字母。
- 对于 1 <= i <= k 时，每个 si 都在 wordList 中。注意， beginWord 不需要在 wordList 中。
- sk == endWord
给你两个单词 beginWord 和 endWord 和一个字典 wordList ，返回 从 beginWord 到 endWord 的 最短转换序列 中的 单词数目 。如果不存在这样的转换序列，返回 0 。
```
- 思路1：广度优先搜索，优化建图过程，一个单词，建立虚拟节点，将单词连向虚拟节点，比如单词马冬梅，我们建立三个虚拟节点，\*冬梅，马\*梅，和马冬\*，那么马冬梅能转化到的单词也会连向这三个节点。最后求的路径长度便是两倍。
```cpp
class Solution {
public:
    unordered_map<string, int> wordID;
    vector<vector<int>> edges;
    int nodenum = 0;

    void addword(string& word){
        if (!wordID.count(word)){
            wordID[word] = nodenum++;
            edges.emplace_back();
        }
    }

    void addedge(string& word){
        addword(word);
        int id1 = wordID[word];
        for (char& it : word){
            char temp = it;
            it = '*';
            addword(word);
            int id2 = wordID[word];
            edges[id1].push_back(id2);
            edges[id2].push_back(id1);
            it = temp;
        }
    }

    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        for (string& word : wordList){
            addedge(word);
        }
        addedge(beginWord);
        if (!wordID.count(endWord)){
            return 0;
        }
        vector<int> dis(nodenum, INT_MAX);
        int beginid = wordID[beginWord], endid = wordID[endWord];
        dis[beginid] = 0;
        queue<int> q;
        q.push(beginid);
        while (!q.empty()){
            int x = q.front();
            q.pop();
            if (x == endid){
                return dis[endid] / 2 + 1;
            }
            for (int& it : edges[x]){
                if (dis[it] == INT_MAX){
                    dis[it] = dis[x] + 1;
                    q.push(it);
                }
            }
        }
        return 0;
    }
};
```
- 思路2：双向广度优先搜索，减小搜索空间
```cpp
class Solution {
public:
    unordered_map<string, int> wordID;
    vector<vector<int>> edges;
    int nodenum = 0;

    void addword(string& word){
        if (!wordID.count(word)){
            wordID[word] = nodenum++;
            edges.emplace_back();
        }
    }

    void addedge(string& word){
        addword(word);
        int id1 = wordID[word];
        for (char& it : word){
            char temp = it;
            it = '*';
            addword(word);
            int id2 = wordID[word];
            edges[id1].push_back(id2);
            edges[id2].push_back(id1);
            it = temp;
        }
    }

    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        for (string& word : wordList){
            addedge(word);
        }
        addedge(beginWord);
        if (!wordID.count(endWord)){
            return 0;
        }

        // 两个队列，每一循环，begin队列往后bfs一层，end队列往前bfs一层
        vector<int> disbegin(nodenum, INT_MAX);
        int beginid = wordID[beginWord];
        disbegin[beginid] = 0;
        queue<int> qbegin;
        qbegin.push(beginid);

        vector<int> disend(nodenum, INT_MAX);
        int endid = wordID[endWord];
        disend[endid] = 0;
        queue<int> qend;
        qend.push(endid);

        while (!qbegin.empty() && !qend.empty()){
            int qbegin_size = qbegin.size();
            for (int i = 0; i < qbegin_size; ++i){
                int nodebegin = qbegin.front();
                qbegin.pop();
                if (disend[nodebegin] != INT_MAX){ // 如果disend也能到达nodebegin节点，说明此路可通
                    return (disbegin[nodebegin] + disend[nodebegin]) / 2 + 1;
                }
                for (int& it : edges[nodebegin]){
                    if (disbegin[it] == INT_MAX){
                        disbegin[it] = disbegin[nodebegin] + 1;
                        qbegin.push(it);
                    }
                }
            }

            int qend_size = qend.size();
            for (int i = 0; i < qend_size; ++i){
                int nodeend = qend.front();
                qend.pop();
                if (disbegin[nodeend] != INT_MAX){ // 如果disbegin也能到达nodeend节点，说明此路可通
                    return (disbegin[nodeend] + disend[nodeend]) / 2 + 1;
                }
                for (int& it : edges[nodeend]){
                    if (disend[it] == INT_MAX){
                        disend[it] = disend[nodeend] + 1;
                        qend.push(it);
                    }
                }
            }
        }
        return 0;
    }
};
```

## [冗余连接](https://leetcode.cn/problems/redundant-connection/)
```
树可以看成是一个连通且 无环 的 无向 图。
给定往一棵 n 个节点 (节点值 1～n) 的树中添加一条边后的图。添加的边的两个顶点包含在 1 到 n 中间，且这条附加的边不属于树中已存在的边。图的信息记录于长度为 n 的二维数组 edges ，edges[i] = [ai, bi] 表示图中在 ai 和 bi 之间存在一条边。
请找出一条可以删去的边，删除后可使得剩余部分是一个有着 n 个节点的树。如果有多个答案，则返回数组 edges 中最后出现的边。
```
- 思路：Kruskal，并查集
```cpp
class Solution {
public:
    int find(vector<int>& fa, int x){
        return x == fa[x] ? x : (fa[x] = find(fa, fa[x]));
    }

    void merge(vector<int>& fa, int x, int y){
        fa[find(fa, x)] = find(fa, y);
    }

    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        int n = edges.size();
        vector<int> fa(n + 1);
        for (int i = 1; i <= n; ++i) {fa[i] = i;}
        for (auto& e:edges){
            int n1 = e[0], n2 = e[1];
            if (find(fa, n1) != find(fa, n2)){
                merge(fa, n1, n2);
            }else{
                return e;
            }
        }
        return vector<int> {};
    }
};
```


# 题目总结
&#x2705;[腐烂的橘子](https://leetcode.cn/problems/rotting-oranges/)

&#x2705;[最短的桥](https://leetcode.cn/problems/shortest-bridge/)

&#x2705;[滑动谜题](https://leetcode.cn/problems/sliding-puzzle/)

&#x2705;[网络延迟时间](https://leetcode.cn/problems/network-delay-time/)

[K站中转内最便宜的航班](https://leetcode.cn/problems/cheapest-flights-within-k-stops/)

[单词接龙](https://leetcode.cn/problems/word-ladder/)

[冗余连接](https://leetcode.cn/problems/redundant-connection/)