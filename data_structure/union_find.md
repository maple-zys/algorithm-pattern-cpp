# 并查集
用于处理不相交集合 (disjoint sets) 合并及查找的问题，典型应用有连通分量检测，环路检测等。原理和复杂度分析等可以参考[维基百科](https://en.wikipedia.org/wiki/Disjoint-set_data_structure)。

## [冗余连接](https://leetcode.cn/problems/redundant-connection/)
```
树可以看成是一个连通且 无环 的 无向 图。
给定往一棵 n 个节点 (节点值 1～n) 的树中添加一条边后的图。添加的边的两个顶点包含在 1 到 n 中间，且这条附加的边不属于树中已存在的边。图的信息记录于长度为 n 的二维数组 edges ，edges[i] = [ai, bi] 表示图中在 ai 和 bi 之间存在一条边。
请找出一条可以删去的边，删除后可使得剩余部分是一个有着 n 个节点的树。如果有多个答案，则返回数组 edges 中最后出现的边。
```
- 思路：遍历每一条边，若两个端点不属于一个集合，则将它们联合到一起，若属于同一个集合，则返回答案。
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

## [账户合并](https://leetcode.cn/problems/accounts-merge/)
```
给定一个列表 accounts，每个元素 accounts[i] 是一个字符串列表，其中第一个元素 accounts[i][0] 是 名称 (name)，其余元素是 emails 表示该账户的邮箱地址。
现在，我们想合并这些账户。如果两个账户都有一些共同的邮箱地址，则两个账户必定属于同一个人。请注意，即使两个账户具有相同的名称，它们也可能属于不同的人，因为人们可能具有相同的名称。一个人最初可以拥有任意数量的账户，但其所有账户都具有相同的名称。
合并账户后，按以下格式返回账户：每个账户的第一个元素是名称，其余元素是 按字符 ASCII 顺序排列 的邮箱地址。账户本身可以以 任意顺序 返回。
```
- 思路：基本思路依旧是并查集。并查集存储的是每个邮箱地址对应的编号，合并操作也是针对编号进行合并，遍历账户的邮箱后便可知道。合并后有多少个不同的账户。
```cpp
class Union{
public:
    vector<int> fa;

    void init(int n) {
        fa.resize(n);
        for (int i = 0; i < n; ++i) {fa[i] = i;}
    }

    int find(int x) {return x == fa[x] ? x : (fa[x] = find(fa[x]));}

    void merge(int x, int y) {fa[find(x)] = find(y);}
};

class Solution {
public:
    vector<vector<string>> accountsMerge(vector<vector<string>>& accounts) {
        map<string, int> email2index;
        map<string, string> email2name;
        int emailscount = 0;
        for (auto& a:accounts){
            string& name = a[0];
            for (int i = 1; i < a.size(); ++i){
                string& email = a[i];
                if (!email2index.count(email)){
                    email2index[email] = emailscount++;
                    email2name[email] = name;
                }
            }
        }
        Union u;
        u.init(emailscount);
        for (auto& a:accounts){
            string& firstemail = a[1];
            int firstindex = email2index[firstemail];
            for (int i = 2; i < a.size(); ++i){
                string& nextemail = a[i];
                int nextindex = email2index[nextemail];
                u.merge(firstindex, nextindex);
            }
        }
        map<int, vector<string>> index2emails;
        for (auto& [email, _] : email2index){
            int index = u.find(email2index[email]);
            index2emails[index].emplace_back(email);
        }
        vector<vector<string>> merged;
        for (auto& [_, emails] : index2emails){
            sort(emails.begin(), emails.end());
            string& name = email2name[emails[0]];
            vector<string> a;
            a.emplace_back(name);
            for (auto& e : emails){
                a.emplace_back(e);
            }
            merged.emplace_back(a);
        }
        return merged;
    }
};
```

## [最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/)
```
给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。
```
- 思路1：并查集，该题需维护每一个集合的大小

```cpp
class Solution {
public:
    map<int, int> fa, cnt;

    int find(int x) {return x == fa[x] ? x : (fa[x] = find(fa[x]));}

    void merge(int x, int y){
        x = find(x);
        y = find(y);
        if (x != y){
            fa[x] = y;
            cnt[y] += cnt[x];
        }
    }

    int longestConsecutive(vector<int>& nums) {
        for (int num:nums) {
            fa[num] = num;
            cnt[num] = 1;
        }
        for (int num : nums){
            if (fa.count(num + 1)){
                merge(num, num - 1);
            }
        }
        int ans = 0;
        for (int num : nums) { ans = max(ans, cnt[num]);}
        return ans;
    }
};
```
- 思路2：动态规划，字典内保存着以该数字为端点的连续序列的长度，遍历数组，得到以nums[i] - 1和nums[i] + 1为端点的连续序列的长度，当前的长度便为两者之和+1。
```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        map<int, int> ct;
        int ans = 0;
        for (int i = 0; i < nums.size(); ++i){
            if (ct[nums[i]] == 0){
                int left = ct[nums[i] -1], right = ct[nums[i] + 1]; 
                int cur = left + right + 1;
                ans = max(ans, cur);
                ct[nums[i] - left] = cur;
                ct[nums[i]] = cur;
                ct[nums[i] + right] = cur;
            }
            
        }
        return ans;
    }
};
```

## [连接所有点的最小费用](https://leetcode.cn/problems/min-cost-to-connect-all-points/submissions/)
没错，又是这题最小生成树，之前在堆那一章采用堆优化的Prim算法实现过，这里采用的是Kruskal+并查集。
```cpp
class Solution {
public:
    int mhd(vector<int>& point1, vector<int>& point2){
        return abs(point2[0] - point1[0]) + abs(point2[1] - point1[1]);
    }

    class edge{
    public:
        edge(int a, int b, int dis):a(a), b(b), dis(dis){}
        int a, b, dis;
    };

    int minCostConnectPoints(vector<vector<int>>& points) {
        int n = points.size();
        int ans = 0;
        vector<edge> es;
        for (int i = 0; i < n; ++i){
            for (int j = i + 1; j < n; ++j){
                es.push_back(edge(i, j, mhd(points[i], points[j])));
            }
        }
        vector<int> fa(n);
        for (int i = 0; i < n; i++){
            fa[i] = i;
        }
        sort(es.begin(), es.end(), [](const edge &e1, const edge &e2){return e1.dis < e2.dis;});
        for (auto e:es){
            int fa_a = find(e.a, fa);
            int fa_b = find(e.b, fa);
            if (fa_a != fa_b){
                merge(fa_a, fa_b, fa);
                ans += e.dis;
            }
        }
        return ans;
    }

    int find(int x, vector<int>& fa){
        return x == fa[x] ? x : (fa[x] = find(fa[x], fa));
    }

    void merge(int x, int y, vector<int>& fa){
        int fx = find(x, fa);
        int fy = find(y, fa);
        if (fx == fy){return;}
        fa[fx] = fy;
    }
};
```

# 题目总结
&#x2705;[冗余连接](https://leetcode.cn/problems/redundant-connection/)

&#x2705;[账户合并](https://leetcode.cn/problems/accounts-merge/)

&#x2705;[最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/)

&#x2705;[连接所有点的最小费用](https://leetcode.cn/problems/min-cost-to-connect-all-points/submissions/)