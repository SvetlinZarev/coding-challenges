# [1342. Number of Steps to Reduce a Number to Zero](https://leetcode.com/problems/number-of-steps-to-reduce-a-number-to-zero/)

## Problem

### Description

Given an integer `num`, return the number of steps to reduce it to zero.

In one step, if the current number is even, you have to divide it by 2,
otherwise, you have to subtract 1 from it.

### Constraints

* `0 <= num <= 10^6`

### Examples

```text
Input: num = 14
Output: 6
Explanation: 
Step 1) 14 is even; divide by 2 and obtain 7. 
Step 2) 7 is odd; subtract 1 and obtain 6.
Step 3) 6 is even; divide by 2 and obtain 3. 
Step 4) 3 is odd; subtract 1 and obtain 2. 
Step 5) 2 is even; divide by 2 and obtain 1. 
Step 6) 1 is odd; subtract 1 and obtain 0.
```

```text
Input: num = 8
Output: 4
Explanation: 
Step 1) 8 is even; divide by 2 and obtain 4. 
Step 2) 4 is even; divide by 2 and obtain 2. 
Step 3) 2 is even; divide by 2 and obtain 1. 
Step 4) 1 is odd; subtract 1 and obtain 0.
```

## Solutions

### Simulation

```rust
pub fn number_of_steps(num: i32) -> i32 {
    let mut n = num;
    let mut steps = 0;

    while n > 0 {
        if n & 1 == 0 {
            n = n / 2;
        } else {
            n -= 1;
        }

        steps += 1;
    }

    steps
}
```

### Using Bit Operations (`O(1)` time complexity)

Observations:

* An even number has its last bit set to zero
* An odd number has its last bit set to one
* Subtracting 1 from an odd number, makes it an even number
* A division by 2 is equal to a "right shift"

Therefore, for each even number we perform 1 operation - right shift, and for
each odd number (except for 1) we perform two operations - subtraction and right
shift.

1. The number of right shifts we will perform is equal to the number of bits
   minus one (because we cannot do a right shift is the number is equal to just
   1), minus the number of leading zeroes:`1111 => 0111 => 0011 => 0001`

2. The number of subtractions we will perform is equal to the number of 1bits in
   the number, because the only way to convert a 1 to a 0 is to subtract 1.

Therefore, the number of operations is equal to the sum of 1# and 2#

```rust
pub fn number_of_steps(num: i32) -> i32 {
    if num == 0 {
        return 0;
    }

    (i32::BITS - num.leading_zeros() - 1 + num.count_ones()) as i32
}
```

## Related Problems

* [2139. Minimum Moves to Reach Target Score](/leetcode/2100%20-%202199/2139%20-%20Minimum%20Moves%20to%20Reach%20Target%20Score.md)
