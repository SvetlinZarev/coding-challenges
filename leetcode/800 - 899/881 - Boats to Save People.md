# [881. Boats to Save People](https://leetcode.com/problems/boats-to-save-people/)

## Problem

### Description

You are given an array `people` where `people[i]` is the weight of the `i`th
person, and an infinite number of boats where each boat can carry a maximum
weight of `limit`. Each boat carries **at most two people** at the same time,
provided the sum of the weight of those people is at most `limit`.

Return the minimum number of boats to carry every given person.

### Constraints

* `1 <= people.length <= 5 * 10^4`
* `1 <= people[i] <= limit <= 3 * 10^4`

### Examples

```text
Input: people = [1,2], limit = 3
Output: 1
Explanation: 1 boat (1, 2)
```

```text
Input: people = [3,2,2,1], limit = 3
Output: 3
Explanation: 3 boats (1, 2), (2) and (3)
```

```text
Input: people = [3,5,3,4], limit = 5
Output: 4
Explanation: 4 boats (3), (3), (4), (5) 
```

## Solutions

### Best-fit strategy

The idea is to select the heaviest person, then try to find another one (if
possible) that weights as much as possible, that fits on the boat

```rust
use std::collections::HashMap;

pub fn num_rescue_boats(mut people: Vec<i32>, limit: i32) -> i32 {
    people.sort_unstable();

    let mut freq = HashMap::new();
    for &person in people.iter() {
        *freq.entry(person).or_insert(0u32) += 1;
    }

    let mut boats = 0;
    for &person in people.iter().rev() {
        match freq.get_mut(&person).filter(|&&mut x| x > 0) {
            None => continue,
            Some(count) => *count -= 1,
        }

        boats += 1;

        let free_capacity = limit - person;
        if free_capacity > 0 {
            let mut search_from = match people.binary_search(&free_capacity) {
                Ok(idx) => idx + 1,
                Err(idx) => idx.min(people.len() - 1),
            };

            while search_from > 0 {
                search_from -= 1;

                let person = people[search_from];
                match freq.get_mut(&person).filter(|&&mut x| x > 0) {
                    None => continue,
                    Some(count) => *count -= 1,
                }

                break;
            }
        }
    }

    boats
}
```

### Two pointers

Because a boat can carry only two people it doesn't matter if we put the two
lightest on a board with a lot of remaining capacity or the two best fitting,
because we cannot utilize the remaining space due to the two person limit.

Thus, we can try to repeatedly fit the heaviest and the lightest:

```rust
pub fn num_rescue_boats(mut people: Vec<i32>, limit: i32) -> i32 {
    people.sort_unstable();
    let mut boats = 0;
    let mut lo = 0;
    let mut hi = people.len() - 1;

    while lo <= hi {
        boats += 1;

        if people[lo] + people[hi] <= limit {
            lo += 1;
        }

        if hi == 0 {
            break;
        }

        hi -= 1;
    }

    boats
}
```

There is a slight issue with the above implementation though, we have to check
if `hi==0` on each iteration. We can avoid that by sorting the list in
descending order, thus we will decrease `hi` only when we are adding a lighter,
second person to the boat. Thus, the only case when we can cause a subtraction
underflow is when there is only one person in the `people` array. We can handle
that case outside the loop:

```rust
pub fn num_rescue_boats(mut people: Vec<i32>, limit: i32) -> i32 {
    if people.len() <= 1 {
        // avoid subtraction underflow in the loop below 
        return people.len() as i32;
    }

    people.sort_unstable_by(|a, b| a.cmp(b).reverse());

    let mut boats = 0;
    let mut lo = 0;
    let mut hi = people.len() - 1;

    while lo <= hi {
        boats += 1;

        if people[lo] + people[hi] <= limit {
            hi -= 1;
        }

        lo += 1;
    }

    boats
}
```

An alternative implementation that avoid that issue would follow from the
observation that the maximum possible number of boats cannot exceed the number
of people, because we need at least one person per boat. Then we can start
decreasing that number:

```rust
pub fn num_rescue_boats(mut people: Vec<i32>, limit: i32) -> i32 {
    people.sort_unstable();

    let mut boats = people.len() as i32;
    let mut lo = 0;
    let mut hi = people.len() - 1;

    while lo < hi {
        if people[lo] + people[hi] <= limit {
            // we decrease the number of boats by 1, every time we fit 2 persons on 1 boat
            boats -= 1;
            lo += 1;
        }

        hi -= 1;
    }

    boats
}
```