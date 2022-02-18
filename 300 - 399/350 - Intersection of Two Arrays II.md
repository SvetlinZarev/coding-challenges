# [350. Intersection of Two Arrays II](https://leetcode.com/problems/intersection-of-two-arrays-ii/)

## Problem

Given two integer arrays `nums1` and `nums2`, return an array of their
intersection. Each element in the result must appear as many times as it shows
in both arrays and you may return the result in any order.

#### Constraints

* `1 <= nums1.length, nums2.length <= 1000`
* `0 <= nums1[i], nums2[i] <= 1000`

#### Examples

```text
Input: nums1 = [1,2,2,1], nums2 = [2,2]
Output: [2,2]
```

```text
Input: nums1 = [4,9,5], nums2 = [9,4,9,8,4]
Output: [4,9]
Explanation: [9,4] is also accepted.
```

## Solution

```rust

pub fn intersect(nums1: Vec<i32>, nums2: Vec<i32>) -> Vec<i32> {
    let mut a = nums1;
    let mut b = nums2;
    if b.len() < a.len() {
        std::mem::swap(&mut a, &mut b);
    }

    let mut freq = HashMap::new();
    a.iter().copied().for_each(|v| {
        freq.entry(v).and_modify(|v| *v += 1).or_insert(1);
    });

    let mut result = vec![];
    for n in b.iter() {
        if let Some(count) = freq.get_mut(n) {
            if *count > 0 {
                *count -= 1;
                result.push(*n);
            }
        }
    }

    result
}
```


## Related Problems

* [349. Intersection of Two Arrays](349%20-%20Intersection%20of%20Two%20Arrays.md)