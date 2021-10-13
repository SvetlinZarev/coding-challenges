# [645. Set Mismatch](https://leetcode.com/problems/set-mismatch/)

## Problem

You have a set of integers `s`, which originally contains all the numbers
from `1` to `n`. Unfortunately, due to some error, one of the numbers in `s` got
duplicated to another number in the set, which results in repetition of one
number and loss of another number.

You are given an integer array `nums` representing the data status of this set
after the error.

Find the number that occurs twice and the number that is missing and return them
in the form of an array

#### Constraints

* `2 <= nums.length <= 104`
* `1 <= nums[i] <= 10^4`

#### Examples

```text
Input: nums = [1,2,2,4]
Output: [2,3]
```

```text
Input: nums = [1,1]
Output: [1,2]
```

## Solution

### O(n) time with O(1) additional space

```rust
pub fn find_error_nums(mut nums: Vec<i32>) -> Vec<i32> {
    let mut idx = 0;
    while idx < nums.len() {
        let pos = (nums[idx] - 1) as usize;
        if nums[idx] == nums[pos] {
            idx += 1;
            continue;
        }

        nums.swap(pos, idx);
    }

    for (idx, val) in nums.iter().enumerate() {
        if idx as i32 != *val - 1 {
            return vec![*val, idx as i32 + 1];
        }
    }

    vec![]
}
```

### Other approaches

#### Using HashMap

Use the numbers as map keys and store the number of occurrences as map values

#### With Sorting

Sort the array and check the difference between each consecutive number. 