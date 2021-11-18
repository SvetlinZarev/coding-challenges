# [448. Find All Numbers Disappeared in an Array](https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/)

## Problem

Given an array nums of n integers where `nums[i]` is in the range `[1, n]`,
return an array of all the integers in the range `[1, n]` that do not 
appear in `nums`.


#### Constraints

* `n == nums.length`
* `1 <= n <= 10^5`
* `1 <= nums[i] <= n`


#### Examples

```text
Input: nums = [4,3,2,7,8,2,3,1]
Output: [5,6]
```

```text
Input: nums = [1,1]
Output: [2]
```

## Solutions

### With additional space

Create a new array with the same length and initialize all 
values to `false`. Then for each value in the original array,
mark with `true` the `value - 1` index in the boolean array.

The positions still containing `false` are the missing 
numbers.

### In-place without extra space

Given that the numbers are in the range `[1; n]` we can move
each number to its correct position, thus sorting the array.

Any number that is nnot in its correct position marks a 
missing number, which is `idx + 1`:

```rust
pub fn find_disappeared_numbers(mut nums: Vec<i32>) -> Vec<i32> {
    let mut pos = 0;

    while pos < nums.len() {
        // if the number is at the correct position,move to the next position
        if nums[pos] == (pos + 1) as i32 {
            pos += 1;
            continue;
        }

        // a duplicate number which will cause an infinite loop
        // in order to avoid the loop, mive to the next position
        let idx = (nums[pos] - 1) as usize;
        if nums[pos] == nums[idx] {
            pos += 1;
            continue;
        }

        nums.swap(pos, idx);
    }

    let mut result = vec![];
    for (idx, &num) in nums.iter().enumerate() {
        if idx + 1 != num as usize {
            result.push((idx + 1) as i32);
        }
    }

    result
}
```
