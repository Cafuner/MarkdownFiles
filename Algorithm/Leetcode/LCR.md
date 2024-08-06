# LCR 

[TOC]



## LCR 125.图书整理II

>读者来到图书馆排队借还书，图书管理员使用两个书车来完成整理借还书的任务。书车中的书从下往上叠加存放，图书管理员每次只能拿取书车顶部的书。排队的读者会有两种操作：
>
>- `push(bookID)`：把借阅的书籍还到图书馆。
>- `pop()`：从图书馆中借出书籍。
>
>为了保持图书的顺序，图书管理员每次取出供读者借阅的书籍是 **最早** 归还到图书馆的书籍。你需要返回 **每次读者借出书的值** 。
>
>如果没有归还的书可以取出，返回 `-1` 。
>
>- `1 <= bookID <= 10000`
>- 最多会对 `push`、`pop` 进行 `10000` 次调用

用两个栈实现队列。

```c++
class CQueue {
public:
    stack<int> in;
    stack<int> out;
    CQueue() {

    }
    
    void appendTail(int value) {
        in.push(value);
    }
    
    int deleteHead() {
        if(out.size()) {
            int ret = out.top();
            out.pop();
            return ret;
        }
        else if(in.size()) {
            int sz = in.size();
            for(int i=0; i<sz; i++) {
                out.push(in.top());
                in.pop();
            }
            int ret = out.top();
            out.pop();
            return ret;
        }
        else {
            return -1;
        }
    }
};

/**
 * Your CQueue object will be instantiated and called as such:
 * CQueue* obj = new CQueue();
 * obj->appendTail(value);
 * int param_2 = obj->deleteHead();
 */
```





## LCR 140.训练计划II

>给定一个头节点为 `head` 的链表用于记录一系列核心肌群训练项目编号，请查找并返回倒数第 `cnt` 个训练项目编号。
>
>- `1 <= head.length <= 100`
>- `0 <= head[i] <= 100`
>- `1 <= cnt <= head.length`

时间复杂度 $O(n)$ 。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* trainingPlan(ListNode* head, int cnt) {
        ListNode* ret = head;
        ListNode* end = head;
        for(int i=0; i<cnt; i++) {
            end = end->next;
        }
        while(end!=nullptr) {
            ret = ret->next;
            end = end->next;
        }
        return ret;
    }
};
```



## LCR 143.子结构判断

>给定两棵二叉树 `tree1` 和 `tree2`，判断 `tree2` 是否以 `tree1` 的某个节点为根的子树具有 **相同的结构和节点值** 。
>注意，**空树** 不会是以 `tree1` 的某个节点为根的子树具有 **相同的结构和节点值** 。
>
>```
>0 <= 节点个数 <= 10000
>```

时间复杂度 $O(mn)$ 。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:

    bool check(TreeNode* A, TreeNode* B) {
        if(B==NULL) {
            return true;
        }
        if(A==NULL) {
            return false;
        }
        if(A->val!=B->val) {
            return false;
        }
        return check(A->left, B->left) && check(A->right, B->right);
    }

    bool isSubStructure(TreeNode* A, TreeNode* B) {
        if(A==NULL || B==NULL) {
            return false;
        }
        int root = B->val;
        queue<TreeNode*> que;
        que.push(A);
        while(!que.empty()) {
            TreeNode* node = que.front();
            que.pop();
            if(node->val == root && check(node, B)) {
                return true;
            } 
            if(node->left) {
                que.push(node->left);
            }
            if(node->right) {
                que.push(node->right);
            }
        }
        return false;
    }
};
```











