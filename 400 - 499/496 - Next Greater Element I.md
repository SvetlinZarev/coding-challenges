# [496. Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/)

## Problem

The next greater element of some element `x` in an array is the first greater
element that is to the right of `x` in the same array.

You are given two distinct 0-indexed integer arrays `nums1` and `nums2`,
where `nums1` is a subset of `nums2`.

For each `0 <= i < nums1.length`, find the index `j` such
that `nums1[i] == nums2[j]` and determine the next greater element of `nums2[j]`
in `nums2`. If there is no next greater element, then the answer for this query
is `-1`.

Return an array `ans` of length `nums1.length` such that `ans[i]` is the next
greater element as described above.

#### Constraints

* `1 <= nums1.length <= nums2.length <= 1000`
* `0 <= nums1[i], nums2[i] <= 10^4`
* All integers in `nums1` and `nums2` are unique.
* All the integers of `nums1` also appear in `nums2`.

#### Examples

```text
Input: nums1 = [4,1,2], nums2 = [1,3,4,2]
Output: [-1,3,-1]
Explanation: The next greater element for each value of nums1 is as follows:
- 4 is underlined in nums2 = [1,3,4,2]. There is no next greater element, so the answer is -1.
- 1 is underlined in nums2 = [1,3,4,2]. The next greater element is 3.
- 2 is underlined in nums2 = [1,3,4,2]. There is no next greater element, so the answer is -1.
```

```text
Input: nums1 = [2,4], nums2 = [1,2,3,4]
Output: [3,-1]
Explanation: The next greater element for each value of nums1 is as follows:
- 2 is underlined in nums2 = [1,2,3,4]. The next greater element is 3.
- 4 is underlined in nums2 = [1,2,3,4]. There is no next greater element, so the answer is -1.
```

## Solutions

### Brute Force

```rust
pub fn next_greater_element(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
    assert!(nums1.len() <= nums2.len());

    let mut result = Vec::with_capacity(nums1.len());
    'next: for idx in 0..nums1.len() {
        let base_num = nums1[idx];

        let mut found = false;
        for &n in nums2.iter() {
            if n == base_num {
                found = true;
                continue;
            }

            if found {
                if n > base_num {
                    result.push(n);
                    continue 'next;
                }
            }
        }

        result.push(-1);
    }

    result
}
```

And we can skip some iterations:

```rust
pub fn next_greater_element(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
    assert!(nums1.len() <= nums2.len());

    let mut jumps = HashMap::with_capacity(nums2.len() + nums2.len() / 2);
    for (idx, &n) in nums2.iter().enumerate() {
        jumps.insert(n, idx);
    }

    let mut result = Vec::with_capacity(nums1.len());
    'next: for base in nums1 {
        let idx = *jumps.get(&base).unwrap();
        for n in nums2.iter().copied().skip(idx) {
            if n > base {
                result.push(n);
                continue 'next;
            }
        }

        result.push(-1);
    }

    result
}
```

### Using Monotonic Stack

```rust
use std::collections::HashMap;

pub fn next_greater_element(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
    let mut stack = Vec::with_capacity(nums2.len());
    let mut map = HashMap::with_capacity(nums2.len());

    for n in nums2 {
        while let Some(&top) = stack.last() {
            if n <= top {
                break;
            }

            map.insert(top, n);
            stack.pop();
        }

        stack.push(n);
    }

    let mut result = Vec::with_capacity(nums1.len());
    for n in nums1 {
        let value = map.get(&n).copied().unwrap_or(-1);
        result.push(value);
    }
    result
}
```