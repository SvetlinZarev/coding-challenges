# [1588. Sum of All Odd Length Subarrays](https://leetcode.com/problems/sum-of-all-odd-length-subarrays/)

## Problem

### Description

Given an array of positive integers `arr`, calculate the sum of all possible
odd-length subarrays.

A subarray is a contiguous subsequence of the array.

Return the sum of all odd-length subarrays of `arr`.

### Constraints

* `1 <= arr.length <= 100`
* `1 <= arr[i] <= 1000`

### Examples

```text
Input: arr = [1,4,2,5,3]
Output: 58
Explanation: The odd-length subarrays of arr and their sums are:
[1] = 1
[4] = 4
[2] = 2
[5] = 5
[3] = 3
[1,4,2] = 7
[4,2,5] = 11
[2,5,3] = 10
[1,4,2,5,3] = 15
If we add all these together we get 1 + 4 + 2 + 5 + 3 + 7 + 11 + 10 + 15 = 58
```

```text
Input: arr = [1,2]
Output: 3
Explanation: There are only 2 subarrays of odd length, [1] and [2]. Their sum is 3.
```

```text
Input: arr = [10,11,12]
Output: 66
```

## Solutions

### Bruteforce

```rust
pub fn sum_odd_length_subarrays(arr: Vec<i32>) -> i32 {
    let mut sum = 0;

    for s in 0..arr.len() {
        for j in s + 1..=arr.len() { //arr.len() inclusive!
            if (j - s) % 2 == 1 {
                sum += arr[s..j].iter().copied().sum::<i32>();
            }
        }
    }

    sum
}
```