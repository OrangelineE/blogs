# Introduction
1. 数据结构的正确使用可以使算法效率大大提升
2. 在数据量超大时，循环的空间使用比递归要好，递归会使堆栈爆炸
3. 算法效率：对于多项式 f(x) = a0 + a1 * x + a2 * x^2 + ... + an * x^n, it's dumb to calculate the result by:
```C
double f(int n, double a[], double x) {
    int i;
    double p = a[0];
    for(int i = 1; i <= n; i++) {
        p += a[i] * pow(x, n);
    }
    return p;
}
```
更高效的方法是利用因式分解，将f(x)化成 a0 + x*(a1 + x*(a2 + x*(a3 + ...(a(n-1) + x*(an))...)))
```C
double f(int n, double a[], double x) {
    int i;
    double p = a[n];
    for(int i = n; i>= 1; i--) {
        p += a[i - 1] + p*x;
    }
    return p;
}
```

需要注意的是！`尾递归非常容易转化为迭代(iterate)，所以任何尾递归的函数都可以转化为迭代进行优化`.

## Algorithm Complexity
1 < log n < n < n * logn < n^2 < ... < 2^n < n!

## Example:求最大子列和
给定一个整数序列 {a0, a1, a2, a3, a4, ..., an},求其最大子序列的和
1. Brute Force: 将每个子列的和都求出来: at least O(n^2)
2. Divide Conquer: O(n * logn)
![dv](https://drive.google.com/file/d/14XbyRqQpBuQDEhmCbqLipT0bBQKtp0Au/view?usp=drive_link)
3. Immediate Processing: O(n)
![algo](https://drive.google.com/file/d/14XbyRqQpBuQDEhmCbqLipT0bBQKtp0Au/view?usp=drive_link)

# List:线性表
## Feature
1. 数据对象集：n个元素构成的有序序列
## Implementation
1. 顺序存储：数组
2. 链式存储：链表

# Heap:堆
用完全二叉树进行存储
+ MaxHeap, the root->val > left-> && root->val > right->val
+ MinHeap

堆的建立（该题以最大堆举例）
+ 方法1: 通过插入操作，把N个元素一个一个相继插入到一个初始为空的堆中，时间复杂度为O(NlogN)
+ 方法2: 在线性时间复杂度下建立最大堆 O(N)

    1. 将N个元素按照输入顺序存入，先满足完全二叉树的结构特性
    2. 调整各结点位置，以满足最大堆的有序特性
## Huffman Tree
A Huffman tree is a binary tree constructed for the purpose of optimal encoding of symbols such that the most frequently occurring symbols have the shortest codes. It is built using a specific algorithm known as Huffman coding, named after David A. Huffman, who invented it while a Ph.D. student at MIT.

### Construction
每次把权值最小的两棵二叉树合并。
如何选取两个最小的？利用堆！

### Feature
Characteristics of Huffman Trees
1. No nodes of degree 1: A Huffman tree does not have any nodes with exactly one child.
2. Number of nodes: A Huffman tree with n leaf nodes has a total of 2n−1 nodes.
3. Swapping subtrees: Swapping the left and right subtrees of any non-leaf node in a Huffman tree results in another valid Huffman tree.
4. Different structures with the same weights:
For a given set of weights {w0, w1, ..., w2}​, there may exist different Huffman trees with different structures.

    Example with weights {1,2,3,3}:
    ``` 
       Tree 1             
         ●
        / \
       3   ●
          / \
         ●   3
        / \
       1   2


        Tree 2 
          ●
         / \
        ●   3
       / \
      ●   3
     / \
    1   2
    ```
### Example: 打印堆中的路径
因为堆是一棵完全二叉树，所以可以很完美的用数组来表示。而且要打印从叶子结点到根结点的路径，用数组也可以很方便的输出（父结点 = 子结点/2）

so the problem is 
1. build the heap using array
2. print the path


# Union-Find
是一个从下往上寻找的结构

## 存储结构
1. 树
2. 数组
```C++
struct disjointSet{
    vector<int> parents;
    vector<int> sz;
    disjoint(int size) {
        parents.resize(size);
        sz = size;
        for(int i = 0; i < size; i++) {
            parents[i] = i;
        }
    }
}
```
## Functions
1. find()
2. union()

