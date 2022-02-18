# [349. Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/)

## Problem

Given two integer arrays `nums1` and `nums2`, return an array of their
intersection. Each element in the result must be unique and you may return the
result in any order.

#### Constraints

* `1 <= nums1.length, nums2.length <= 1000`
* `0 <= nums1[i], nums2[i] <= 1000`

#### Examples

```text
Input: nums1 = [1,2,2,1], nums2 = [2,2]
Output: [2]
```

```text
Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
Output: [9,4]
Explanation: [4,9] is also accepted.
```

## Slutions

### Set intersection

```rust
use std::collections::HashSet;

pub fn intersection(a: Vec<i32>, b: Vec<i32>) -> Vec<i32> {
    let (small, mut large) = if a.len() <= b.len() { (a, b) } else { (b, a) };

    let mut set = small.into_iter().collect::<HashSet<_>>();
    large.retain(|x| set.remove(x));

    large
}
```

### Binary search

```rust
pub fn intersection(a: Vec<i32>, b: Vec<i32>) -> Vec<i32> {
    let (mut small, mut large) = if a.len() <= b.len() { (a, b) } else { (b, a) };

    small.sort_unstable();
    large.sort_unstable();
    small.dedup();
    
    let mut answer = Vec::with_capacity(small.len());

    for x in small.iter() {
        if large.binary_search(x).is_ok() {
            answer.push(*x);
        }
    }

    answer
}
```

## Related Problems

* [350. Intersection of Two Arrays II](350%20-%20Intersection%20of%20Two%20Arrays%20II.md)