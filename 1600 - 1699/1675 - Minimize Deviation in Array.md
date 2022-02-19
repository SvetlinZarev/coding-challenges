# [1675. Minimize Deviation in Array](https://leetcode.com/problems/minimize-deviation-in-array/)

## Problem

You are given an array `nums` of `n` positive integers.

You can perform two types of operations on any element of the array any number
of times:

* If the element is even, divide it by 2.
    * For example, if the array is `[1,2,3,4]`, then you can do this operation
      on the last element, and the array will be `[1,2,3,2]`.
* If the element is odd, multiply it by 2.
    * For example, if the array is `[1,2,3,4]`, then you can do this operation
      on the first element, and the array will be `[2,2,3,4]`.

The deviation of the array is the maximum difference between any two elements in
the array.

Return the minimum deviation the array can have after performing some number of
operations.

#### Constraints

* `n == nums.length`
* `2 <= n <= 10^5`
* `1 <= nums[i] <= 10^9`

#### Examples

```text
Input: nums = [1,2,3,4]
Output: 1
Explanation: You can transform the array to [1,2,3,2], then to [2,2,3,2], then the deviation will be 3 - 2 = 1.
```

```text
Input: nums = [4,1,5,20,3]
Output: 3
Explanation: You can transform the array after two operations to [4,2,5,5,3], then the deviation will be 5 - 2 = 3.
```

```text
Input: nums = [2,10,8]
Output: 3
```

## Solution

#### Observations

* The maximum deviation can be obtained by subtracting the smallest number in
  the array from the largest one. Thus, we have to minimize that difference.
* Odd numbers can only grow in value. If an odd number is multiplied by 2, then
  it becomes an even number, thus its value can be increased only once
* Even numbers can only become smaller
* Given the previous two points, we can initially multiply all odd numbers by
  two in order to use only one operation (division by 2) later;

### Using BinaryHeap (priority queue)

```rust
use std::collections::BinaryHeap;

pub fn minimum_deviation(nums: Vec<i32>) -> i32 {
    let mut smallest = i32::MAX;
    let mut deviation = i32::MAX;
    let mut priority_queue = BinaryHeap::with_capacity(nums.len());

    for mut n in nums.into_iter() {
        // Because we can multiply the odd numbers by 2 only once,
        // let's do it now in order to work with only one operation
        // - division - later
        if n % 2 != 0 {
            n *= 2;
        }

        // fin out the smallest number in the array in order to be able to calculate the deviation
        smallest = smallest.min(n);
        priority_queue.push(n);
    }

    while let Some(largest) = priority_queue.pop() {
        let diff = largest - smallest;
        if deviation > diff {
            deviation = diff;
        }

        // Because we have already multiplied all odd numbers by 2, thus making them even, then if
        // this number is odd, it will appear again as largest number, thus creating an infinite
        // loop. This means that we cannot reduce the deviation any more than that.
        if largest % 2 != 0 {
            break;
        }

        let reduced = largest / 2;
        if reduced < smallest {
            smallest = reduced;
        }

        priority_queue.push(reduced);
    }

    deviation
}
```

### using BTreeMap

Currently, the features `first()`/`last()` (aka `min()`/`max()`) are unstable,
so one has to use iterators + `remove()` which is slow.

```rust
use std::collections::BTreeSet;

pub fn minimum_deviation(nums: Vec<i32>) -> i32 {
    let mut tree = nums
        .into_iter()
        .map(|x| if x % 2 != 0 { x * 2 } else { x })
        .collect::<BTreeSet<_>>();

    let mut deviation = i32::MAX;
    loop {
        let min = match tree.iter().next().copied() {
            None => break,
            Some(value) => value,
        };
        let max = match tree.iter().rev().next().copied() {
            None => break,
            Some(value) => value,
        };
        tree.remove(&max);

        deviation = deviation.min(max - min);
        if max % 2 != 0 {
            break;
        }
        tree.insert(max / 2);
    }

    deviation
}
```