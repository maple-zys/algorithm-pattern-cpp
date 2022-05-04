# 链表基本技能
- null/nil异常处理
- dummy node哑巴节点
- 快慢指针
- 插入节点到排序链表
- 从链表中删除节点
- 翻转链表
- 合并链表
- 找到链表的中间节点

# 常见题型
## [删除排序链表中的重复元素1](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)
```
给定一个已排序的链表的头 head ， 删除所有重复的元素，使每个元素只出现一次 。返回 已排序的链表 。
```
```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head) {return head;}
        ListNode* cur = head;
        while (cur->next){
            if (cur->val == cur->next->val){
                cur->next = cur->next->next;
            }else{
                cur = cur->next;
            }
        }
        return head;
    }
};
```

## [删除排序链表中的重复元素2](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)
```
给定一个已排序的链表的头 head ， 删除原始链表中所有重复数字的节点，只留下不同的数字 。返回 已排序的链表 。
```
- 思路：和上一题不同的是重复元素全部删除，头结点可能被删除，所以采用dummy结点辅助
```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head || !head->next) {return head;}
        ListNode* dummy = new ListNode(-1, head);
        ListNode* pre = dummy;
        ListNode* end;
        while (head && head->next){
            if (head->val != head->next->val){
                pre = head;
                head = head->next;
            }else{
                end = head->next->next;
                while (end && end->val == head->val){
                    end = end->next;
                }
                pre->next = end;
                head = end;
            }
        }
        return dummy->next;
    }
};
```
注意：访问node->next或node->val时，一定要保证node != nullptr

## [反转链表1](https://leetcode-cn.com/problems/reverse-linked-list/)
```
给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。
```
- 思路：在遍历链表时，将当前节点的next指针改为指向前一个节点。由于节点没有引用其前一个节点，因此必须事先存储其前一个节点。在更改引用之前，还需要存储后一个节点。最后返回新的头引用。
```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* pre = nullptr;
        ListNode* cur = head;
        while (cur) {
            ListNode* next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }
};
```

## [反转链表2](https://leetcode-cn.com/problems/reverse-linked-list-ii/)
```
给你单链表的头指针 head 和两个整数 left 和 right ，其中 left <= right 。请你反转从位置 left 到位置 right 的链表节点，返回 反转后的链表 。
```
- 找到需要反转的第一个节点，然后反转right-left次。
```cpp
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        if (!head) {return head;}
        right -= left;
        ListNode* dummy = new ListNode(-1, head);
        ListNode* cur = dummy;
        while (left > 1){
            cur = cur->next;
            --left;
        }
        ListNode* start = cur->next;
        while (right > 0){
            ListNode* temp = start->next;
            start->next = temp->next;
            temp->next = cur->next;
            cur->next = temp;
            --right;
        }
        return dummy->next;
    }
};
```

## [K个一组反转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)
```
给你链表的头节点 head ，每 k 个节点一组进行翻转，请你返回修改后的链表。
k 是一个正整数，它的值小于或等于链表的长度。如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。
```
- 思路：写一个反转整个链表的函数，输入为头尾结点，输出为新的头尾结点。遍历链表，每K个就将该K个链表断开，调用反转函数，再重连。

```cpp
class Solution {
public:
    pair<ListNode*, ListNode*> _reverse(ListNode* head, ListNode* tail){
        ListNode* pre = nullptr;
        ListNode* p = head;
        while (pre != tail){
            ListNode* nextnode = p->next;
            p->next = pre;
            pre = p;
            p = nextnode;
        }
        return make_pair(tail, head);
    }

    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* dummy = new ListNode(-1, head);
        ListNode* pre = dummy;
        while (head){
            ListNode* tail = pre;
            for (int i = 0; i < k; ++i){
                tail = tail->next;
                if (!tail) {return dummy->next;}
            }
            ListNode* nexthead = tail->next;
            pair<ListNode*, ListNode*> rst = _reverse(head, tail);
            head = rst.first;
            tail = rst.second;
            pre->next = head;
            tail->next = nexthead;
            pre = tail;
            head = nexthead;
        }
        return dummy->next;
    }
};
```
需要注意的是，
- 由于头结点有变化，所以需要构造dummy结点。
- 断开链表时，要记录这段链表的前一个结点和后一个结点，不然无法重连。

## [合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)
```
将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 
```
- 思路1：递归
```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        if (!list1) {return list2;}
        if (!list2) {return list1;}
        if (list1->val < list2->val){
            list1->next = mergeTwoLists(list1->next, list2);
            return list1;
        }else{
            list2->next = mergeTwoLists(list1, list2->next);
            return list2;
        }
    }
};
```
- 思路2：非递归，通过 dummy node 链表，连接各个元素
```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode* dummy = new ListNode(-1);
        ListNode* tail = dummy;
        while (list1 && list2){
            if (list1->val > list2->val){
                tail->next = list2;
                list2 = list2->next;
            }else{
                tail->next = list1;
                list1 = list1->next;
            }
            tail = tail->next;
        }
        if (!list1){
            tail->next = list2;
        }else{
            tail->next = list1;
        }
        return dummy->next;
    }
};
```

## [分割链表](https://leetcode-cn.com/problems/partition-list/)
```
给你一个链表的头节点 head 和一个特定值 x ，请你对链表进行分隔，使得所有小于 x 的节点都出现在 大于或等于 x 的节点之前。
你应当保留两个分区中每个节点的初始相对位置。
```
- 思路：将大于 x 的节点，放到另外一个链表，最后连接这两个链表
```cpp
class Solution {
public:
    ListNode* partition(ListNode* head, int x) {
        ListNode* greater = new ListNode(-1);
        ListNode* g_node = greater;
        ListNode* dummy = new ListNode(-1, head);
        ListNode* d_node = dummy;
        while (d_node->next){
            if (d_node->next->val >= x){
                g_node->next = d_node->next;
                g_node = g_node->next;
                d_node->next = d_node->next->next;
            }else{
                d_node = d_node->next;
            }
        }
        g_node->next = nullptr;
        d_node->next = greater->next;
        return dummy->next;
    }
};
```

## [排序链表](https://leetcode-cn.com/problems/sort-list/)
```
给你链表的头结点 head ，请将其按 升序 排列并返回 排序后的链表 。 复杂度为O(nlogn)
```
- 思路：链表归并排序。
```cpp
class Solution {
public:
    ListNode* _findmid(ListNode* head){
        ListNode* slow = head;
        ListNode* fast = head->next;
        while (fast && fast->next){
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }

    ListNode* _merge(ListNode* l1, ListNode* l2){
        ListNode* l_merge = new ListNode();
        ListNode* tail = l_merge;
        while (l1 && l2){
            if (l1->val > l2->val){
                tail->next = l2;
                l2 = l2->next;
            }else{
                tail->next = l1;
                l1 = l1->next;
            }
            tail = tail->next;
        }
        tail->next = (l1) ? l1 : l2;
        return l_merge->next;
    }

    ListNode* sortList(ListNode* head) {
        if (!head || !head->next) {return head;}
        ListNode* mid = _findmid(head);
        ListNode* tail = mid->next;
        mid->next = nullptr;
        return _merge(sortList(head), sortList(tail));
    }
};
```
注意：
1. 快慢指针找中点时，fast和fast->next都需要判断是否为空；
2. 递归时，需要将中间节点断开(mid->next = nullptr;)；
3. 递归停止条件为head或者head->next是空；

## [重排链表](https://leetcode-cn.com/problems/reorder-list/)
```
给定一个单链表 L 的头节点 head ，单链表 L 表示为：
L0 → L1 → … → Ln - 1 → Ln
请将其重新排列后变为：
L0 → Ln → L1 → Ln - 1 → L2 → Ln - 2 → …
不能只是单纯的改变节点内部的值，而是需要实际的进行节点交换。
```
- 思路1：每次将当前结点之后的链表翻转一次
```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* pre = nullptr;
        ListNode* cur = head;
        while (cur) {
            ListNode* next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }
        return pre;
    }

    void reorderList(ListNode* head) {
        if (!head || !head->next) {return;}
        ListNode* node = head;
        while (node->next){
            node->next = reverseList(node->next);
            node = node->next;
        }
        return;
    }
};
```
- 思路2：利用线性表存储该链表，直接按顺序访问指定元素，重建该链表
```cpp
class Solution {
public:
    void reorderList(ListNode* head) {
        if (!head || !head->next) {return;}
        vector<ListNode*> vec;
        ListNode* node = head;
        while (node){
            vec.emplace_back(node);
            node = node->next;
        }
        int i = 0, j = vec.size() - 1;
        while (i < j){
            vec[i]->next = vec[j];
            ++i;
            if (i == j){
                break;
            }
            vec[j]->next = vec[i];
            --j;
        }
        vec[i]->next = nullptr;
        return;
    }
};
```

## [环形链表1](https://leetcode-cn.com/problems/linked-list-cycle/)
```
给你一个链表的头节点 head ，判断链表中是否有环。
```
- 思路：设置快慢指针，如果有环，快指针一定会追上慢指针，如果没有环，快指针会指向nullptr

```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode* slow = head, *fast = head;
        while (fast && fast->next){
            slow = slow->next;
            fast = fast->next->next;
            if (fast == slow){
                return true;
            }
        }
        return false;
    }
};
```

## [环形链表2](https://leetcode-cn.com/problems/linked-list-cycle-ii/)
```
给定一个链表的头节点  head ，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。
```
- 思路：快慢指针，如果无环，快指针会指向nullptr，如果有环，快慢指针会相遇，相遇后将快指针重新指向head，两个指针均一步一步往下跳，再次相遇时，便为环的入口

```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* slow = head, *fast = head;
        while (fast && fast->next){
            slow = slow->next;
            fast = fast->next->next;
            if (fast == slow){
                fast = head;
                while (fast != slow){
                    slow = slow->next;
                    fast = fast->next;
                }
                return fast;
            }
        }
        return nullptr;
    }
};
```

## [回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)
```
给你一个单链表的头节点 head ，请你判断该链表是否为回文链表。如果是，返回 true ；否则，返回 false 。
```
- 思路：用栈存储前半链表的值，然后在遍历后半链表时一一弹出，进行比对。

```cpp
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        stack<int> st;
        ListNode* slow = head, *fast = head;
        while (fast && fast->next){
            st.push(slow->val);
            slow = slow->next;
            fast = fast->next->next;
        }
        if (fast){
            slow = slow->next;
        }
        while (!st.empty()){
            if (slow->val != st.top()){
                return false;
            }
            st.pop();
            slow = slow->next;
        }
        return true;
    }
};
```

## [复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)
```
给你一个长度为 n 的链表，每个节点包含一个额外增加的随机指针 random ，该指针可以指向链表中的任何节点或空节点。构造这个链表的 深拷贝。
```
- 思路1：回溯+哈希表，用哈希表记录每一个节点对应新节点的创建情况。遍历到某个结点时，如果这两个节点中的任何一个节点的新节点没有被创建，我们都立刻递归地进行创建，如果已经拷贝过，我们可以直接从哈希表中取出拷贝后的节点的指针并返回即可。
```cpp
class Solution {
public:
    map<Node*, Node*> hs;
    Node* copyRandomList(Node* head) {
        if (!head) {return head;}
        if (!hs.count(head)){
            Node* headNew = new Node(head->val);
            hs[head] = headNew;
            headNew->next = copyRandomList(head->next);
            headNew->random = copyRandomList(head->random);
        }
        return hs[head];
    }
};
```

- 思路2：思路1的空间复杂度为$O(n)$，而通过迭代+结点拆分（在每有链表的每一个个节点后面复制一个结点，然后再将复制的节点均拆出来连在一起），可以只使用$O(1)$的空间复杂度
```cpp
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if (!head) {return head;}
        for (Node* node = head; node != nullptr; node = node->next->next){
            Node* nodenew = new Node(node->val);
            nodenew->next = node->next;
            node->next = nodenew;
        }
        for (Node* node = head; node != nullptr; node = node->next->next){
            Node* nodenew = node->next;
            nodenew->random = (node->random) ? node->random->next : nullptr;
        }
        Node* headnew = head->next;
        for (Node* node = head; node != nullptr; node = node->next){
            Node* nodenew = node->next;
            node->next = node->next->next;
            nodenew->next = (nodenew->next) ? nodenew->next->next : nullptr;
        }
        return headnew;
    }
};
```

# 总结
- null/nil异常处理
- dummy node哑巴节点
- 快慢指针
- 插入节点到排序链表
- 从链表中删除节点
- 翻转链表
- 合并链表
- 找到链表的中间节点

# 题目总结
&#x2705;[删除排序链表中的重复元素1](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

&#x2705;[删除排序链表中的重复元素2](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

&#x2705;[反转链表1](https://leetcode-cn.com/problems/reverse-linked-list/)

&#x2705;[反转链表2](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

&#x2705;[K个一组反转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

&#x2705;[合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

&#x2705;[分割链表](https://leetcode-cn.com/problems/partition-list/)

&#x2705;[排序链表](https://leetcode-cn.com/problems/sort-list/)

&#x2705;[重排链表](https://leetcode-cn.com/problems/reorder-list/)

&#x2705;[环形链表1](https://leetcode-cn.com/problems/linked-list-cycle/)

&#x2705;[环形链表2](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

&#x2705;[回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)

&#x2705;[复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/)