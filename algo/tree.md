
# Tree
It is a Special Graph.
## Introduction
n个节点构成的有限集合
If n = 0， 是一棵空树
If n > 0, 有以下性质：
1. 树中有一个根（root）：称为r
2. 其余结点ke可分为m个互不相交的有限集T1，T2, ..., Tm,其中每个集合又是一棵树，称为原来树的子树(subtree).
## Representation
### Son-Sibling Way
```
+-------------------------+
|         Element         |
+-------------------------+
| FirstChild | NextSibling|
+-------------------------+

struct Element {
    Element* son; //represents its children
    Element* sibling; //represents other nodes on the same level
}
```

## Binary Tree
From Wikipidia:

A binary tree is a tree data structure in which each node has at most two children, referred to as the left child and the right child. That is, it is a k-ary tree with k = 2.

### Some Terminology
From Wikipidia:

+ A rooted binary tree has a root node and every node has at most two children.

+ A `full` binary tree (sometimes referred to as a `proper`, plane, or strict binary tree) is a tree in which every node has `either 0 or 2` children. 

    Another way of defining a full binary tree is a recursive definition. A full binary tree is either:

    A single vertex (a single node as the root node).
    A tree whose root node has two subtrees, both of which are full binary trees.

+ A `perfect` binary tree is a binary tree in which `all` interior nodes have `two` children and all leaves have the` same depth` or same level (the level of a node defined as the number of edges or links from the root node to a node). `A perfect binary tree is a full binary tree.`

+ A `complete` binary tree is a binary tree in which every level, `except possibly the last`, is completely filled, and all nodes in the last level are as far left as possible. It can have between 1 and 2h nodes at the last level h.`A perfect tree is therefore always complete but a complete tree is not always perfect.` Some authors use the term complete to refer instead to a perfect binary tree as defined above, in which case they call this type of tree (with a possibly not filled last level) an almost complete binary tree or nearly complete binary tree. A complete binary tree can be efficiently represented using an `array`.

+ A `balanced` binary tree is a binary tree structure in which the left and right subtrees of every node `differ in height` (the number of edges from the top-most node to the farthest node in a subtree) by `no more than 1` (or the skew is no greater than 1). One may also consider binary trees where no leaf is much farther away from the root than any other leaf. (Different balancing schemes allow different definitions of "much farther".)

+ A degenerate (or pathological) tree is where each 
parent node has only `one` associated child node.[24] This means that the tree will behave like a `linked list` data structure. In this case, an advantage of using a binary tree is significantly reduced because it is essentially a linked list which time complexity is O(n) (n as the number of nodes) and it has more data space than the linked list due to two pointers per node, while the complexity of O(log2n) for data search in a balanced binary tree is normally expected.

### Features
1. 一个二叉树的第i层的最大结点树为2^(i-1), i >= 1
2. 深度为k的二叉树有最大结点总数为2^(k-1), k >= 1
3. 对于任何非空二叉树，若n0表示叶结点的个数，n2表示度为2的非叶结点个数，那么二者满足关系：`n0 = n2 + 1` 

    证明：总边数可表示为 `总结点数 - 1` 和 `每个节点所贡献的边数的和`，即

        `n0 + n1 + n2 - 1 = n0*0 + n1*1 + n2*2`
    
    In this way, n0 = n2 + 1 can be proved.

### Operations
1. 判断树是否为空
2. 遍历二叉树
    1. 前序遍历 pre-order
    2. 中序遍历 in-order
    3. 后序遍历 post-order
    4. 层序遍历 level order
3. 创建二叉树

### 二叉树的存储结构
1. 顺序存储结构

`完全二叉树`简直是完美的候选人！

按照从上到下，从左到右顺序存储n个结点的完全二叉树的结点父子关系：
```
          A
       /     \
     B         C
   /   \     /   \
  D     E   F     G
 / \   / \ / \   / \
H   I J   K L   M   N   

| Index |  0  |  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |  9  | 10 | 11 | 12 | 13 |
|-------|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|----|----|----|----|
| Node  |  A  |  B  |  C  |  D  |  E  |  F  |  G  |  H  |  I  |  J  |  K |  L |  M |  N |
```
有以下几个性质：
1. 非根结点 (i > 1) 的父结点的序号是floor(i / 2)
2. 结点序号为i的左孩子结点的序号是2i
3. 结点序号为i的右孩子结点的序号是2i + 1 

对于一般二叉树，可以先把空缺的地方用nullptr来填充，补成完全二叉树再用数组进行存储

2. 链表存储
```
Struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
}
```

### Traversal
Using Recursion: 
    
1. 前序遍历 pre-order
2. 中序遍历 in-order
```C++
void inOrderTraversal (TreeNode* root) {
    if(root) {
        preOrderTraversal(root->left);
        cout << "root->val = " << root->val << endl;
        preOrderTraversal(root->right);
    }
}
```
3. 后序遍历 post-order

Without Using Recurion 使用Stack
基本思路：
+ 遇到一个结点，把它压入stack，并继续遍历它的左子树
+ 若左子树遍历完，或为空，从stack里pop掉这个结点并访问它
+ 然后按其右指针去中序遍历它的右子树
```C++
void inOrderTraversal (TreeNode* root) {
    stack s;
    s.push(root);
       while(!root || !s.empty()) {
            while(!root) {
                s.push(root);
                root = root->left;
            }
            if(!s.empty()) {
                root = stack.top();
                stack.pop();
                cout << "root->val = " << root->val << endl;
                root = root->right;
            }

       }
    }

```
Other order traversal just put 'cout << "root->val = " << root->val << endl;' into other places.

Level Order Traversal
``` C++
void levelOrderTraversal (TreeNode* root) {
    if(root == nullptr) return;
    queue<TreeNode*> q;
    q.push(root);
    while(!q.empty()) {
        int sz = q.size();
        for(int i = 0; i < sz; i++){
            TreeNode* top = q.front();
            q.pop;
            cout << "root->val = " << root->val << endl;
            if(top->left) q.push(top->left);
            if(top->right) q.push(top->right);

        }    
    }
}
```

Example:
1. 求二叉树的高度

    Height = max(Height(L), Height(R)) + 1
    因为要先知道左右子树的高度，所以要用`后序遍历`处理
    ```C++
    int getHeight(TreeNode* root) {
        if(root == nullptr) return 0;
        int leftH = getHeight(root->left);
        int right = getHeight(root->right);
    return max(leftH, rightH) + 1;
    }
    ```
2. 二元表达式树及其遍历
    ```
            +
        /     \
        +         *
    /   \     /   \
    a     *   +     g
        / \ / \
        b  c *  f
            / \
            d   e 
    ```
    三种遍历及其结果

    + 先序遍历（前缀表达式）: + + a * b c * + * d e f g
    + 中序遍历（中缀表达式）: a + b * c + d * e + f * g
    + 后序遍历（后缀表达式）: a b c * + d e * f + g * +

    But if you consider this scenario:
        
    ```
            +
        /     \
        +         *
    /   \     /   \
    a     *   *     g
        / \ / \
        b  c +  f
            / \
            d   e 
    ```
    中序遍历（中缀表达式）: a + b * c + d + e * f * g, which will calculate the 'd + e * f' firstly, but it intends to calculate (d + e) * f. 

    中序表达式会收到运算符号优先级的影响！结果不是准确的！

    有没有办法处理这个问题呢？当然！在输出左子树的时候先输出‘(’，输出右子树的时候补上一个‘)’就可以完美解决问题！
3. 用两种遍历顺序确定一个二叉树

    注意：必须要有`中序遍历`才可以,因为无法确定左右的边界
    考虑 pre-order: [B, A], post-order: [A, B]
    ```
    B           B
      \        /
       A      A
    ```
    显然，上面两种都有可能，无法确定顺序

    用前序遍历和中序遍历确定一棵二叉树
    1. 根据先序遍历序列第一个结点确定根结点
    2. 根据根结点在中序遍历序列中分割出左右两个子序列
    3. 对左子树和右子树分别递归使用相同的方法继续分解

    ```
    Preorder Sequence: Root | Left Subtree | Right Subtree
                 +------+------+------+
                 | Root | Left | Right|
                 | Node | Sub  | Sub  |
                 +------+------+------+
                     ↓        ↓
                    Continue recursion

    Inorder Sequence: Left Subtree | Root | Right Subtree
                    +-------------+------+--------------+
                    | Left Sub    | Root | Right Sub    |
                    | Tree        | Node | Tree         |
                    +-------------+------+--------------+
                            ↓         ↓
                        Continue recursion
    ```

### Binary Search Tree
+ root->val > root->left->val && root->val < root->right->val
+ 最大元素一定在树最右分支的叶结点上
+ 最小元素一定在树最左分支的叶结点上

#### Find

#### Insert

#### Delete

### Balance Binary Tree
平衡二叉树的高度可以达到log2n吗？

设N(h)为高度为h时的平衡二叉树饿最小结点数，结点数最少时：
`N(h) = N(h - 1) + N(h - 2) + 1`

#### 平衡二叉树的调整
+ ‘RL’
+ ‘RR’
若麻烦结点在发现者的右子树的右边，则称该插入为‘RR’ 插入，因此需要‘RR’旋转。其余同理。
+ ‘LL’
+ ‘LR’


Example: 判断两个序列是否为相同的二叉搜索树
1.  不建树的方法 
    利用数组，先把根提取出来，再分别判别起左子树是否一致&右子树是否一致

2.  建一棵树T，用另一个序列判别是否与T一致

Example: Complete Binary Search Tree

Input: 1 2 3 4 5 6 7 8 9 0
Output: print the nodes with level order traversal

需要考虑的是：
1. 建树 （链表 ？ or 数组 ？）
因为是完全二叉树，所以优先考虑数组，并且输出结果的时候，数组里的结果是已经按照层序遍历排好了的，非常方便。
2. 核心算法
    + 先把输入按大小排序放入数组
    + 然后已知结点总数N，根据完全二叉树的结果可以推算出左子树的大小，则root的位置可以被求出来。
    + 同样的方法对左子树和右子树进行操作

    Process Overview

    Input: A sorted array A.
    Output: A binary search tree T.
    Algorithm: Recursively select the middle element of each subarray as the root node, and build left and right subtrees.

    Steps:

    1. Initialization: Call the solve function with initial parameters.

    2. Recursive Division: Calculate the number of elements in the current subarray, determine the left subtree size, select the middle element as the root, and recursively build the left and right subtrees.
    ```
    void solve(int ALeft, int ARight, int TRoot) {
        /* 初始调用为 solve(0, N-1, 0) */
        
        int n = ARight - ALeft + 1;
        if (n == 0) return;
        
        int L = GetLeftLength(n);  /* 计算出 n 个结点的树其左子树有多少个结点 */
        
        T[TRoot] = A[ALeft + L];
        
        int LeftTRoot = TRoot * 2 + 1;//Node->val从0开始，所以与平时的公式不一样
        int RightTRoot = LeftTRoot + 1;
        
        solve(ALeft, ALeft + L - 1, LeftTRoot);
        solve(ALeft + L + 1, ARight, RightTRoot);
    }
    ```

问题是 怎么知道左子树的结点呢？
考虑如下完全二叉树：
```
          ●               <- h = 1, node = 1, total = 1
        /   \
      ●       ●           <- h = 2, node = 2, total = 3
    /  \     /  \
   ●    ●   ●    ●        <- h = 3, node = 4, total = 7
  / \  / \ / \  / \
 ●  ● ●  

 ```
 完全二叉树（总结点数为N）可以被分为 完美二叉树 + 最后一层的结点数 X

 那么完美二叉树（树高为h）的结点数该如何计算呢？
 
 找一下规律：
 
 + 在根结点上，height = 1, node = 1 = 2^(1-1), total node = 2^1 - 1

 + 下一层，height = 2, node = 2 = 2^(2-1), total node = 2^2 - 1

 + 再下一层，height = 3, node = 4 = 2^(3-1), total node = 2^3 - 1
 ....

 + If `height = H`, there are `2^(H - 1)` nodes on that level and the total nodes are` 2^H - 1`

所以我们可以得到 2^H - 1 + X = N, `H = floor(log2(N+1))` [1]

综上，假设完美二叉树的总高为h, 对于左子树而言，其树高为h-1，所以我们可以得到左子树的总结点数为 `2^(H - 1) - 1 + X` [2]

但是注意：左子树是有范围限制的，当X的大小 > 该层所有结点的一半时，X不能直接加到左子树中去。

X的最小值为0，最大值为2^(H-1), 所以X = min(X, 2^(H-1)) [3]

所以，具体步骤为
1. 计算完美二叉树的高度H. [1]
2. 计算X的大小 [3]
3. 计算左子树的总结点数 [2]


### Minimum Spanning Tree
1. no cycle
2. N node and N-1 edges
3. The sum of weighted edge is the smallest
#### Prim-让一棵小树长大
类似于Dijkstra算法
```C++
void Prim() {
    MST = (s);
    while(1) {
        V = 未收录顶点中dist最小者
        if(这样的V不存在)
        break；
        将V收录进MST: dist[V] = 0
        for(V的每个邻接点W) {
            if(dist[W] != 0) {
                if (edge[V][W] < dist[W]) {
                    dist[W] = edge[V][W];
                    parent[W] = V;
                }
            }
        }
    }
    if(MST 中 顶点的个数不足V个) {
        Error("MST doesn't exist");
    }
}
```
dist[W]初始化为E[s,W] (if W is adjacent to s)or infinite

parent[s] = -1

但注意，图本身可能为空


#### Kruskal - 将森林合并成树
对稀疏图更好
```C++
void Kruskal(Graph G) {
    MST = {}; 
    while (MST 中不到 |V| - 1 条边 && E 中还有边) {
        从 E 中取一条权重最小的边 E(v,w);  // 最小堆
        将 E(v,w) 从 E 中删除;
        if (E(v,w) 不在 MST 中构成回路) {  // 并查集
            将 E(v,w) 加入 MST;
        } else {
            忽略 E(v,w);
        }
    }
    if (MST 中不到 |V| - 1 条边) {
        Error("生成树不存在");
    }
}
```
