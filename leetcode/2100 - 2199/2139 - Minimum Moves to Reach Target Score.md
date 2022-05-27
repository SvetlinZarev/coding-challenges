# [2139. Minimum Moves to Reach Target Score](https://leetcode.com/problems/minimum-moves-to-reach-target-score/)

## Problem

### Description

You are playing a game with integers. You start with the integer `1` and you
want to reach the integer `target`.

In one move, you can either:

* Increment the current integer by one (i.e., `x = x + 1`).
* Double the current integer (i.e., `x = 2 * x`).

You can use the increment operation any number of times, however, you can only
use the `double` operation at most `maxDoubles` times.

Given the two integers `target` and `maxDoubles`, return the minimum number of
moves needed to reach `target` starting with `1`.

### Constraints

* `1 <= target <= 10^9`
* `0 <= maxDoubles <= 100`

### Examples

```text
Input: target = 5, maxDoubles = 0
Output: 4
Explanation: Keep incrementing by 1 until you reach target.
```

```text
Input: target = 19, maxDoubles = 2
Output: 7
Explanation: Initially, x = 1
Increment 3 times so x = 4
Double once so x = 8
Increment once so x = 9
Double again so x = 18
Increment once so x = 19
```

```text
Input: target = 10, maxDoubles = 4
Output: 4
Explanation: Initially, x = 1
Increment once so x = 2
Double once so x = 4
Increment once so x = 5
Double again so x = 10
```

## Solutions

### Simulation/Greedy

```rust
pub fn min_moves(target: i32, max_doubles: i32) -> i32 {
    let mut doubles = max_doubles;
    let mut num = target;
    let mut steps = 0;

    // Check for `doubles` in order to avoid unnecessary looping if the 
    // number is big, but we cannot perform more `double` operations
    while num > 1 && doubles > 0 {
        if num & 1 != 0 {
            num -= 1;
        } else {
            num /= 2;
            doubles -= 1;
        }

        steps += 1;
    }

    // Fast path if `doubles` is zero
    if num > 1 {
        steps += num - 1;
    }

    steps
}
```

### Constant time solution using bitwise operations

```rust
pub fn min_moves(target: i32, max_doubles: i32) -> i32 {
    // This is the number of division operations that we are allowed to do.
    // The maximum number of possible divisions is the number of bits to the 
    // right of the most significant 1 bit:
    // * 0000_0001 => 0 divisions (no bits to the right of the 1 MSB)
    // * 0000_1000 => 3 divisions
    // * 1000_0000 => 7 divisions
    // * 1010_1010 => 7 divisions
    let divisions = max_doubles.min((i32::BITS - 1 - target.leading_zeros()) as i32);

    // The difference is pretty obvious - it's the same as in the 
    // naive solution. Once we can no longer do divisions, we just 
    // subtract one from the value (because we start from ONE to TARGET), 
    // and this is the number of subtractions that we would need to do
    // in case we were looping in the naive solution
    let difference = (target >> divisions) - 1;

    // So far so good, but how many subtractions we need to perform in between 
    // the divisions (not in general, only in between the divisions). Example: 
    // * 18/2 => 9-1 => 8/2 => 4/2 => 2/2 => 1 
    // So in this case we need to do 1 subtraction. 
    //
    // This too can be computed using bitwise operations, by computing a mask 
    // of 1 bits that covers the bits that will be "divided":
    // Number: ..10101
    // Mask:   ..00111
    // Result: ..00101
    // Thus we end up with 2 one bits set. We would need to switch them to 0
    // in order to continue with the divisions, and the only way to do is 
    // via subtracting 1
    let mask = (1 << divisions) - 1;
    let subtractions = (target & mask).count_ones() as i32;

    // The result is the sum of the number of division operations, 
    // plus the number of subtraction operations in between the divisions,
    // plus the number of subtractions that would need to be performed
    // on the remaining value
    divisions + subtractions + difference
}
```

## Related Problems

* [1342. Number of Steps to Reduce a Number to Zero](/leetcode/1300%20-%201399/1342%20-%20Number%20of%20Steps%20to%20Reduce%20a%20Number%20to%20Zero.md)
