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