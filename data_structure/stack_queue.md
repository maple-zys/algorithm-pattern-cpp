# 简介
栈，后进先出，适合DFS深度搜索等；队列，先进先出，适合BFS广度搜索等。但栈和队列的应用远不止DFS、BFS。

# 栈
## [最小栈](https://leetcode-cn.com/problems/min-stack/)
```
设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。
实现 MinStack 类:
MinStack() 初始化堆栈对象。
void push(int val) 将元素val推入堆栈。
void pop() 删除堆栈顶部的元素。
int top() 获取堆栈顶部的元素。
int getMin() 获取堆栈中的最小元素。
```
- 思路：栈中存的元素，维护这当前插入时的最小值
```cpp
class MinStack {
private:
    stack<pair<int, int>> st;
public:
    MinStack() {}
    
    void push(int val) {
        if (st.empty()){
            st.push(make_pair(val, val));
        }else{
            st.push(make_pair(val, min(val, st.top().second)));
        }
    }
    
    void pop() {
        st.pop();
    }
    
    int top() {
        return st.top().first;
    }
    
    int getMin() {
        return st.top().second;
    }
};
```

## [逆波兰表达式求值](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)
```
根据 逆波兰表示法，求表达式的值。两个整数之间的除法只保留整数部分。
```
- 思路：采用栈，遍历字符串，遇到数字入栈，遇到运算符，弹出两个数字，然后将运算结果再入栈。

```cpp
class Solution {
public:
    int _stoi(string& s){
        int ans = 0, base = 1, sign, stop;
        if (s[0] == '-'){
            sign = 0;
            stop = 1;
        }else if (s[0] == '+'){
            sign = 1;
            stop = 1;
        }else{
            sign = 1;
            stop = 0;
        }
        for (int i = s.size() - 1; i >= stop; --i){
            ans += (s[i] - '0') * base;
            base *= 10;
        }
        return (sign) ? ans : (-1) * ans;
    }

    int evalRPN(vector<string>& tokens) {
        stack<int> st;
        for (int i = 0; i < tokens.size(); ++i){
            if (tokens[i] == "+"){
                int n1 = st.top();
                st.pop();
                int n2 = st.top();
                st.pop();
                st.push(n2 + n1);
            }else if (tokens[i] == "-"){
                int n1 = st.top();
                st.pop();
                int n2 = st.top();
                st.pop();
                st.push(n2 - n1);
            }else if (tokens[i] == "*"){
                int n1 = st.top();
                st.pop();
                int n2 = st.top();
                st.pop();
                st.push(n2 * n1);
            }else if (tokens[i] == "/"){
                int n1 = st.top();
                st.pop();
                int n2 = st.top();
                st.pop();
                st.push(n2 / n1);
            }else{
                st.push(_stoi(tokens[i]));
            }
        }
        return st.top();
    }
};
```