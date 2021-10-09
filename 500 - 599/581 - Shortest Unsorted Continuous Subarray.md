# [581. Shortest Unsorted Continuous Subarray](https://leetcode.com/problems/shortest-unsorted-continuous-subarray/)

## Problem

Given an integer array `nums`, you need to find one continuous subarray that if
you only sort this subarray in ascending order, then the whole array will be
sorted in ascending order.

Return the shortest such subarray and output its length.

##### Constraints

* `1 <= nums.length <= 104`
* `-105 <= nums[i] <= 105`

#### Example

* Input: nums = `[2,6,4,8,10,9,15]`
* Output: 5

* Explanation: You need to sort `[6, 4, 8, 10, 9]` in ascending order to make
  the whole array sorted in ascending order.

## Solutions

### Using sorting

We can make a copy of the original array and sort it. Then we have to find the
first and last indexes where the values differ:

* Time: 4ms
* Memory: 2.1MB
* Time complexity: O(log n)
* Space complexity: O(n)

```rust
pub fn find_unsorted_subarray(nums: Vec<i32>) -> i32 {
    let mut sorted = nums.clone();
    sorted.sort_unstable();

    nums.iter()
        .copied()
        .zip(sorted.iter().copied())
        .enumerate()
        .find(|(_, (a, b))| a != b)
        .and_then(|(start, _)| {
            nums.iter()
                .copied()
                .zip(sorted.iter().copied())
                .enumerate()
                .rfind(|(_, (a, b))| a != b)
                .map(|(end, _)| end - start + 1)
        })
        .map(|len| len as i32)
        .unwrap_or(0)
}
```

### Using a monotonically increasing/decreasing stack

* Time 8 ms
* Memory: 2.1 MB
* Time complexity: O(n)
* Space complexity: O(n)

```rust
pub fn find_unsorted_subarray(nums: Vec<i32>) -> i32 {
    if nums.len() <= 1 {
        return 0;
    }

    let mut stack = Vec::with_capacity(4);
    let mut left = nums.len();

    // monotonically increasing stack
    for idx in 0..nums.len() {
        while let Some(&top) = stack.last() {
            if nums[top] <= nums[idx] {
                break;
            }
            stack.pop();
            left = left.min(top);
        }

        stack.push(idx);
    }

    // If we never encounter an element where nums[i] > nums[i + 1],
    // then it means the array is sorted, so there is no point in 
    // calculating the array end index
    if left == nums.len() {
        return 0;
    }

    stack.clear();
    let mut right = 0;

    // monotonically decreasing stack
    for idx in (0..nums.len()).rev() {
        while let Some(&top) = stack.last() {
            if nums[top] >= nums[idx] {
                break;
            }
            stack.pop();
            right = right.max(top);
        }
        stack.push(idx);
    }

    if right <= left {
        return 0;
    }

    (right - left + 1) as i32
}
```
