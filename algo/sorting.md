# Sorting
+ By default, it is sorting integers in an increasing order
+ stability(稳定性): 任意两个相等的数据，排序前后的相对位置不会发生改变
+ 没有一种排序在任何情况下都是最好的
  
## Bubble Sort
+ 每次从上到下，比较相邻两个的大小。如果前面的大于后面的，则交换位置
+ 假设数组 nums 长度为 n：
  
    第一次排序，可以保证 nums[n-1] 存放正确；

    第二次排序就不需要再去n-1的地方遍历了，即在n-2的地方停止即可
+ 好处：
  + 简单
  + 如果数据是由链表存储，也可用bubble sort进行排序
  + 只有严格大于才进行交换，所以是`稳定的`

```C++
void bubbleSort(vector<int> arr, int n) {
    for(int p = n-1; p >= 0; p--) {
        int flag = 0;
        for(int i = 0; i < p; i++) {//一次冒泡
            if(nums[i] > nums[i+1]) {
                swap(numus[i], nums[i+1])；
                flag = 1;//发生了交换
            }
            
        }
        if(flag == 0) {
            break;//全程无交换，意味着数组已经排序完毕
        }
    }
}
```
最好：顺序 T= O（N）
最坏：逆序 T= O（N^2）

## Insertion Sort 
+ 想象你在打牌
+ 假设一开始手里有一张牌（index = 0）
+ 把后面的牌从手上的牌的最后一张开始向前比较，如果该牌 < 比较的牌，说明它还要往前去，所以要让比较的牌让出空位，然后继续比较，直到该牌 >= 比较的牌。
+ 好处：
  + 当两张牌相等时，不会交换位置，所以也是`稳定的`
```C++
void insertionSort(vector<int> arr, int n) {
    for(int p = 1; p < n; p++) {
        int tmp = arr[p]; //摸下一张牌
        int i；
        for( i = p; i > 0 && arr[i - 1] > tmp; i--) {
            arr[i] = arr[i - 1];//移出空位
        }
        arr[i] = tmp;//新牌落位
    }
}
```
最好：顺序 T= O（N）
最坏：逆序 T= O（N^2）


## Inversion 逆序对
对于下标i < j, 如果 A[i] > A[j], 则称（i， j）是一对逆序对。

`交换2个相邻元素正好消去一个逆序对！`

‘I’ represents the number of inversions:

插入排序： T(N, I) = O(N+I) —— 如果序列基本有序，则插入排序简单且高效

定理：
+ 任意N个不同元素组成的序列平均具有 N(N-1)/4 个逆序对
+ 任何仅以交换相邻两元素来排序的算法，其平均时间复杂度是 omega(N^2)

所以如果要提高算法效率，我们必须
+ 每次消去不止一个逆序对
+ 每次交换相隔较远的两个元素

## Shell Sort
+ 定义增量序列 D(m) > D(m-1) > ... > D1 = 1
+ 对每个D(k)进行 “D(k)-间隔” 排序（k = m, m-1, ..., 1）
+ 注意： “D(k)-间隔”有序的序列在“D(k - 1)-间隔”排序后，仍然保持“D(k)-间隔”有序

原始希尔排序 D(m) = floor(n/2), D(k) = floor(D(k+1)/2)
```C++
void shellSort(vector<int> arr, int n) {
    for(int d = n/2; d > 0; d /= 2) {//希尔增量序列
       for(int p = d; p < n; p++) {//插入排序
            int tmp = arr[p];
            for(int i = p; i>= d && arr[i - d] > tmp; i -= d) {
                a[i] = a[i -d];
            }
            a[i] = tmp;
       }
    }
}
```
最坏：T = theta(N^2)

若增量元素不互质，前面的增量序列可能不起作用

更多增量序列：
1. Hibbard
2. Sedgewick

## Selection Sort

```C++
void selectionSort(vector<int> arr, int n) {
    for(int i = 0; i < n; i++) {
        int minpos = scanForMin(arr, i, n-1); //从arr[i]-arr[n-1]中找最小元，并将其位置赋给minPosition
        swap(arr[i], arr[minpos]); //将未排序部分的最小元换到有序部分的最后位置
    }
}
```
时间复杂度取决于scanForMin，无论如何
T = theta(N^2)

## Heap Sort
MinHeap 可用于快速寻找下一个最小的数字

算法1：

```C++
void heapSort(vector<int> arr, int n) {
    BuildHeap(arr); //O(N)
    vector<int> tmp;
    for(int i = 0; i < n; i++) {
        tmp[i] = deletedMin(arr[i]); //O(logN)
    }
    for(int i = 0; i < N; i++) {
        arr[i] = tmp[i];
    }
    
}
```
T = O(NlogN), but needs O(N) space

算法2：

```C++
void heapSort(vector<int> arr, int n) {
    for(int i = n/2; i >= 0; i--) {//Build MaxHeap
        percDown(arr, i, n);
    }
    vector<int> tmp;
    for(int i = n-1; i > 0; i--) {
        swap(arr[0], arr[i]); //Delete max, always put the max element to the last position
        percDown(arr, 0, i); //把剩下的元素继续调整成最大堆

    }
}
```
T = O(NlogN)

## Merge Sort
+ 好处
  + 稳定
```C++
vector<int> sortArray(vector<int>& nums) {
        tmp.resize(nums.size());
        mergeSort(nums, 0, nums.size() - 1);
        return nums;
}
void mSort(vector<int>& nums, int start, int end) {

    if (start >= end) return;
    int mid = (start + end) >> 1;
    mSort(nums, start, mid);
    mSort(nums, mid + 1, end);
    merge(nums, start, mid, end);
}
void merge(vector<int>& nums, int start, int mid, int end) {
    int i = start;
    int j = mid + 1;
    int cnt = 0;
    while(i <= mid && j <= end) {
        if (nums[i] <= nums[j]) {
            tmp[cnt++] = nums[i++];
        }
        else {
            tmp[cnt++] = nums[j++];
        }
    }
    while(i <= mid) {
        tmp[cnt++] = nums[i++];
    }
    while(j <= end) {
        tmp[cnt++] = nums[j++];
    }
    for(int i = 0; i < cnt; i++) {
        nums[i + start] = tmp[i];
        // cout << "tmp [" << i + start << "] = " << tmp[i+start] << endl;
    }
}
   
```

T = O(NlogN)

## Quick Sort

+ 分而治之
  + 分：每次选择一个主元（pivot）
  + 治：将剩下的元素分成两个集合，大于等于pivot的 和 小于等于pivot的


```C++
void quickSort(vector<int> arr, int n) {

}

```
最好情况：每次正好中分，T = O(NlogN)

如何选 主元 至关重要
+ rand()不便宜！时间cost大
+ 取头，中，尾的中位数

```C++
void median3(vector<int> arr, int left, int right) {
    int mid = (left + right)/2;
    if(arr[left] > arr[mid]) {
        swap(arr[left], arr[mid]);
    }
    if(arr[left] > arr[right]) {
        swap(arr[left], arr[right]);
    }
    if(arr[mid] > arr[right]) {
        swap(arr[mid], arr[right]);
    }
    /* now it is arr[left] <= arr[mid] <= arr[right]*/

    swap(arr[mid], arr[right - 1]); //将pivot藏在右边
    //这时只需要考虑arr[left+1]...arr[right-2]
return arr[right - 1]
}


void quickSort(vector<int> arr, int left, int right) {
    int pivot = median3(arr, left, right);
    int i = left, j = right - 1;
    for(; ;) {
        while(arr[++i] < arr[pivot]) {

        }
        while(arr[--j] > arr[pivot]) {
            
        }
        if(i < j) {
            swap(arr[i], arr[j]);
        } else {
            break;
        }
    }
    quickSort(arr, left, i - 1);
    quickSort(arr, i + 1, right);
}
```

## Table Sort

以上算法涉及频繁移动两两元素，但当元素本身非常大时，情况会变得十分复杂，这时引入我们的表排序。

间接排序

定义一个指针数组作为表（table），此处指针并非真的指针，而是数组下标

若真的要物理排序
+ 已知：N个数字的排列由若干独立的环组成
+ 可以根据这个，来一个一个环来进行

## Bucket Sort
Problem Statement:
Suppose we have N students, and their scores are integers between 0 and 100 (thus there are 𝑀=101 different possible scores). How can we sort the students by their scores in linear time?

Illustration:
+ 'count' array is used to keep track of the number of students with each score.
+ Example 'count' array shows scores from 0 to 100.
+ Students' scores are inserted into the corresponding 'count' index.
+ Finally, the sorted students are output based on the 'count' array.

```C++
void Bucket_Sort(ElementType A[], int N) {
    count[i]初始化;
    while (读入一个学生成绩grade)
        将该生插入count[grade]链表;
    for (i = 0; i < M; i++) {
        if (count[i]) {
            输出整个count[i]链表;
        }
    }
}
```

## Radix Sort（基数排序）
Problem Statement:

Suppose we have N=10 integers, and each integer's value is between 0 and 999 (thus there are M=1000 different possible values). Is it possible to sort them in linear time?

Input Sequence:
64, 8, 216, 512, 27, 729, 0, 1, 343, 125

Sorting using "Least Significant Digit" (LSD) first:
```
Pass 1 (Sorting by the least significant digit):

Bucket	Numbers
0	    0
1	
2	
3	    343
4	    64
5	    125
6	    216
7	    27
8	    8
9	    729

Pass 2 (Sorting by the second least significant digit):
Bucket	Numbers
0	    0, 1，8
1	    512
2	    125，27，729
3	    343
4	    
5	
6	    64
7	    
8	
9	    

Pass 3 (Sorting by the most significant digit):
Bucket	Numbers
0	    0, 1，8，27，64
1	    125
2	    216
3	    343
4	
5	    512
6	    
7	    729
8	    
9	
```
基数排序也有`"Most Significant Digit" (MSD)`的排序

基数排序也很适合处理`多关键字`的排序
+ 可以理解为每个关键字就是进制上的某一位，他们的priotity来排是哪一位

### Sorting Algorithms Complexity and Stability

**Sorting Algorithm**:
- **Average Time Complexity**: \( O(N^2) \)
- **Worst-Case Time Complexity**: \( O(N^2) \)
- **Space Complexity**: \( O(1) \)
- **Stability**: Unstable

**Bubble Sort**:
- **Average Time Complexity**: \( O(N^2) \)
- **Worst-Case Time Complexity**: \( O(N^2) \)
- **Space Complexity**: \( O(1) \)
- **Stability**: Stable

**Straight Insertion Sort**:
- **Average Time Complexity**: \( O(N^2) \)
- **Worst-Case Time Complexity**: \( O(N^2) \)
- **Space Complexity**: \( O(1) \)
- **Stability**: Stable

**Shell Sort**:
- **Average Time Complexity**: \( O(N^{1.3}) \)
- **Worst-Case Time Complexity**: \( O(N^2) \)
- **Space Complexity**: \( O(1) \)
- **Stability**: Unstable

**Heap Sort**:
- **Average Time Complexity**: \( O(N \log N) \)
- **Worst-Case Time Complexity**: \( O(N \log N) \)
- **Space Complexity**: \( O(1) \)
- **Stability**: Unstable

**Quick Sort**:
- **Average Time Complexity**: \( O(N \log N) \)
- **Worst-Case Time Complexity**: \( O(N^2) \)
- **Space Complexity**: \( O(\log N) \)
- **Stability**: Unstable

**Merge Sort**:
- **Average Time Complexity**: \( O(N \log N) \)
- **Worst-Case Time Complexity**: \( O(N \log N) \)
- **Space Complexity**: \( O(N) \)
- **Stability**: Stable

**Radix Sort**:
- **Average Time Complexity**: \( O(P(N + B)) \)
- **Worst-Case Time Complexity**: \( O(P(N + B)) \)
- **Space Complexity**: \( O(N + B) \)
- **Stability**: Stable
