# [703. Kth Largest Element in a Stream](https://leetcode.com/problems/kth-largest-element-in-a-stream/)

## Problem

### Description

Design a class to find the `k`th largest element in a stream. Note that it is
the `k`th largest element in the sorted order, not the `k`th distinct element.

Implement `KthLargest` class:

* `KthLargest(int k, int[] nums)` Initializes the object with the integer k and
  the stream of integers `nums`.
* `int add(int val) Appends the` integer val to the stream and returns the
  element representing the `k`th largest element in the stream.

### Constraints

* `1 <= k <= 10^4`
* `0 <= nums.length <= 10^4`
* `-10^4 <= nums[i] <= 10^4`
* `-10^4 <= val <= 10^4
* At most `10^4` calls will be made to add.
* It is guaranteed that there will be at least `k` elements in the array when
  you search for the `k`th element.

### Examples

```text
Input
["KthLargest", "add", "add", "add", "add", "add"]
[[3, [4, 5, 8, 2]], [3], [5], [10], [9], [4]]
Output
[null, 4, 5, 5, 8, 8]

Explanation
KthLargest kthLargest = new KthLargest(3, [4, 5, 8, 2]);
kthLargest.add(3);   // return 4
kthLargest.add(5);   // return 5
kthLargest.add(10);  // return 5
kthLargest.add(9);   // return 8
kthLargest.add(4);   // return 8
```

## Solutions

### Using a Priority Queue

```rust
use std::cmp::Reverse;
use std::collections::BinaryHeap;

struct KthLargest {
    pq: BinaryHeap<Reverse<i32>>,
}

impl KthLargest {
    fn new(k: i32, nums: Vec<i32>) -> Self {
        assert!(k > 0);

        let mut pq = BinaryHeap::with_capacity(k as usize);
        for n in nums {
            if pq.len() < pq.capacity() {
                pq.push(Reverse(n));
            } else if let Some(Reverse(kth)) = pq.peek().copied() {
                if n > kth {
                    pq.pop();
                    pq.push(Reverse(n));
                }
            }
        }

        Self { pq }
    }

    fn add(&mut self, val: i32) -> i32 {
        // If we have K elements, then check if the PQ's top element is the answer
        if self.pq.len() >= self.pq.capacity() {
            if let Some(Reverse(kth)) = self.pq.peek().copied() {
                if val <= kth {
                    return kth;
                }

                // If it's not, remove it and fall-through to the case where we don't jave K elements yet
                self.pq.pop();
            }
        }

        self.pq.push(Reverse(val));
        self.pq.peek().copied().unwrap().0
    }
}
```

## Related Problems

* [215. Kth Largest Element in an Array](/leetcode/200%20-%20299/215%20-%20Kth%20Largest%20Element%20in%20an%20Array.md)
* [347. Top K Frequent Elements](/leetcode/300%20-%20399/347%20-%20Top%20K%20Frequent%20Elements.md)
* [973. K Closest Points to Origin](/leetcode/900%20-%20999/973%20-%20K%20Closest%20Points%20to%20Origin.md)
* [1985. Find the Kth Largest Integer in the Array](/leetcode/1900%20-%201999/1985%20-%20Find%20the%20Kth%20Largest%20Integer%20in%20the%20Array.md)

