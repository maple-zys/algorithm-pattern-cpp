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
