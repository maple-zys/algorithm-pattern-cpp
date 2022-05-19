# 排序
## 常考排序
### 冒泡排序
```cpp
void BubbleSort(vector<int>& arr) {
    int n = arr.size();
    if (n < 2) { return; }
    for (int i = 0; i < n - 1; ++i) {
        for (int j = 0; j < n - 1 - i; ++j) {
            if (arr[j] > arr[j + 1]) {
                swap(arr[j], arr[j + 1);
            }
        }
    }
}

void BubbleSort(vector<int>& arr) {
	//优化版，只有在优化版中，最好时间复杂度才是O(n)
    int n = arr.size();
    if (n < 2) { return; }
    for (int i = 0; i < n - 1; ++i) {
        bool did_swap = false;
        for (int j = 0; j < n - 1 - i; ++j) {
            if (arr[j] > arr[j + 1]) {
                did_swap = true;
                swap(arr[j], arr[j + 1);
            }
        }
        if (!did_swap) { break; }
    }
}

```

### 快速排序
```cpp
int Partition_in_Quick_1(vector<int>& arr, int left, int right) {
	// 单向遍历
    int pivot = arr[left], pos = left;
    while (left < right) {
        while (left < right && arr[right] >= pivot) {
            --right;
        }
        while (left < right && arr[left] <= pivot) {
            ++left;
        }
        if (left < right) {
            swap(arr[left], arr[right]);
        }
    }
    arr[pos] = arr[left];
    arr[left] = pivot;
    return left;
}

int Partition_in_Quick_2(vector<int>& arr, int left, int right) {
	// 双向遍历
    int pivot = arr[left];
    while (left < right) {
        while (left < right && arr[right] >= pivot) {
            --right;
        }
        arr[left] = arr[right];
        while (left < right && arr[left] <= pivot) {
            ++left;
        }
        arr[right] = arr[left];
    }
    arr[left] = pivot;
    return left;
}

void QuickSort(vector<int>& arr, int left, int right) {
    if (left < right) {
        int pos = Partition_in_Quick_1(arr, left, right);
				// int pos = Partition_in_Quick_2(arr, left, right);
        QuickSort(arr, left, pos - 1);
        QuickSort(arr, pos + 1, right);
    }
}

```

### 插入排序
```cpp
void InsertSort(vector<int>& arr) {
    int n = arr.size();
    if (n < 2) { return; }
    for (int i = 1; i < n; ++i) {
        int target = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > target) {
            swap(arr[j], arr[j + 1]);
            --j;
        }
    }
}

```

### 希尔排序
```cpp
void ShellSort(vector<int>& arr) {
    int n = arr.size();
    if (n < 2) { return; }
    int step = n >> 1;
    while (step) {
        for (int i = step; i < n; ++i) {
            while (i >= step && arr[i] < arr[i - step]) {
                swap(arr[i], arr[i - step]);
                i -= step;
            }
        }
        step >>= 1;
    }
}

```

### 选择排序
```cpp
void SelectSort(vector<int>& arr) {
    int n = arr.size();
    if (n < 2) { return; }
    for (int i = 0; i < n - 1; ++i) {
        int min_index = i;
        for (int j = i + 1; j < n; ++j) {
            min_index = (arr[j] < arr[min_index]) ? j : min_index;
        }
        if (i != min_index) {
            swap(arr[i], arr[min_index]);
        }
    }
}

```

### 堆排序
```cpp
void HeapAdjust(vector<int>& arr, int i, int n) {
    int largest = i, left = 2 * i + 1, right = 2 * (i + 1);
    if (left < n && arr[left] > arr[largest]) {
        largest = left;
    }
    if (right < n && arr[right] > arr[largest]) {
        largest = right;
    }
    if (largest != i) {
        swap(arr[i], arr[largest]);
        HeapAdjust(arr, largest, n);
    }
}

void HeapSort(vector<int>& arr) {
    int n = arr.size();
    for (int i = n / 2; i >= 0; --i) {
        HeapAdjust(arr, i, n);
    }
    for (int j = n - 1; j > 0; --j) {
        swap(arr[0], arr[j]);
        HeapAdjust(arr, 0, j);
    }
}

```

### 归并排序
```cpp
void Merge(vector<int>& arr, int left, int mid, int right) {
    int n_temp = right - left + 1;
    vector<int> temp(n_temp);
    int t = 0; //辅助数组的起始下标
    int i = left, j = mid + 1; //两个子序列的起始位置
    while (i <= mid && j <= right) {
        if (arr[i] < arr[j]) {
            temp[t++] = arr[i++];
        }else {
            temp[t++] = arr[j++];
        }
    }
    while (i <= mid) {
        temp[t++] = arr[i++];
    }
    while (j <= right) {
        temp[t++] = arr[j++];
    }
    for (int k = 0; k < n_temp; ++k) {
        arr[left + k] = temp[k];
    }
}

void _reverse(vector<int>& arr, int begin, int end) {
    while (begin < end) {
        swap(arr[begin++], arr[end--]);
    }
}

void MergeInPlace(vector<int>& arr, int left, int mid, int right) {
	// 原地归并，不需要辅助数组
    int i = left, j = mid + 1;
    while (i < j && j <= right) {
        while (i < j && arr[i] <= arr[j]) {
            ++i;
        }
        int old_j = j;
        while (j <= right && arr[i] > arr[j]) {
            ++j;
        }
        _reverse(arr, i, old_j - 1);
        _reverse(arr, old_j, j - 1);
        _reverse(arr, i, j - 1);
        i += (j - old_j);
    }
}

void MergeSort(vector<int>& arr, int left, int right) {
	// 递归
    if (left < right) {
        int mid = left + ((right - left) >> 1);
        //int mid = (left + right) / 2;
        MergeSort(arr, left, mid);
        MergeSort(arr, mid + 1, right);
        Merge(arr, left, mid, right);
    }
}

void MergeSort_Non_Recursive(vector<int>& arr) {
	// 非递归
    int n = arr.size(), cur_len = 1;
    while (cur_len <= n) {
        for (int i = 0; i <= n - cur_len; i += cur_len * 2) {
            int left = i, mid = i + cur_len - 1, right = i + cur_len * 2 - 1;
            right = (right >= n) ? n - 1 : right;
            //Merge(arr, left, mid, right); // 辅助数组合并
            MergeInPlace(arr, left, mid, right); // 原地合并
        }
        cur_len *= 2;
    }
}

```

## [数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)
```
给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。
请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素.
```
- 思路1：排序后取第k个，最简单直接，复杂度 O(N log N) 代码略。
- 思路2：使用大小为k的最小堆，复杂度$O(N log k)$。
```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        priority_queue<int, vector<int>, greater<int>> q;
        for (int i = 0; i < nums.size(); ++i){
            if (q.size() < k){
                q.push(nums[i]);
            }else{
                if (nums[i] >= q.top()){
                    q.push(nums[i]);
                    q.pop();
                }
            }
        }
        return q.top();
    }
};
```
- 思路3：类似于快排，每次 partition 后检查 pivot 是否为第 k 个元素，如果是则直接返回，如果比 k 大，则继续 partition 小于 pivot 的元素，如果比 k 小则继续 partition 大于 pivot 的元素。相较于快排，quick select 每次只需 partition 一侧，因此平均复杂度为 O(N)。
```cpp
class Solution {
public:
    int quick_sort(vector<int>& nums, int left, int right){
        int pivot = nums[left];
        while (left < right){
            while (left < right && nums[right] <= pivot){
                --right;
            }
            nums[left] = nums[right];
            while (left < right && nums[left] >= pivot){
                ++left;
            }
            nums[right] = nums[left];
        }
        nums[left] = pivot;
        return left;
    }

    int findKthLargest(vector<int>& nums, int k) {
        int left = 0, right = nums.size() - 1;
        while (true){
            int pos = quick_sort(nums, left, right);
            if (pos == k - 1){
                return nums[pos];
            }else if (pos < k - 1){
                left = pos + 1;
            }else{
                right = pos - 1;
            }
        }
    }
};
```