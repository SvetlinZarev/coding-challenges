# [268. Missing Number](https://leetcode.com/problems/missing-number/)

## Problem

### Description

Given an array `nums` containing n distinct numbers in the range `[0, n]`,
return the only number in the range that is missing from the array.

### Constraints

* `n == nums.length`
* `1 <= n <= 10^4`
* `0 <= nums[i] <= n`
* All the numbers of `nums` are unique.

### Examples

```text
Input: nums = [3,0,1]
Output: 2
```

> Explanation: `n = 3` since there are 3 numbers, so all numbers are in the
> range
> `[0,3]`. `2` is the missing number in the range since it does not appear
> in `nums`.

```text
Input: nums = [9,6,4,2,3,5,7,0,1]
Output: 8
```

```text
Input: nums = [0]
Output: 1
```

## Solutions

### Sort + Binary Search

```rust
pub fn missing_number(mut nums: Vec<i32>) -> i32 {
    nums.sort_unstable();

    let mut lo = 0;
    let mut hi = nums.len();

    while lo < hi {
        let mid = lo + (hi - lo) / 2;

        if nums[mid] > mid as i32 {
            hi = mid;
        } else {
            lo = mid + 1;
        }
    }

    hi as i32
}
```

### Cyclic Sort

```rust
pub fn missing_number(mut nums: Vec<i32>) -> i32 {
    let mut idx = 0;

    // put each number at a position with an index equal to its value
    while idx < nums.len() {
        if nums[idx] < idx as i32 && nums[idx] != idx as i32 {
            let pos = nums[idx] as usize;
            nums.swap(idx, pos);
            continue;
        }

        idx += 1;
    }

    // find the first position where the index is not the same as the value
    nums.iter()
        .copied()
        .enumerate()
        .position(|(idx, val)| idx != val as usize)
        .or(Some(nums.len()))
        .map(|idx| idx as i32)
        .unwrap()
}
```

### XOR

We have to loop twice:

* once XOR-ing all numbers in `[0; n]`
* once XOR-ing all numbers in the array

The result will contain the missing number.

Here is an implementation that combines those two loops into one:

```rust
pub fn missing_number(nums: Vec<i32>) -> i32 {
    let mut missing = nums.len();

    for i in 0..nums.len() {
        missing ^= i;
        missing ^= nums[i] as usize;
    }
    missing as i32
}
```

### Difference of sums

```rust
pub fn missing_number(nums: Vec<i32>) -> i32 {
    let range_sum = nums.len() * (nums.len() + 1) / 2;
    range_sum as i32 - nums.iter().sum::<i32>()
}
```

## Resources

* [Triangular number](https://en.wikipedia.org/wiki/Triangular_number)

## Related Problems

* [136. Single Number](/leetcode/100%20-%20199/136%20-%20Single%20Number.md)
