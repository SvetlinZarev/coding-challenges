# [347. Top K Frequent Elements](https://leetcode.com/problems/top-k-frequent-elements/)

## Problem

### Description

Given an integer array `nums` and an integer `k`, return the `k` most frequent
elements. You may return the answer in any order.

### Constraints

* `1 <= nums.length <= 10^5`
* `k` is in the range `[1, the number of unique elements in the array]`.
* It is guaranteed that the answer is unique.

### Examples

```text
Input: nums = [1], k = 1
Output: [1]
```

```text
Input: nums = [1,1,1,2,2,3], k = 2
Output: [1,2]
```

## Solutions

### Using a priority queue

```rust
use std::cmp::Reverse;
use std::collections::{BinaryHeap, HashMap};

pub fn top_k_frequent(nums: Vec<i32>, k: i32) -> Vec<i32> {
    let mut freq = HashMap::with_capacity(nums.len());
    nums.iter().copied().for_each(|n| {
        freq.entry(n).and_modify(|v| *v += 1).or_insert(1);
    });

    let mut heap = BinaryHeap::with_capacity(k as usize);
    freq.iter().for_each(|(num, count)| {
        if heap.len() < heap.capacity() {
            heap.push(Reverse((*count, *num)));
        } else {
            let Reverse((min, _)) = heap.peek().unwrap();
            if count > min {
                heap.pop();
                heap.push(Reverse((*count, *num)));
            }
        }
    });

    heap.iter().map(|&Reverse((_, v))| v).collect()
}
```

## Related problems

* [215. Kth Largest Element in an Array](/leetcode/200%20-%20299/215%20-%20Kth%20Largest%20Element%20in%20an%20Array.md)
* [703. Kth Largest Element in a Stream](/leetcode/700%20-%20799/703%20-%20Kth%20Largest%20Element%20in%20a%20Stream.md)
* [973. K Closest Points to Origin](/leetcode/900%20-%20999/973%20-%20K%20Closest%20Points%20to%20Origin.md)
* [1985. Find the Kth Largest Integer in the Array](/leetcode/1900%20-%201999/1985%20-%20Find%20the%20Kth%20Largest%20Integer%20in%20the%20Array.md)
