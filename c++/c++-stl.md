# Heap

## priority_queue
### Default
The default pq is `max_heap`, meaning the largest element is always at the top.

```C++
std::priority_queue<int> pq;
```

The default comparator for priority_queue is `std::less<T>`, which compares elements in such a way that the largest element has the highest priority.

## make_heap(begin_iterator, end_iterator, (comp))
By default, it generates the `max` heap, but we can use a custom comparator to change it into the min heap.

Transforms a range into a heap:
```C++
#include <iostream>
#include <algorithm>
#include <vector>

int main() {
    std::vector<int> v = {10, 20, 5, 15, 30};

    std::make_heap(v.begin(), v.end());

    std::cout << "Max element (heap top): " << v.front() << std::endl;  // Output: 30

    return 0;
}
```
Time complexity: O(N), Auxilary Space: O(1)

Any other functions are like:
+ `push heap(begin_interator, end_iterator)`: Arrange the heap after insertion at the end

    Time complexity: O(logN)
+ `pop_heap(begin_interator, end_iterator)`: Moves the max element at the end for deletion

    Time complexity: O(logN)

    ps: Then we can use `pop_back()` of the vector class to remove that out.
+ `sort_heap`: Sort the elements of the max heap to ascending order.

    Time complexity: O(NlogN)
+ `is_heap`: check if the given range is max_heap.

    Time complexity: O(N)


## Comparator
### How to write a comparator?
Three ways:
1. Function Pointer
2. Lamda Expression
3. Functors(Function Object)

#### Function Pointer

```C++
#include <iostream>
#include <queue>
#include <vector>

bool compare(int a, int b) {
    return a > b;  // For a min-heap
}

int main() {
    std::priority_queue<int, std::vector<int>, bool(*)(int, int)> pq(compare);

    pq.push(10);
    pq.push(20);
    pq.push(5);

    while (!pq.empty()) {
        std::cout << pq.top() << " ";
        pq.pop();
    }
    // Output: 5 10 20

    return 0;
}
```

#### Lamda Expression

Customerize the comparator by using `Lamda` function.
The following code shows the min_heap.
```C++
auto cmp = [](int left, int right) { return left > right; };

priority_queue<int, vector<int>, decltype(cmp)> pq(cmp);
```

#### Functors(FUnction Object)
It can also be achieved through functions pointer

```C++
struct Compare {
    bool operator()(const int& a, const int& b) const {
        return a > b;  // For a min-heap
    }
};

int main() {
    std::priority_queue<int, std::vector<int>, Compare> pq;

    pq.push(10);
    pq.push(20);
    pq.push(5);

    while (!pq.empty()) {
        std::cout << pq.top() << " ";
        pq.pop();
    }
    // Output: 5 10 20

    return 0;
}
```
`!!! Attention!!!!`

When you apply the above `Comparator` class into `sort`, it is different from the way you declare the priority_queue!

You need one more `()`! 
Same for the `make_heap()`.
```C++
#include <iostream>
#include <algorithm>
#include <vector>

struct Compare {
    bool operator()(const int& a, const int& b) const {
        return a > b;  // Sort in descending order
    }
};

int main() {
    std::vector<int> vec = {10, 20, 5, 15, 30};

    std::sort(vec.begin(), vec.end(), Compare());

    for (int num : vec) {
        std::cout << num << " ";
    }
    // Output: 30 20 15 10 5

    return 0;
}
```

### Opposite Comparator Behavior in Heap

The behavior of comparators in heaps, especially in the context of priority_queue, can indeed seem `counterintuitive` at first. 

When you provide a custom comparator to a priority_queue, it defines what `higher priority` means for the elements. This priority is `inversely` related to the natural order used in sorting algorithms.

### Example
Comparator return `a < b`;

**In Sorting Context**

return a < b means:

Elements are sorted in `ascending` order.
Smaller elements come first.

**In priority_queue Context**

return a < b means:

Elements with "`higher priority`" are those where a < b evaluates to `true`.

This actually makes the `larger` elements come to the top of the heap because the comparator determines the element with the `highest` priority.

Essentially, return a < b makes the priority_queue act as a max-heap, placing the largest elements at the top.