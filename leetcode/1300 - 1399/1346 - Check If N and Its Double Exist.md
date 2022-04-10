# [1346. Check If N and Its Double Exist](https://leetcode.com/problems/check-if-n-and-its-double-exist/)

## Problem

### Description

Given an array `arr` of integers, check if there exists two integers `N` and `M`
such that `N` is the double of `M` ( i.e. `N = 2 * M`).

More formally check if there exists two indices `i` and `j` such that :

* `i != j`
* `0 <= i, j < arr.length`
* `arr[i] == 2 * arr[j]`

### Constraints

* `2 <= arr.length <= 500`
* `-10^3 <= arr[i] <= 10^3`

### Examples

```text
Input: arr = [10,2,5,3]
Output: true
Explanation: N = 10 is the double of M = 5,that is, 10 = 2 * 5.
```

```text
Input: arr = [7,1,14,11]
Output: true
Explanation: N = 14 is the double of M = 7,that is, 14 = 2 * 7.
```

```text
Input: arr = [3,1,7,11]
Output: false
Explanation: In this case does not exist N and M, such that N = 2 * M.
```

## Solutions

### Using a HashSet (`O(n)`)

Add the numbers to the set one by one. This means that we have to check for both
smaller & larger numbers.

For instance if the array is `[2, 4]` it's not enough to check for just `2 * 2`
and `4 * 2`, because `4` would not have been inserted in the set at the time of
checking for `2 * 2`.

We must not check for the half of odd numbers, for instance `[3, 7]`,
because `3 == 7/2`, but `3 * 2 != 7`.

```rust
use std::collections::HashSet;

pub fn check_if_exist(arr: Vec<i32>) -> bool {
    let mut numbers = HashSet::new();
    for x in arr.iter().copied() {
        if numbers.contains(&(x * 2)) {
            return true;
        }

        if x % 2 == 0 && numbers.contains(&(x / 2)) {
            return true;
        }

        numbers.insert(x);
    }

    false
}
```

### using a HashMap (`O(n)`)

```rust
use std::collections::HashMap;

pub fn check_if_exist(arr: Vec<i32>) -> bool {
    let numbers = arr.iter().copied().fold(HashMap::new(), |mut map, x| {
        *map.entry(x).or_insert(0) += 1;
        map
    });

    for x in arr.iter().copied() {
        if let Some(&count) = numbers.get(&(x * 2)) {
            // we have found the doubled number, but we need to check if it's zero, because `0 * 2 == 0`,
            // thus we need to make sure a zero appears at least twice in the array
            if x != 0 {
                return true;
            }

            if count > 1 {
                return true;
            }
        }
    }

    false
}
```

### Using sorting + binary search (`O(N log n)`)

Although it's asymptotically slower than the HashSet/Map approaches, this one
does not require additional memory.

```rust

pub fn check_if_exist(mut arr: Vec<i32>) -> bool {
    arr.sort_unstable();

    for &x in arr.iter() {
        if let Ok(idx) = arr.binary_search(&(x * 2)) {
            if x != 0 {
                return true;
            }

            if (idx > 0 && arr[idx - 1] == 0) || (idx < arr.len() - 1 && arr[idx + 1] == 0) {
                return true;
            }
        }
    }

    false
}
```