# [991. Broken Calculator](https://leetcode.com/problems/broken-calculator/)

## Problem

### Description

There is a broken calculator that has the integer `startValue` on its display
initially. In one operation, you can:

* multiply the number on display by 2, or
* subtract 1 from the number on display.

Given two integers `startValue` and `target`, return the minimum number of
operations needed to display `target` on the calculator.

### Constraints

* `1 <= x, y <= 10^9`

### Examples

```text
Input: startValue = 2, target = 3
Output: 2
Explanation: Use double operation and then decrement operation {2 -> 4 -> 3}.
```

```text
Input: startValue = 5, target = 8
Output: 2
Explanation: Use decrement and then double {5 -> 4 -> 8}.
```

```text
Input: startValue = 3, target = 10
Output: 3
Explanation: Use double, decrement and double {3 -> 6 -> 5 -> 10}.
```

## Solutions

### Greedy 1 (slow)

We can work backwards - trying to reach the `start_value` from the `target`
value. Then we can make the following observations:

* an odd target value can only be achieved if we subtract 1 from a larger, even
  value
* We can only divide even values, thus if the value is odd, we have to add +1 to
  make it divisible by 2

```rust
pub fn broken_calc(start_value: i32, target: i32) -> i32 {
    let mut operations = 0;
    let mut value = target;

    while value != start_value {
        operations += 1;

        if value > start_value && value % 2 == 0 {
            value /= 2;
        } else {
            value += 1;
        }
    }

    operations
}
```

### Greedy 2 (fast)

We can make the observation that when the current value is less than the start
value, then we can only execute the `plus one` operation N times
until `start_value == value`. Thus, we can compute that in `O(1)` time instead
of `O(N)`.

**Example: ** Let `start_value=1_000_000_000` and `target=1`. Instead of looping
a billion times, subtracting 1, we can ust do it immediately, because this is
the only allowed operation.

```rust
pub fn broken_calc(start_value: i32, target: i32) -> i32 {
    let mut operations = 0;
    let mut value = target;

    while value > start_value {
        operations += 1;

        if value % 2 == 0 {
            value /= 2;
        } else {
            value += 1;
        }
    }

    operations + (start_value - value)
}
```