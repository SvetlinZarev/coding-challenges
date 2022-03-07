# [1356. Sort Integers by The Number of 1 Bits](https://leetcode.com/problems/sort-integers-by-the-number-of-1-bits/)

## Problem

### Description

You are given an integer array `arr`. Sort the integers in the array in
ascending order by the number of 1's in their binary representation and in case
of two or more integers have the same number of 1's you have to sort them in
ascending order.

Return the array after sorting it.

### Constraints

* `1 <= arr.length <= 500`
* `0 <= arr[i] <= 10^4`

### Examples

```text
Input: arr = [0,1,2,3,4,5,6,7,8]
Output: [0,1,2,4,8,3,5,6,7]
Explantion: [0] is the only integer with 0 bits.
[1,2,4,8] all have 1 bit.
[3,5,6] have 2 bits.
[7] has 3 bits.
The sorted array by bits is [0,1,2,4,8,3,5,6,7]
```

```text
Input: arr = [1024,512,256,128,64,32,16,8,4,2,1]
Output: [1,2,4,8,16,32,64,128,256,512,1024]
Explantion: All integers have 1 bit in the binary representation, you should just sort them in ascending order.
```

## Solutions

### Sort once by value, then do a stable sort by the number of 1s

```rust
pub fn sort_by_bits(mut arr: Vec<i32>) -> Vec<i32> {
    arr.sort_unstable();
    arr.sort_by(|a, b| a.count_ones().cmp(&b.count_ones()));

    arr
}
```

### Sort once with a custom comparator

```rust
use std::cmp::Ordering;

pub fn sort_by_bits(mut arr: Vec<i32>) -> Vec<i32> {
    arr.sort_unstable_by(|a, b| match a.count_ones().cmp(&b.count_ones()) {
        Ordering::Less => Ordering::Less,
        Ordering::Greater => Ordering::Greater,
        Ordering::Equal => a.cmp(&b),
    });

    arr
}
```