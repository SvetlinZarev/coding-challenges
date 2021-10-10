# [88. Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/)

# Problem

You are given two integer arrays `nums1` and `nums2`, sorted in non-decreasing
order, and two integers m and n, representing the number of elements in `nums1`
and `nums2` respectively.

Merge `nums1` and `nums2` into a single array sorted in non-decreasing order.

The final sorted array should not be returned by the function, but instead be
stored inside the array `nums1`. To accommodate this, `nums1` has a length
of `m + n`, where the first `m` elements denote the elements that should be
merged, and the last n elements are set to 0 and should be ignored. `nums2` has
a length of `n`.

## Solutions

# With a copy of the first array:

```rust
pub fn merge(nums1: &mut Vec<i32>, m: i32, nums2: &mut Vec<i32>, n: i32) {
    let a = nums1[0..m as usize].to_vec();
    let b = nums2;

    nums1.truncate(0);

    let mut ap = 0;
    let mut bp = 0;

    while ap < a.len() && bp < b.len() {
        if a[ap] < b[bp] {
            nums1.push(a[ap]);
            ap += 1;
        } else if a[ap] > b[bp] {
            nums1.push(b[bp]);
            bp += 1;
        } else {
            nums1.push(a[ap]);
            nums1.push(b[bp]);
            ap += 1;
            bp += 1;
        }
    }

    if ap == a.len() {
        for idx in bp..b.len() {
            nums1.push(b[idx]);
        }
    } else {
        for idx in ap..a.len() {
            nums1.push(a[idx]);
        }
    }
}
```

## In-place without additional copies

It should be possible to merge the arrays into the first one by going backwards
from the largest element towards the smallest, thus no two elements will
overlap, thus no need for a copy of the first array