# [1413. Minimum Value to Get Positive Step by Step Sum](https://leetcode.com/problems/minimum-value-to-get-positive-step-by-step-sum/)

## Problem

Given an array of integers `nums`, you start with an initial positive 
value `startValue`.

In each iteration, you calculate the *step by step* sum of `startValue` 
plus elements in `nums` (from left to right).

Return the minimum positive value of `startValue` such that the 
*step by step* sum is never less than `1`.


#### Constraints

* `1 <= nums.length <= 100`
* `-100 <= nums[i] <= 100`


#### Examples

```text
Input: nums = [-3,2,-3,4,2]
Output: 5
Explanation: If you choose startValue = 4, in the third iteration your step by step sum is less than 1.
                step by step sum
                startValue = 4 | startValue = 5 | nums
                  (4 -3 ) = 1  | (5 -3 ) = 2    |  -3
                  (1 +2 ) = 3  | (2 +2 ) = 4    |   2
                  (3 -3 ) = 0  | (4 -3 ) = 1    |  -3
                  (0 +4 ) = 4  | (1 +4 ) = 5    |   4
                  (4 +2 ) = 6  | (5 +2 ) = 7    |   2
```

```text
Input: nums = [1,2]
Output: 1
Explanation: Minimum start value should be positive. 
```

```text
Input: nums = [1,-2,-3]
Output: 5
```


## Solutions

```rust
pub fn min_start_value(nums: Vec<i32>) -> i32 {
    let mut start = 0;
    let mut sum = 0;

    for &n in nums.iter() {
        // track the sum till the current step
        sum += n;

        // if the sum is at least 1, then there is nothing to do
        if sum < 1 {
            // if the sum is less then 1, calculate how much we are missing
            start += 1 - sum;
            // then "add" that missing amount to "sum" and continue
            sum = 1;
        }
    }

    // By definition the start value must be at least one,
    // so if the array contains only positive numbers (thus
    // `start == 0`) return 1
    start.max(1)
}
```

Or a bit simplified:
```rust
pub fn min_start_value(nums: Vec<i32>) -> i32 {
    let mut start = 1;
    let mut sum = 0;

    for &n in nums.iter() {
        // track the sum till the current step
        sum += n;
        
        // if the sum is at least 1, then there is nothing to do
        if sum + start < 1 {
            // if the sum is less then 1, calculate how much we are missing
            start += 1 - (sum + start);
        }
    }
    
    start
}
```

Or with even less code:
```rust
pub fn min_start_value(nums: Vec<i32>) -> i32 {
    let mut start = 1;
    let mut sum = 0;

    for &n in nums.iter() {
        // track the sum till the current step
        sum += n;
        // if the step by step value is less than 1 (i.e. the sum is negative), 
        // add the difference, otherwise add 0, thus not changing the sum
        start += (1 - (sum + start)).max(0);
    }

    start
}
```
