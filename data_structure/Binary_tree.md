# 二叉树遍历
前序遍历：先访问根节点，再前序遍历左子树，再前序遍历右子树 

中序遍历：先中序遍历左子树，再访问根节点，再中序遍历右子树 

后序遍历：先后序遍历左子树，再后序遍历右子树，再访问根节点

注意：
- 根的遍历顺序决定了是哪一种遍历
- 左子树为优先子树，也有题目需要先遍历右子树


## [前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)
递归：
```cpp
class Solution {
public:
    vector<int> res;

    void preorderhelper(TreeNode* root){
        if (!root) {return;}
        res.push_back(root->val);
        preorderhelper(root->left);
        preorderhelper(root->right);
    }

    vector<int> preorderTraversal(TreeNode* root) {
        preorderhelper(root);
        return res;
    }
};
```
非递归：
```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> res;
        if (!root){
            return res;
        }
        st.push(root);
        while (!st.empty()){
            TreeNode* node = st.top();
            st.pop();
            res.push_back(node->val);
            if (node->right) {st.push(node->right);}
            if (node->left) {st.push(node->left);}
        }
        return res;
    }
};
```

## [中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)
递归：
```cpp
class Solution {
public:
    vector<int> res;

    void inorderhelper(TreeNode* root){
        if (!root) {return;}
        inorderhelper(root->left);
        res.push_back(root->val);
        inorderhelper(root->right);
    }

    vector<int> inorderTraversal(TreeNode* root) {
        inorderhelper(root);
        return res;
    }
};
```
非递归：
```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> res;
        if (!root){
            return res;
        }
        TreeNode* node = root;
        while (!st.empty() || node){
            if (node){
                st.push(node);
                node = node->left;
            }else{
                node = st.top();
                st.pop();
                res.push_back(node->val);
                node = node->right;
            }
        }
        return res;
    }
};
```

## [后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)
递归：
```cpp
class Solution {
public:
    vector<int> res;

    void postorderhelper(TreeNode* root){
        if (!root) {return;}
        postorderhelper(root->left);
        postorderhelper(root->right);
        res.push_back(root->val);
    }

    vector<int> postorderTraversal(TreeNode* root) {
        postorderhelper(root);
        return res;
    }
};
```
非递归：
```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        stack<TreeNode*> st;
        vector<int> res;
        TreeNode* node = root;
        TreeNode* last_visit = new TreeNode();
        while (!st.empty() || node){
            if (node){
                st.push(node);
                node = node->left;
            }else{
                TreeNode* fa = st.top(); //暂时不弹出，等右孩子节点弹出后，根节点才会弹出
                if (fa->right && last_visit != fa->right){
                    node = fa->right;
                }else{
                    last_visit = st.top();
                    st.pop();
                    res.push_back(last_visit->val);
                }
            }
        }
        return res;
    }
};
```

## [层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)
层序遍历采用队列，一层一层地遍历二叉树
```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if (!root) {return res;}
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()){
            vector<int> temp;
            TreeNode* node;
            int n = q.size();
            for (int i = 0; i < n; ++i){
                node = q.front();
                q.pop();
                temp.push_back(node->val);
                if (node->left) {q.push(node->left);}
                if (node->right) {q.push(node->right);}
            }
            res.push_back(temp);
        }
        return res;
    }
};
```

# 分治法(二叉树DFS应用)
二叉树的部分题目，天然适合分治法。先处理左子树，再处理右子树，再合并结果。

分支法一般用于快速排序、归并排序、二叉树等等。

分治法模板：
- 递归返回条件
- 分段处理
- 合并结果

这里列举一些二叉树的算法题：
## [二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)
```
给定一个二叉树，找出其最大深度。
二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。
**说明**: 叶子节点是指没有子节点的节点。
```
- 思路一：递归分治
```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (!root) {return 0;} else {return 1 + max(maxDepth(root->left), maxDepth(root->right));}
    }
};
```
- 思路二：层序遍历
```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        int depth = 0;
        if (!root) {return 0;}
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()){
            TreeNode* node;
            int n = q.size();
            for (int i = 0; i < n; ++i){
                node = q.front();
                q.pop();
                if (node->left) {q.push(node->left);}
                if (node->right) {q.push(node->right);}
            }
            ++depth;
        }
        return depth;
    }
};
```

## [平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)
```
给定一个二叉树，判断它是否是高度平衡的二叉树。
一棵高度平衡二叉树定义为：一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1 。
```
- 思路一：自顶向下递归
```cpp
class Solution {
public:
    int height(TreeNode* root) {
        if (!root) {return 0;} else {return 1 + max(height(root->left), height(root->right));}
    }

    bool isBalanced(TreeNode* root) {
        if (!root) {return true;} else {return abs(height(root->left) - height(root->right)) <= 1 && isBalanced(root->left) && isBalanced(root->right);}
    }
};
```
不难发现，时间复杂度为$O(n^2)$，而且递归时有很多height的重复计算。能不能把两次递归合在一起呢，用$O(n)$的复杂度完成此题。
- 思路二：自底向上递归
```cpp
class Solution {
public:
    int height(TreeNode* root) {
        if (!root) {return 0;}
        int left = height(root->left), right = height(root->right);
        if (left == -1 || right == -1 || abs(left - right) > 1){
            return -1;
        }else{
            return 1 + max(left, right);
        }
    }

    bool isBalanced(TreeNode* root) {
        return height(root) >= 0;
    }
};
```

## [二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)
```
给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。
最近公共祖先的定义为：“对于有根树 T 的两个节点 p、q，最近公共祖先表示为一个节点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”
```
```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root) {return nullptr;}
        if (root == p || root == q) {return root;}
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        if (!left) {return right;}
        if (!right) {return left;}
        return root;
    }
};
```

# 二叉树BFS应用
## [二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)
```
给你二叉树的根节点 root ，返回其节点值的 锯齿形层序遍历 。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。
```
- 思路：用双端队列进行层序遍历，变量记录当前的弹出和入队方向
```cpp
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if (!root) {return res;}
        deque<TreeNode*> q;
        q.push_back(root);
        bool start_from_left = true;
        while (!q.empty()){
            vector<int> temp;
            TreeNode* node;
            int n = q.size();
            if (start_from_left){
                for (int i = 0; i < n; ++i){
                    node = q.front();
                    q.pop_front();
                    temp.push_back(node->val);
                    if (node->left) {q.push_back(node->left);}
                    if (node->right) {q.push_back(node->right);}
                }
            }else{
                for (int i = 0; i < n; ++i){
                    node = q.back();
                    q.pop_back();
                    temp.push_back(node->val);
                    if (node->right) {q.push_front(node->right);}
                    if (node->left) {q.push_front(node->left);}
                }
            }
            start_from_left = !start_from_left;
            res.push_back(temp);
        }
        return res;
    }
};
```

# 二叉搜索树应用
## [验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)
```
给你一个二叉树的根节点 root ，判断其是否是一个有效的二叉搜索树。
```
- 思路一：中序遍历检查是否有序。
```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        stack<TreeNode*> st;
        long long pre = (long long)INT_MIN - 1;
        if (!root) {return true;}
        while (!st.empty() || root != nullptr){
            if (root){
                st.push(root);
                root = root->left;
            }else{
                root = st.top();
                st.pop();
                if (root->val <= pre){
                    return false;
                }
                pre = root->val;
                root = root->right;
            }
        }
        return true;
    }
};
```
- 思路二：利用二叉搜索树的性质，根结点为左子树的右边界，右子树的左边界。所以递归判断左子树的时候，将根节点作为上界；判断右子树的时候，将根节点作为下界。
```cpp
class Solution {
public:
    bool helper(TreeNode* root, long long lower, long long upper){
        if (!root) {return true;}
        if (root->val <= lower || root->val >= upper) {return false;}
        return helper(root->left, lower, root->val) && helper(root->right, root->val, upper);
    }

    bool isValidBST(TreeNode* root) {
        return helper(root, LONG_MIN, LONG_MAX);
    }
};
```

## [二叉搜索树中的插入操作](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)
```
给定二叉搜索树（BST）的根节点 root 和要插入树中的值 value ，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。 输入数据 保证 ，新值和原始二叉搜索树中的任意节点值都不同。
```
- 思路：找到最后一个叶子节点满足插入条件即可。如果是平衡二叉树，还需要进行平衡判断并调整，这里暂时没有。
```cpp
class Solution {
public:
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        if (!root) {return new TreeNode(val);}
        TreeNode* node = root;
        while (true){
            if (val > node->val){
                if (node->right){
                    node = node->right;
                }else{
                    node->right = new TreeNode(val);
                    return root;
                }
            }else{
                if (node->left){
                    node = node->left;
                }else{
                    node->left = new TreeNode(val);
                    return root;
                }
            }
        }
    }
};
```

## [二叉搜索树与双向链表](https://leetcode.cn/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)
```
输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。
```
```cpp
class Solution {
private:
    Node* pre, *head;

public:
    void dfs(Node* cur){
        if (!cur) {return;}
        dfs(cur->left);
        if (pre)
        {
            pre->right = cur;
        }else{
            head = cur;
        }
        cur->left = pre;
        pre = cur;
        dfs(cur->right);
    }

    Node* treeToDoublyList(Node* root) {
        if (!root) return nullptr;
        dfs(root);
        head->left = pre;
        pre->right = head;
        return head;
    }
};
```

# 总结
- 掌握二叉树前、中、后序遍历的递归和非递归，以及层序遍历；
- 熟悉二叉树DFS应用和其中的分治思想
- 熟悉二叉树BFS应用
- 熟悉二叉搜索树的基本性质和操作

# 题目总结
&#x2705;[前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

&#x2705;[中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

&#x2705;[后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

&#x2705;[层序遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

&#x2705;[二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

&#x2705;[平衡二叉树](https://leetcode-cn.com/problems/balanced-binary-tree/)

&#x2705;[二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

&#x2705;[二叉树的锯齿形层序遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

&#x2705;[验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

&#x2705;[二叉搜索树中的插入操作](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)