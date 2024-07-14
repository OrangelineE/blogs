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



