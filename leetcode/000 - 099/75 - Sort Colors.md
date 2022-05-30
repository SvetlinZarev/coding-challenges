# [75. Sort Colors](https://leetcode.com/problems/sort-colors/)

## Problem

Given an array `nums` with `n` objects colored red, white, or blue, sort them
in-place so that objects of the same color are adjacent, with the colors in the
order red, white, and blue.

We will use the integers 0, 1, and 2 to represent the color red, white, and
blue, respectively.

You must solve this problem without using the library's sort function.

#### Examples

```text
Input: nums = [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

```text
Input: nums = [2,0,1]
Output: [0,1,2]
```

## Solutions

### Using two pointers

The general idea is to put the 0s on the left and the 2s on the right. What's
left is the 1s in the middle

```rust
pub fn sort_colors(nums: &mut Vec<i32>) {
    let mut r = 0;
    let mut b = nums.len() - 1;

    let mut idx = r;

    while r < b && idx <= b {
        if nums[idx] == 0 {
            if r == idx {
                idx += 1;
            } else {
                nums.swap(r, idx);
            }
            r += 1;
        } else if nums[idx] == 2 {
            nums.swap(b, idx);
            b -= 1;
        } else {
            idx += 1;
        }
    }
}
```

### Using counting sort

```rust
pub fn sort_colors(nums: &mut Vec<i32>) {
    let mut rwb = [0, 0, 0];
    for n in nums.iter().copied() {
        rwb[n as usize] += 1;
    }

    let mut idx = 0;

    while idx < rwb[0] {
        nums[idx] = 0;
        idx += 1;
    }

    while idx < rwb[0] + rwb[1] {
        nums[idx] = 1;
        idx += 1;
    }

    while idx < nums.len() {
        nums[idx] = 2;
        idx += 1;
    }
}
```