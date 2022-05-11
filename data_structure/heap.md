# 优先级队列（堆）
用到优先级队列 (priority queue) 或堆 (heap) 的题一般需要维护一个动态更新的池，元素会被频繁加入到池中或从池中被取走，每次取走的元素为池中优先级最高的元素 (可以简单理解为最大或者最小)。用堆来实现优先级队列是效率非常高的方法，加入或取出都只需要 $O(log N)$ 的复杂度。

# Kth largest/smallest


## [数据流中的第K大元素](https://leetcode-cn.com/problems/kth-largest-element-in-a-stream/)
```
设计一个找到数据流中第 k 大元素的类（class）。注意是排序后的第 k 大元素，不是第 k 个不同的元素。
请实现 KthLargest 类：
KthLargest(int k, int[] nums) 使用整数 k 和整数流 nums 初始化对象。
int add(int val) 将 val 插入数据流 nums 后，返回当前数据流中第 k 大的元素。
```

```cpp
class KthLargest {
public:
    priority_queue<int, vector<int>, greater<int>> hp;
    int kk;

    KthLargest(int k, vector<int>& nums) {
        kk = k;
        for (auto& n : nums){
            add(n);
        }
    }
    
    int add(int val) {
        hp.push(val);
        if (hp.size() > kk){
            hp.pop();
        }
        return hp.top();
    }
};
```

## [有序矩阵中第K小的元素](https://leetcode-cn.com/problems/kth-smallest-element-in-a-sorted-matrix/)
```
给你一个 n x n 矩阵 matrix ，其中每行和每列元素均按升序排序，找到矩阵中第 k 小的元素。
请注意，它是 排序后 的第 k 小元素，而不是第 k 个 不同 的元素。
你必须找到一个内存复杂度优于 O(n2) 的解决方案。
```
- 思路1：每一行相当于一个单调递增的队列，将每个队首元素放入小顶堆中，第K个从堆中弹出的便是所求元素。
```cpp
class Solution {
public:
    class num{
    public:
        int val, x, y;
        num(int _val, int _x, int _y):val(_val), x(_x), y(_y){}
        bool operator > (const num& n) const {return this->val > n.val;}
    };

    int kthSmallest(vector<vector<int>>& matrix, int k) {
        priority_queue<num, vector<num>, greater<num>> q;
        for (int i = 0; i < matrix.size(); ++i){
            q.emplace(matrix[i][0], i, 0);
        }
        for (int i = 0; i < k - 1; ++i){
            num n = q.top();
            q.pop();
            if (n.y < matrix.size() - 1){
                q.emplace(matrix[n.x][n.y + 1], n.x, n.y + 1);
            }
        }
        return q.top().val;
    }
};
```
- 思路2：二分查找，对于一个数mid，矩阵中小于该数的一定在左上角，大于该数的一定在右下角，可以进行二分查找，找到刚好左上角数字个数为k的mid。

```cpp
class Solution {
public:
    bool check(vector<vector<int>>& matrix, int k, int mid, int n){
        int i = n - 1, j = 0, num = 0;
        while (i >= 0 && j <= n - 1){
            if (matrix[i][j] <= mid){
                num += i + 1;
                ++j;
            }else{
                --i;
            }
        }
        return num >= k;
    }

    int kthSmallest(vector<vector<int>>& matrix, int k) {
        int n = matrix.size(), left = matrix[0][0], right = matrix[n - 1][n - 1];
        while (left < right){
            int mid = left + ((right - left) >> 1);
            if (check(matrix, k, mid, n)){
                right = mid;
            }else{
                left = mid + 1;
            }
        }
        return left;
    }
};
```

## [查找和最小的K对数字](https://leetcode-cn.com/problems/find-k-pairs-with-smallest-sums/)
```
给定两个以 升序排列 的整数数组 nums1 和 nums2 , 以及一个整数 k 。
定义一对值 (u,v)，其中第一个元素来自 nums1，第二个元素来自 nums2 。
请找到和最小的 k 个数对 (u1,v1),  (u2,v2)  ...  (uk,vk) 。
```
- 思路：优先队列（小顶堆），弹出k个元素

```cpp
class Solution {
public:
    vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        vector<vector<int>> ans;
        int m = nums1.size(), n = nums2.size();
        auto cmp = [&nums1, &nums2](const pair<int, int>& p1, const pair<int, int>& p2){
            return nums1[p1.first] + nums2[p1.second] > nums1[p2.first] + nums2[p2.second];
        };
        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(cmp)> q(cmp);
        for (int i = 0; i < min(k, m); ++i){
            q.emplace(i, 0);
        }
        while (k-- > 0 && !q.empty()){
            int x = q.top().first, y = q.top().second;
            q.pop();
            vector<int> temp = {nums1[x], nums2[y]};
            ans.emplace_back(temp);
            if (y < n - 1){
                q.emplace(x, y + 1);
            }
        }
        return ans;
    }
};
```

# 贪心+堆

## [最大的团队表现值](https://leetcode-cn.com/problems/maximum-performance-of-a-team/)
```
公司有编号为 1 到 n 的 n 个工程师，给你两个数组 speed 和 efficiency ，其中 speed[i] 和 efficiency[i] 分别代表第 i 位工程师的速度和效率。请你返回由最多 k 个工程师组成的 ​​​​​​最大团队表现值 ，由于答案可能很大，请你返回结果对 10^9 + 7 取余后的结果。
团队表现值 的定义为：一个团队中「所有工程师速度的和」乘以他们「效率值中的最小值」。
```
- 思路：先将工程师按照效率降序排列，从高到低枚举效率值，选取效率值高于枚举值的，人数小于等于k，速度尽可能大，的工程师。可以将工程师入堆，小顶堆（按速度比较），当堆中元素达到k时，弹出堆顶元素，再进行下一次循环，这样能保证堆中元素是目前的前k大。
```cpp
class Solution {
public:
    using ll = long long;
    
    int maxPerformance(int n, vector<int>& speed, vector<int>& efficiency, int k) {
        vector<pair<int, int>> v;
        auto cmp = [](const pair<int, int>& s1, const pair<int, int>& s2) {return s1.first > s2.first; };
        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(cmp)> q(cmp);
        for (int i = 0; i < n; ++i) {
            v.emplace_back(speed[i], efficiency[i]);
        }
        sort(v.begin(), v.end(), [](const pair<int, int>& s1, const pair<int, int>& s2) {return s1.second > s2.second; });
        ll ans = 0, sum = 0;
        for (int i = 0; i < n; ++i) {
            ll mine = v[i].second;
            ll maxs = sum + v[i].first;
            ans = max(ans, mine * maxs);
            q.push(v[i]);
            sum += v[i].first;
            if (q.size() == k) {
                sum -= q.top().first;
                q.pop();
            }
        }
        return ans % (int(1e9) + 7);
    }
};
```

## [IPO](https://leetcode.cn/problems/ipo/)'
```
假设 力扣（LeetCode）即将开始 IPO 。为了以更高的价格将股票卖给风险投资公司，力扣 希望在 IPO 之前开展一些项目以增加其资本。 由于资源有限，它只能在 IPO 之前完成最多 k 个不同的项目。帮助 力扣 设计完成最多 k 个不同项目后得到最大总资本的方式。
给你 n 个项目。对于每个项目 i ，它都有一个纯利润 profits[i] ，和启动该项目需要的最小资本 capital[i] 。
最初，你的资本为 w 。当你完成一个项目时，你将获得纯利润，且利润将被添加到你的总资本中。
总而言之，从给定项目中选择 最多 k 个不同项目的列表，以 最大化最终资本 ，并输出最终可获得的最多资本。
```
- 思路：首先，将项目按照资本升序排序，将满足w的项目入大顶堆（按照利润），堆顶便是当前能购买的利润最大的项目。购买一次后，会更新w，再接着进入入堆操作。
```cpp
class Solution {
public:
    int findMaximizedCapital(int k, int w, vector<int>& profits, vector<int>& capital) {
        vector<pair<int, int>> projects;
        for(int i = 0; i < capital.size(); ++i){
            projects.emplace_back(profits[i], capital[i]);
        }
        sort(projects.begin(), projects.end(), [](const pair<int, int>& p1, const pair<int, int>& p2){return p1.second < p2.second;});
        int index = 0;
        auto cmp = [](const pair<int, int>& p1, const pair<int, int>& p2){return p1.first < p2.first;};
        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(cmp)> q(cmp);
        for (int i = 0; i < k; ++i){
            while (index < projects.size() && projects[index].second <= w){
                q.push(projects[index++]);
            }
            if (!q.empty()){
                w += q.top().first;
                q.pop();
            }else{
                break;
            }
        }
        return w;
    }
};
```

## [会议室2](https://leetcode.cn/problems/meeting-rooms-ii/)
```
给你一个会议时间安排的数组 intervals ，每个会议时间都会包括开始和结束的时间 intervals[i] = [starti, endi] ，为避免会议冲突，同时要考虑充分利用会议室资源，请你计算至少需要多少间会议室，才能满足这些会议安排。
```
- 思路：先将会议按开始时间排序，然后建立小顶堆，放入会议结束的房间。堆顶元素即是结束最早的会议室的结束时间，若小于当前会议开始时间，则弹出堆顶会议室，更新后再入堆，若堆顶元素大于当前会议结束时间，说明没有会议室结束，需要新开，所以直接入堆。
```cpp
class Solution {
public:
    int minMeetingRooms(vector<vector<int>>& intervals) {
        if (intervals.size() == 0) {
            return 0;
        }
        priority_queue<int, vector<int>, greater<int>> allocator;

        sort(intervals.begin(), intervals.end(),
                [](auto a, auto b) { return  a[0] < b[0]; });
        allocator.push(intervals[0][1]);
        for (int i = 1; i < intervals.size(); i++) {
            if (intervals[i][0] >= allocator.top()) {
                allocator.pop();
            }
            allocator.push(intervals[i][1]);
        }

        return allocator.size();
    }
};
```

## []()
```
给定一个字符串 s ，检查是否能重新排布其中的字母，使得两相邻的字符不同。
返回 s 的任意可能的重新排列。若不可行，返回空字符串 "" 。
```
- 思路：将每个字母出现的次数放入大顶堆，若堆的大小大于2，每次取堆顶两个元素，将其拼入答案中，重新入堆。最后若堆中还剩一个元素，则直接拼入答案
```cpp
class Solution {
public:
    string reorganizeString(string s) {
        if (s.size() < 2) {return s;}
        vector<int> counts(26, 0);
        for (int i = 0; i < s.size(); ++i) {++counts[s[i] - 'a'];}
        if (*max_element(counts.begin(), counts.end()) > (s.size() + 1) / 2) {return "";}
        auto cmp = [&counts](const char& c1, const char& c2){return counts[c1 - 'a'] < counts[c2 - 'a'];};
        priority_queue<char, vector<char>, decltype(cmp)> q(cmp);
        for (char c = 'a'; c <= 'z'; ++c) {if (counts[c - 'a']) {q.push(c);}}
        string ans = "";
        while (q.size() > 1){
            char c1 = q.top();
            q.pop();
            --counts[c1 - 'a'];
            char c2 = q.top();
            q.pop();
            --counts[c2 - 'a'];
            if (counts[c1 - 'a']) {q.push(c1);}
            if (counts[c2 - 'a']) {q.push(c2);}
            ans += c1;
            ans += c2;
        }
        if (!q.empty()) {ans += q.top();}
        return ans;
    }
};
```

## [连接所有点的最小费用](https://leetcode.cn/problems/min-cost-to-connect-all-points/)
由于原repo中的题目为lintcode中的收费题，这里就找一题leetcode中的来代替。这里知识点是堆优化的Prim算法。
```
给你一个points 数组，表示 2D 平面上的一些点，其中 points[i] = [xi, yi] 。
连接点 [xi, yi] 和点 [xj, yj] 的费用为它们之间的 曼哈顿距离 ：|xi - xj| + |yi - yj| ，其中 |val| 表示 val 的绝对值。
请你返回将所有点连接的最小总费用。只有任意两点之间 有且仅有 一条简单路径时，才认为所有点都已连接。
```
- 思路：在Prim算法中，寻找最短边时采用堆

```cpp

```