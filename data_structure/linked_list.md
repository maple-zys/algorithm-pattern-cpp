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