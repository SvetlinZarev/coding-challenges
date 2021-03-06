# [313. Super Ugly Number](https://leetcode.com/problems/super-ugly-number/)

## Problem

A "super ugly" number is a positive integer whose prime factors are in the array
`primes`.

Given an integer `n` and an array of integers `primes`, return the `n`th super
ugly number.

The `n`th super ugly number is guaranteed to fit in a 32-bit signed integer.

#### Constraints

* `1 <= n <= 10^6`
* `1 <= primes.length <= 100`
* `2 <= primes[i] <= 1000`
* `primes[i]` is guaranteed to be a prime number.
* All the values of `primes` are **unique** and sorted in **ascending** order.

#### Examples

```text
Input: n = 12, primes = [2,7,13,19]
Output: 32
```

> Explanation: `[1,2,4,7,8,13,14,16,19,26,28,32]` is the sequence of the first
> 12 super ugly numbers given `primes = [2,7,13,19]`.

```text
Input: n = 1, primes = [2,3,5]
Output: 1
```

> Explanation: `1` has no prime factors, therefore all of its prime factors
> are in the array `primes = [2,3,5]`.

## Solution

```rust
use std::cmp::Reverse;
use std::collections::BinaryHeap;

pub fn nth_super_ugly_number(n: i32, primes: Vec<i32>) -> i32 {
    let mut ugly = vec![0; (n + 1) as usize];
    ugly[0] = 1;

    // It's the same as if we are merging K sorted arrays. 
    // Each prime number generates a sorted, increasing array
    let mut heap = BinaryHeap::with_capacity(primes.len());
    for idx in 0..primes.len() {

        // 1: the first number in the array; 
        // 2: the `prime`, won't change; 
        // 3: Index in the sorted array pointing to the 
        //    last ugly number generated by that prime number
        heap.push(Reverse((primes[idx], primes[idx], 0)));
    }

    let mut idx = 1; // we already know the first ugly number
    while idx < n as usize {
        match heap.pop() {
            None => panic!(),
            Some(Reverse((num, prime, pos))) => {
                if num != ugly[idx - 1] {
                    ugly[idx] = num;
                    idx += 1;
                }

                let n = prime * ugly[pos + 1];
                heap.push(Reverse((n, prime, pos + 1)));
            }
        }
    }

    ugly[n as usize - 1] // "-1" because the array is zero based
}
```

## Related Problems

* [23. Merge k Sorted Lists](/leetcode/000%20-%20099/23%20-%20Merge%20k%20Sorted%20Lists.md)
* [264. Ugly Number II](/leetcode/200%2-%20299/264%20-%20Ugly%20Number%20II.md)