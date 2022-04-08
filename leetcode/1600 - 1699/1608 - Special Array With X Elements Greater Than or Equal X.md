# [1608. Special Array With X Elements Greater Than or Equal X](https://leetcode.com/problems/special-array-with-x-elements-greater-than-or-equal-x/)

## Problem

### Description

You are given an array `nums` of non-negative integers. `nums` is considered
special if there exists a number `x` such that there are exactly `x` numbers
in `nums` that are greater than or equal to `x`.

Notice that `x` does not have to be an element in `nums`.

Return `x` if the array is special, otherwise, return `-1`. It can be proven
that if `nums` is special, the value for `x` is unique.

### Constraints

* `1 <= nums.length <= 100`
* `0 <= nums[i] <= 1000`

### Examples

```text
Input: nums = [3,5]
Output: 2
Explanation: There are 2 values (3 and 5) that are greater than or equal to 2.
```

```text
Input: nums = [3,5]
Output: 2
Explanation: There are 2 values (3 and 5) that are greater than or equal to 2.
```

```text
Input: nums = [0,4,3,0,4]
Output: 3
Explanation: There are 3 values that are greater than or equal to 3.
```

## Solutions

### Linear search

```rust
pub fn special_array(mut nums: Vec<i32>) -> i32 {
    assert!(nums.len() > 0);

    nums.sort_unstable();

    let mut prev = -1;
    for (idx, &num) in nums.iter().enumerate() {
        let remaining = (nums.len() - idx) as i32;

        if remaining <= num && remaining > prev {
            return remaining;
        }

        prev = num;
    }

    -1
}
```

### Binary Search

The idea is instead of trying all possible candidates for X, to do a binary
search. In order to do that we also need to do a second binary search to find
the index of the first element that is equal or greater than X.

```rust
use std::cmp::Ordering;

pub fn special_array(mut nums: Vec<i32>) -> i32 {
    nums.sort_unstable();

    let mut lo = 0;
    let mut hi = nums.len();

    while lo <= hi {
        let x = lo + (hi - lo) / 2;

        match search(&nums, x as i32) {
            // If there are no greater elements, the we need to try a smaller X if possible
            None => match x {
                0 => break,
                _ => hi = x - 1,
            },
            Some(idx) => match x.cmp(&(nums.len() - idx)) {
                // We have found our X
                Ordering::Equal => return x as i32,

                // There are more bigger than X elements, so we need to try a bigger X
                Ordering::Less => lo = x + 1,

                // SAFETY: cannot underflow because of the `match` above -> X must be greater than (len - idx)
                // There are too few bigger than X elements, so we need to try a smaller X
                Ordering::Greater => hi = x - 1,
            },
        }
    }

    // There is no such number X that satisfies the requirements
    -1
}

// Find the index of the first element that is equal or greater than X
fn search(array: &[i32], t: i32) -> Option<usize> {
    let mut lo = 0;
    let mut hi = array.len() - 1;

    while lo < hi {
        let mid = lo + (hi - lo) / 2;

        if array[mid] >= t {
            hi = mid;
        } else {
            lo = mid + 1;
        }
    }

    if array[hi] >= t {
        Some(hi)
    } else {
        None
    }
}
```

## Related Problems

* [34. Find First and Last Position of Element in Sorted Array](/leetcode/000%20-%20099/34%20-%20Find%20First%20and%20Last%20Position%20of%20Element%20in%20Sorted%20Array.md)