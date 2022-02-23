# [260. Single Number III](https://leetcode.com/problems/single-number-iii/)

## Problem

Given an integer array `nums`, in which exactly two elements appear only once
and all the other elements appear exactly twice. Find the two elements that
appear only once. You can return the answer in any order.

You must write an algorithm that runs in linear runtime complexity and uses only
constant extra space.

#### Constraints

* `2 <= nums.length <= 3 * 10^4`
* `-2^31 <= nums[i] <= 2^31 - 1`
* Each integer in `nums` will appear twice, only two integers will appear once.

#### Examples

```text
Input: nums = [1,2,1,3,2,5]
Output: [3,5]
Explanation:  [5, 3] is also a valid answer.
```

```text
Input: nums = [-1,0]
Output: [-1,0]
```

```text
Input: nums = [0,1]
Output: [1,0]
```

## Solution

Given that each number appears twice we can apply `xor` over all the numbers in
the array. As a result the duplicate numbers will cancel each other out. Then we
have to find any bit that is different in `A` and `B` - i.e. any bit that is `1`
. Then we have to apply the same xor trick, but only over the numbers that
contain that bit. This will give us the first number. And we can just xor the
first number with that one to find the second number.

```rust
pub fn single_number(nums: Vec<i32>) -> Vec<i32> {
    // find the A ^ B number. The reset will cancel each other out
    let mut xored = nums.iter().copied().fold(0, |acc, x| acc ^ x);

    // find the rightmost bit that is 1 - i.e. that is different in A and B
    let diff = xored ^ (xored & (xored - 1));

    // Do the xor trick again, but only on the numbers containing that rightmost bit
    let a = nums
        .iter()
        .copied()
        .filter(|&x| x & diff != 0)
        .fold(0, |acc, x| acc ^ x);
    
    // find the other number
    let b = xored ^ a;

    vec![a, b]
}
```

## Related problems

* [136. Single Number](/leetcode/100%20-%20199/136%20-%20Single%20Number.md)
* [137. Single Number II](/leetcode/100%20-%20199/137%20-%20Single%20Number%20II.md)