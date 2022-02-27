# [202. Happy Number](https://leetcode.com/problems/happy-number/)

## Problem

### Description

Write an algorithm to determine if a number `n` is happy.

A happy number is a number defined by the following process:

* Starting with any positive integer, replace the number by the sum of the
  squares of its digits.
* Repeat the process until the number equals 1 (where it will stay), or it loops
  endlessly in a cycle which does not include 1.
* Those numbers for which this process ends in 1 are happy.

Return `true` if `n` is a happy number, and `false` if not.

### Constraints

* `1 <= n <= 2^31 - 1`

### Examples

```text
Input: n = 19
Output: true
Explanation:
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
```

```text
Input: n = 2
Output: false
```

## Solutions

### With HashMap for caching previously computed numbers

```rust
pub fn is_happy(mut n: i32) -> bool {
    let mut cache = HashSet::new();
    while cache.insert(n) {
        let mut next = 0;
        while n > 0 {
            let x = (n % 10).pow(2);
            n /= 10;
            next += x;
        }
        n = next;
    }

    n == 1
}
```

### Floyd's Cycle-Finding Algorithm

The sequence of numbers is essentially a link list. So we can use the fast/slow
pointer method. If the fast & slow "pointers" have equal values, then there is a
cycle.

```rust
pub fn is_happy(n: i32) -> bool {
    let mut slow = n;
    let mut fast = next(n);

    while fast != 1 && slow != fast {
        slow = next(slow);
        fast = next(next(fast));
    }

    fast == 1
}

fn next(mut n: i32) -> i32 {
    let mut next = 0;

    while n > 0 {
        next += (n % 10).pow(2);
        n /= 10;
    }

    next
}
```