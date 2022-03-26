# [303. Range Sum Query - Immutable](https://leetcode.com/problems/range-sum-query-immutable/)

## Problem

### Description

Given an integer array `nums`, handle multiple queries of the following type:

* Calculate the sum of the elements of `nums` between indices `left` and `right`
  inclusive where `left <= right`.

Implement the `NumArray` class:

* `NumArray(int[] nums)` Initializes the object with the integer array `nums`.
* `int sumRange(int left, int right)` Returns the sum of the elements of `nums`
  between indices `left` and `right` inclusive (
  i.e. `nums[left] + nums[left + 1] + ... + nums[right]`).

### Constraints

* `1 <= nums.length <= 10^4`
* `-10^5 <= nums[i] <= 10^5`
* `0 <= left <= right < nums.length`
* At most `10^4` calls will be made to `sumRange`.

### Examples

```text
Input
["NumArray", "sumRange", "sumRange", "sumRange"]
[[[-2, 0, 3, -5, 2, -1]], [0, 2], [2, 5], [0, 5]]

Output
[null, 1, -1, -3]

Explanation
NumArray numArray = new NumArray([-2, 0, 3, -5, 2, -1]);
numArray.sumRange(0, 2); // return (-2) + 0 + 3 = 1
numArray.sumRange(2, 5); // return 3 + (-5) + 2 + (-1) = -1
numArray.sumRange(0, 5); // return (-2) + 0 + 3 + (-5) + 2 + (-1) = -3
```

## Solution

```rust
struct NumArray {
    cache: Vec<i32>,
}

impl NumArray {
    fn new(nums: Vec<i32>) -> Self {
        let mut cache = Vec::with_capacity(nums.len() + 1);
        cache.push(0);

        let mut sum = 0;
        for n in nums.into_iter() {
            sum += n;
            cache.push(sum);
        }

        Self { cache }
    }

    fn sum_range(&self, left: i32, right: i32) -> i32 {
        //assert!(left >= 0);
        //assert!(left <= right);
        //assert!(right < self.cache.len() as i32);

        self.cache[right as usize + 1] - self.cache[left as usize]
    }
}
```

## Related Problems

* [304. Range Sum Query 2D - Immutable](304%20-%20Range%20Sum%20Query%202D%20-%20Immutable.md)