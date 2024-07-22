# Binary Search
## Find the target
[left, right)

int mid = left + (right - left)/2; // incase overflow


## Find the Left Boundary

For the left boundary, it returns `left`.
```C++
    int findLeftBoundary(const std::vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size();  // right is exclusive

        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] < target) {
                left = mid + 1;
            } else {
                right = mid;
            }
        }

        // After the loop, left should be the first occurrence of the target
        if (left < nums.size() && nums[left] == target) {
            return left;
        }

        // If the target is not found, return -1
        return -1;
    }
```

## Find the Right Boundary

For the right boundary, it returns `left - 1`.

```C++
int findRightBoundary(const std::vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size();  // right is exclusive

    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] <= target) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }

    // After the loop, left is one past the last occurrence of the target
    if (left > 0 && nums[left - 1] == target) {
        return left - 1;
    }

    // If the target is not found, return -1
    return -1;
}
```
