# [665. Non-decreasing Array](https://leetcode.com/problems/non-decreasing-array/)

## Problem

### Description

Given an array `nums` with `n` integers, your task is to check if it could
become non-decreasing by modifying at most one element.

We define an array is non-decreasing if `nums[i] <= nums[i + 1]` holds for
every `i` (0-based) such that `(0 <= i <= n - 2)`.

### Constraints

* `n == nums.length`
* `1 <= n <= 104`
* `-10^5 <= nums[i] <= 10^5`

### Examples

```text
Input: nums = [4, 2, 3]
Output: true
Explanation: You could modify the first 4 to 1 to get a non-decreasing array.
```

```text
Input: nums = [4,2,1]
Output: false
Explanation: You can't get a non-decreasing array by modify at most one element.
```

```text
Input: nums = [-1,4,2,3]
Output: true
```

```text
Input: nums = [3,4,2,3]
Output: false
```

## Solutions

### Naive solution

Loop until a smaller element is found, then check if skipping either `idx`
or `idx-1` makes the array non-decreasing, by looping through the rest of the
elements

The issue with this approach is that we may need to loop through the remainder
of the array twice.

```rust
pub fn check_possibility(nums: &[i32]) -> bool {
    for idx in 1..nums.len() {
        if nums[idx] < nums[idx - 1] {
            // skip left
            if idx - 1 == 0 {
                // we need to skip idx-0
                if nums[1..].windows(2).all(|w| w[1] >= w[0]) {
                    return true;
                }
            } else {
                if is_non_decreasing(nums, idx - 2, idx, idx - 1) {
                    return true;
                }
            }

            // skip right
            if is_non_decreasing(nums, idx - 1, idx + 1, idx) {
                return true;
            }

            return false;
        }
    }

    true
}

fn is_non_decreasing(nums: &[i32], mut l: usize, mut r: usize, skip: usize) -> bool {
    while r < nums.len() {
        if r == skip {
            r += 1;
            continue;
        }

        if l == skip {
            l += 1;
            continue;
        }

        if nums[r] < nums[l] {
            return false;
        }

        r += 1;
        l += 1;
    }

    true
}
```

### Optimised naive solution

1. Loop until we find a decreasing pair `(left, right)`
2. Check if we can skip either `left` or `right` without looping through the
   rest of the array.
3. If we can skip either `left` or `right` check if there is another decreasing
   pair

```rust
pub fn check_possibility<T: AsRef<[i32]>>(nums: T) -> bool {
    let nums = nums.as_ref();

    for idx in 1..nums.len() {
        if nums[idx - 1] > nums[idx] {
            if !(can_skip_left(nums, idx) || can_skip_right(nums, idx)) {
                return false;
            }

            return nums[idx + 1..].windows(2).all(|w| w[0] <= w[1]);
        }
    }

    true
}

fn can_skip_left(nums: &[i32], idx: usize) -> bool {
    assert!(!nums.is_empty());
    // idx should point to the right element, so it should not
    // be possible to have a value of 0
    assert!(idx > 0);

    // example: [5, 6, (7, 3), 8, 9]
    //                  ^  ^
    //                  |  idx (right)
    //                 idx-1 (left)
    // so after skipping "left", we have the array [5, 6, 3, 8, 9]

    // Make sure that the idx-1 (i.e. left) is not the first element
    // in the array. Because if it is, we don't have to check the
    // previous element
    if idx > 1 {
        if nums[idx - 2] > nums[idx] {
            return false;
        }
    }

    // We also have have to check if "right" is <= than the next element
    // if there is a next element
    if idx < nums.len() - 1 {
        if nums[idx] > nums[idx + 1] {
            return false;
        }
    }

    true
}

fn can_skip_right(nums: &[i32], idx: usize) -> bool {
    assert!(!nums.is_empty());
    // idx should point to the right element, so it should not
    // be possible to have a value of 0
    assert!(idx > 0);

    // example: [5, 6, (7, 3), 8, 9]
    //                  ^  ^
    //                  |  idx (right)
    //                 idx-1 (left)
    // so after skipping "right", we have the array [5, 6, 7, 8, 9]

    // Check if "left" is <= than the next element after "right",
    // if there is such element
    if idx < nums.len() - 1 {
        if nums[idx - 1] > nums[idx + 1] {
            return false;
        }
    }

    true
}
```

### Similar to "two pointers"

```rust
pub fn check_possibility<T: AsRef<[i32]>>(nums: T) -> bool {
   let nums = nums.as_ref();
   if nums.is_empty() {
      return true;
   }

   let mut l = 0;
   let mut r = nums.len() - 1;

   // move the left pointer
   while l < r && nums[l] <= nums[l + 1] {
      l += 1;
   }

   // move the right pointer
   while l < r && nums[r - 1] <= nums[r] {
      r -= 1;
   }

   // The L and R pointers point to the same element, which
   // means that here are no decreasing element pairs
   if l == r {
      return true;
   }

   // If the gap between L and R is more than 1 element, it
   // means that there are more than one decreasing pair
   if l + 1 != r {
      return false;
   }

   // example: [5, 6, (7, 3), 8, 9]
   //                  ^  ^
   //                  |  idx (right)
   //                 idx-1 (left)
   // * after skipping "left", we have the array [5, 6, 3, 8, 9]
   // * after skipping "right", we have the array [5, 6, 7, 8, 9]

   // Try skipping "left" and "right" and handle the corner cases
   // when there are no elements before L and after R:

   // Examples:
   // * [4, 2, 3] => L=0, R=1, skipping L, but no need to check prev element
   // * [4, 5, 3] => L=1, R=2, skipping R, but no need to check next element
   if l == 0 || r == nums.len() - 1 {
      return true;
   }

   // * [3, 4, 2, 3] => L=1, R=2, cannot skip anything
   nums[l - 1] <= nums[r] || nums[l] <= nums[r + 1]
}
```