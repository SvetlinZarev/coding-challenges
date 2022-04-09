# [215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)

## Problem

### Description

Given an integer array `nums` and an integer `k`, return the `kth` largest
element in the array.

Note that it is the `kth` largest element in the sorted order, not the `kth`
distinct element.

### Constraints

* `1 <= k <= nums.length <= 10^4`
* `-10^4 <= nums[i] <= 10^4`

### Examples

```text
Input: nums = [3,2,1,5,6,4], k = 2
Output: 5
```

```text
Input: nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
```

## Solutions

### Binary Heap / Priority Queue:  O(N * log K)

```rust
use std::cmp::Reverse;
use std::collections::BinaryHeap;

pub fn find_kth_largest(nums: Vec<i32>, k: i32) -> i32 {
    let mut heap = BinaryHeap::with_capacity((k + 1) as usize);

    for x in nums.into_iter() {
        heap.push(Reverse(x));
        if heap.len() == heap.capacity() {
            heap.pop();
        }
    }

    heap.pop().map(|r| r.0).unwrap_or(0)
}
```

Or with a slight optimisation:

```rust
use std::cmp::Reverse;
use std::collections::BinaryHeap;

pub fn find_kth_largest(nums: Vec<i32>, k: i32) -> i32 {
    let mut heap = BinaryHeap::with_capacity(k as usize);

    for x in nums.into_iter() {
        if heap.len() < heap.capacity() {
            heap.push(Reverse(x));
        } else {
            let &Reverse(top) = heap.peek().unwrap();
            if x > top {
                heap.pop();
                heap.push(Reverse(x));
            }
        }
    }

    heap.pop().map(|r| r.0).unwrap_or(0)
}
```

### Sorting: O(N log N)

Just sort the array and take the `Kth` element

## Related Problems

* [347. Top K Frequent Elements](/leetcode/300%20-%20399/347%20-%20Top%20K%20Frequent%20Elements.md)
* [703. Kth Largest Element in a Stream](/leetcode/700%20-%20799/703%20-%20Kth%20Largest%20Element%20in%20a%20Stream.md)
* [973. K Closest Points to Origin](/leetcode/900%20-%20999/973%20-%20K%20Closest%20Points%20to%20Origin.md)
* * [1985. Find the Kth Largest Integer in the Array](/leetcode/1900%20-%201999/1985%20-%20Find%20the%20Kth%20Largest%20Integer%20in%20the%20Array.md)
