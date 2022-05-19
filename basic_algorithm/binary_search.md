# 二分搜索
给一个**有序数组**和目标值，找**第一次**/**最后一次**/**任何一次**出现的索引，时间复杂度 O(logN)。

## 模板
常用的二分搜索模板有如下三种形式：
![图3](../images/3.png)
这三个模板的不同之处在于：
- 左、中、右索引的分配；
- 循环或递归终止的条件；
- 后处理的必要性；

模板#1和#3是最常用的。模板2更高级一些，用于解决某些特定类型的问题。

### 模板1（left <= right）
- 二分查找最基础和最基本的形式；
- 查找条件可以在不与元素的两侧进行比较的情况下确定（或使用它周围的特定元素）；
- 不需要后处理，因为每一步中，都在检查是否找到了元素。如果到达末尾，则未找到；

### 模板2（left < right）
- 一种实现二分查找的高级方法；
- 查找条件需要访问元素的直接右邻居；
- 使用元素的右邻居来确定是否满足条件，并决定是否向左还是向右；
- 保证查找空间在每一步中至少有2个元素；
- 需要进行后处理，当你剩下1个元素时，循环/递归结束，需要评估剩余元素是否符合条件；

### 模板3（left + 1 < right）
- 实现二分查找的另一种方法；
- 搜索条件需要访问元素的直接左右邻居；
- 使用元素的邻居来确定它是向左还是向右；
- 保证查找空间在每个步骤中至少有3个元素；
- 需要进行后处理。当剩下2个元素时，循环/递归结束，需要评估其余元素是否符合条件；

详细的对比可以参考 Leetcode 上的文章：[二分搜索模板](https://leetcode.cn/leetbook/read/binary-search/xewjg7/)

### 模板例题，[二分查找](https://leetcode.cn/problems/binary-search/)
```
给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。
```
- 如果是最简单的二分搜索，不需要找第一个、最后一个位置，或者是没有重复元素，可以使用模板 1，代码更简洁。同时，如果搜索失败，left 是第一个大于 target 的索引，right 是最后一个小于 target 的索引。
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left <= right){
            int mid = left + ((right - left) >> 1);
            if (nums[mid] == target){
                return mid;
            }else if (nums[mid] < target){
                left = mid + 1;
            }else{
                right = mid - 1;
            }
        }
        return -1;
    }
};
```
- 模板2
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left < right){
            int mid = left + ((right - left) >> 1);
            if (nums[mid] < target){
                left = mid + 1;
            }else{
                right = mid;
            }
        }
        return nums[left] == target ? left : -1;
    }
};
```
- 模板3
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left + 1 < right){
            int mid = left + ((right - left) >> 1);
            if (nums[mid] < target){
                left = mid;
            }else{
                right = mid;
            }
        }
        if (nums[left] == target){
            return left;
        }else if (nums[right] == target){
            return right;
        }else{
            return -1;
        }
    }
};
```

## [在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)
```
给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。
如果数组中不存在目标值 target，返回 [-1, -1]。
```
- 模板2
```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> ans = {-1, -1};
        if (nums.size() == 0) {return ans;}
        int left = 0, right = nums.size() - 1;
        while (left < right){
            int mid = left + ((right - left) >> 1);
            if (nums[mid] < target){
                left = mid + 1;
            }else{
                right = mid;
            }
        }
        if (nums[left] == target){
            ans[0] = left;
        }else{
            return ans;
        }
        left = 0;
        right = nums.size() - 1;
        while (left < right){
            int mid = left + ((right - left + 1) >> 1);
            if (nums[mid] > target){
                right = mid - 1;
            }else{
                left = mid;
            }
        }
        ans[1] = right;
        return ans;
    }
};
```
- 模板3
```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> ans = {-1, -1};
        if (nums.size() == 0) {return ans;}
        int left = 0, right = nums.size() - 1;
        while (left + 1 < right){
            int mid = left + ((right - left) >> 1);
            if (nums[mid] < target){
                left = mid;
            }else{
                right = mid;
            }
        }
        if (nums[left] == target){
            ans[0] = left;
        }else if (nums[right] == target){
            ans[0] = right;
        }else{
            return ans;
        }
        left = 0;
        right = nums.size() - 1;
        while (left + 1 < right){
            int mid = left + ((right - left + 1) >> 1);
            if (nums[mid] <= target){
                left = mid;
            }else{
                right = mid;
            }
        }
        if (nums[right] == target){
            ans[1] = right;
        }else{
            ans[1] = left;
        }
        return ans;
    }
};
```

## [搜索插入位置](https://leetcode.cn/problems/search-insert-position/)
```
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
```
- 思路：模板1，如果搜索失败，left 是第一个大于 target 的索引，right 是最后一个小于 target 的索引。
```cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left <= right){
            int mid = left + ((right - left) >> 1);
            if (nums[mid] == target){
                return mid;
            }else if (nums[mid] < target){
                left = mid + 1;
            }else{
                right = mid - 1;
            }
        }
        return left;
    }
};
```

## [搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/)
```
编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：
每行中的整数从左到右按升序排列。
每行的第一个整数大于前一行的最后一个整数。
```
- 思路：两次二分，先定位行，再定位列
```cpp
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();
        int left = 0, right = m - 1;
        while (left <= right){
            int mid = left + ((right - left) >> 1);
            if (matrix[mid][0] == target){
                return true;
            }else if (matrix[mid][0] < target){
                left = mid + 1;
            }else{
                right = mid - 1;
            }
        }
        int row = max(0, right);
        left = 0;
        right = n - 1;
        while (left <= right){
            int mid = left + ((right - left) >> 1);
            if (matrix[row][mid] == target){
                return true;
            }else if (matrix[row][mid] < target){
                left = mid + 1;
            }else{
                right = mid - 1;
            }
        }
        return false;
    }
};
```

## [寻找旋转排序数组中的最小值1](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)
```
已知一个长度为 n 的数组，预先按照升序排列，经由 1 到 n 次 旋转 后，得到输入数组。例如，原数组 nums = [0,1,2,4,5,6,7] 在变化后可能得到：
若旋转 4 次，则可以得到 [4,5,6,7,0,1,2]
若旋转 7 次，则可以得到 [0,1,2,4,5,6,7]
注意，数组 [a[0], a[1], a[2], ..., a[n-1]] 旋转一次 的结果为数组 [a[n-1], a[0], a[1], a[2], ..., a[n-2]] 。
给你一个元素值 互不相同 的数组 nums ，它原来是一个升序排列的数组，并按上述情形进行了多次旋转。请你找出并返回数组中的 最小元素 。
```
```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int left = 0, right = nums.size() - 1;
        while (left < right){
            int mid = left + ((right - left) >> 1);
            if (nums[mid] > nums[right]){
                left = mid + 1;
            }else{
                right = mid;
            }
        }
        return nums[left];
    }
};
```

## [寻找旋转排序数组中的最小值2](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array-ii/)
```
与上一题相同，只不过数组中会有重复元素。
```
- 思路：与上一题不同的是，这题会出现`nums[mid] == nums[right]`的情况。当出现上述情况时，可以使用`--right`来解决，并不会造成最小值的丢失。因为若`nums[right]`是唯一最小值，那么`nums[mid] > nums[right]`，并不会进入这层判断。若`nums[right]`不是唯一的最小值，那么由于`mid < right`而`nums[mid] == nums[right]`，即还有最小值存在于`[left, right - 1]`区间，因此不会丢失。
```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int left = 0, right = nums.size() - 1;
        while (left < right){
            int mid = left + ((right - left) >> 1);
            if (nums[mid] > nums[right]){
                left = mid + 1;
            }else if (nums[mid] < nums[right]){
                right = mid;
            }else{
                --right;
            }
        }
        return nums[left];
    }
};
```

## [搜索旋转排序数组1](https://leetcode.cn/problems/search-in-rotated-sorted-array/)
```
整数数组 nums 按升序排列，数组中的值 互不相同 。
在传递给函数之前，nums 在预先未知的某个下标 k（0 <= k < nums.length）上进行了 旋转，使数组变为 [nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]（下标 从 0 开始 计数）。例如， [0,1,2,4,5,6,7] 在下标 3 处经旋转后可能变为 [4,5,6,7,0,1,2] 。
给你 旋转后 的数组 nums 和一个整数 target ，如果 nums 中存在这个目标值 target ，则返回它的下标，否则返回 -1 。
```
- 思路：mid在左边的升序序列还是右边的生序序列，target比`nums[mid]`大还小，组合一下有四种情况，分情况讨论往哪一半接着循环。
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left <= right){
            int mid = left + ((right - left) >> 1);
            if (nums[mid] == target){
                return mid;
            }else if (nums[mid] > target){
                if (nums[mid] > nums[right] && nums[left] > target){
                    left = mid + 1;
                }else{
                    right = mid - 1;
                }
            }else{
                if (nums[mid] < nums[left] && nums[right] < target){
                    right = mid - 1;
                }else{
                    left = mid + 1;
                }
            }
        }
        return -1;
    }
};
```

## [搜索旋转排序数组2](https://leetcode.cn/problems/search-in-rotated-sorted-array/)
```
与上一题相同，只不过此时数组内有重复元素
```
- 思路：基本与上题相同，只不过会出现`nums[left] == nums[mid] == nums[right]`的情况，此时将左边界加一右边界减一。
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left <= right){
            int mid = left + ((right - left) >> 1);
            if (nums[mid] == target){
                return mid;
            }else if (nums[mid] > target){
                if (nums[mid] > nums[right] && nums[left] > target){
                    left = mid + 1;
                }else{
                    right = mid - 1;
                }
            }else{
                if (nums[mid] < nums[left] && nums[right] < target){
                    right = mid - 1;
                }else{
                    left = mid + 1;
                }
            }
        }
        return -1;
    }
};
```

# 隐式二分搜索
有时用到二分搜索的题目并不会直接给你一个有序数组，它隐含在题目中，需要你去发现或者构造。一类常见的隐含的二分搜索的问题是求某个有界数据的最值，以最小值为例，当数据比最小值大时都符合条件，比最小值小时都不符合条件，那么符合/不符合条件就构成了一种有序关系，再加上数据有界，我们就可以使用二分搜索来找数据的最小值。注意，数据的界一般也不会在题目中明确提示你，需要你自己去发现。

## [爱吃香蕉的珂珂](https://leetcode.cn/problems/koko-eating-bananas/)
```
珂珂喜欢吃香蕉。这里有 n 堆香蕉，第 i 堆中有 piles[i] 根香蕉。警卫已经离开了，将在 h 小时后回来。
珂珂可以决定她吃香蕉的速度 k （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 k 根。如果这堆香蕉少于 k 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。  
珂珂喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。
返回她可以在 h 小时内吃掉所有香蕉的最小速度 k（k 为整数）。
```

```cpp
class Solution {
public:
    int minEatingSpeed(vector<int>& piles, int h) {
        int left = 1, right = *max_element(piles.begin(), piles.end());
        while (left <= right){
            int mid = left + ((right - left) >> 1);
            int time = 0;
            for (int i = 0; i < piles.size(); ++i){
                time += (piles[i] % mid == 0 ? piles[i] / mid : piles[i] / mid + 1);
                if (time > h) {break;}
            }
            if (time > h){
                left = mid + 1;
            }else{
                right = mid - 1;
            }
        }
        return left;
    }
};
```

## [分割数组的最大值](https://leetcode.cn/problems/split-array-largest-sum/)
```
给定一个非负整数数组 nums 和一个整数 m ，你需要将这个数组分成 m 个非空的连续子数组。
设计一个算法使得这 m 个子数组各自和的最大值最小。
```

```cpp
class Solution {
public:
    bool check(vector<int>& nums, int m, int mid){
        int s = 0, count = 1;
        for (int i = 0; i < nums.size(); ++i){
            s += nums[i];
            if (s > mid){
                ++count;
                s = nums[i];
            }
        }
        return count <= m;
    }

    int splitArray(vector<int>& nums, int m) {
        int left = *max_element(nums.begin(), nums.end()), right = accumulate(nums.begin(), nums.end(), 0);
        while (left <= right){
            int mid = left + ((right - left) >> 1);
            if (check(nums, m, mid)){
                right = mid - 1;
            }else{
                left = mid + 1;
            }
        }
        return left;
    }
};
```

## [在D天内送到包裹的能力](https://leetcode.cn/problems/capacity-to-ship-packages-within-d-days/)
```
传送带上的包裹必须在 days 天内从一个港口运送到另一个港口。
传送带上的第 i 个包裹的重量为 weights[i]。每一天，我们都会按给出重量（weights）的顺序往传送带上装载包裹。我们装载的重量不会超过船的最大运载重量。
返回能在 days 天内将传送带上的所有包裹送达的船的最低运载能力。
```

```cpp
class Solution {
public:
    bool check(vector<int>& weights, int days, int mid){
        int s = 0, count = 1;
        for (int i = 0; i < weights.size(); ++i){
            s += weights[i];
            if (s > mid){
                ++count;
                s = weights[i];
            }
        }
        return count <= days;
    }

    int shipWithinDays(vector<int>& weights, int days) {
        int left = *max_element(weights.begin(), weights.end()), right = accumulate(weights.begin(), weights.end(), 0);
        while (left <= right){
            int mid = left + ((right - left) >> 1);
            if (check(weights, days, mid)){
                right = mid - 1;
            }else{
                left = mid + 1;
            }
        }
        return left;
    }
};
```

# 题目总结
&#x2705;[二分查找](https://leetcode.cn/problems/binary-search/)

&#x2705;[在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

&#x2705;[搜索插入位置](https://leetcode.cn/problems/search-insert-position/)

&#x2705;[搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/)

&#x2705;[寻找旋转排序数组中的最小值1](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)

&#x2705;[寻找旋转排序数组中的最小值2](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array-ii/)

&#x2705;[搜索旋转排序数组1](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

&#x2705;[搜索旋转排序数组2](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

&#x2705;[爱吃香蕉的珂珂](https://leetcode.cn/problems/koko-eating-bananas/)

&#x2705;[分割数组的最大值](https://leetcode.cn/problems/split-array-largest-sum/)

&#x2705;[在D天内送到包裹的能力](https://leetcode.cn/problems/capacity-to-ship-packages-within-d-days/)