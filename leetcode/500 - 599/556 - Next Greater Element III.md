# [556. Next Greater Element III](https://leetcode.com/problems/next-greater-element-iii/)

## Problem

### Description

Given a positive integer `n`, find the smallest integer which has exactly the
same digits existing in the integer `n` and is greater in value than `n`. If no
such positive integer exists, return `-1`.

Note that the returned integer should fit in 32-bit integer, if there is a valid
answer, but it does not fit in 32-bit integer, return `-1`.

### Constraints

* `1 <= n <= 2^31 - 1`

### Examples

```text
Input: n = 12
Output: 21
```

```text
Input: n = 21
Output: -1
```

```text
Input: 1999999999
Output: -1
```

```text
Input: 230241
Output: 230412
```

## Solutions

### Next Greater Element

We have several cases:

1. The digits are sorted in descending order
    * `4321` => Then there is no next greater number
2. The digits are sorted in ascending order
    * `1234` => We need to swap the last two digits: `1243`
3. In all other cases we need to process the number from right to left (i.e.
   starting from the least significant digits towards the most significant
   digits), on order to find the smallest possible answer
    1. Find the first digit that is smaller than the previous one: `230241` ->
       that would be `230>2<41`
    2. Find the smallest digit at the right of the one fond in the previous step
       that is greater than it: `2302>4<1`
    3. Swap those two digits: `230241` -> `230421
    4. Sort the digits at the right of the one found in step one: `230421` ->
       `230412`

```rust

const NO_SOLUTION: i32 = -1;

pub fn next_greater_element(n: i32) -> i32 {
    assert!(n > 0);

    // Convert the number to separate digits, so we can work with them
    // The digits will be in reversed order, i.e. from least significant, to most significant
    let mut digits = num_to_digits(n as u32);

    // Find the first digit that is less than the previous digit. For instance:
    // (not reversed) 1234 -> val=3,idx=2. In our reversed array that would look
    // like: 4321 -> val-3,idx=1. If there is no such digit, we return None
    let swap_idx_a = match find_first_digit_to_swap(&digits) {
        Some(idx) => idx,
        None => return NO_SOLUTION,
    };

    // Find the smallest digit that is greater than the one found in the previous step:
    // Let's have the number 230241, the first digit&idx would be val=2,idx=3,
    // Then the smallest number, greater than 2 at the right is 4
    let swap_idx_b = find_second_digit_to_swap(&digits, swap_idx_a);

    digits.swap(swap_idx_a, swap_idx_b);

    // sort all digits after (remember, that the digits array is reversed, i.e.
    // the array is ordered from least-significant to the most significant digit,
    // thus those digits would be **after** swap_idx_a after we convert it back to
    //a number) swap_idx_a in descending order
    digits[..swap_idx_a].sort_unstable_by(|a, b| a.cmp(b).reverse());

    digits_to_num(&digits).unwrap_or(NO_SOLUTION)
}

fn num_to_digits(n: u32) -> Vec<u32> {
    let mut digits = vec![];

    let mut x = n;
    while x > 0 {
        digits.push(x % 10);
        x /= 10;
    }

    digits
}

fn digits_to_num(digits: &[u32]) -> Option<i32> {
    let mut value = 0u32;

    for &x in digits.iter().rev() {
        value = value.checked_mul(10)?;
        value = value.checked_add(x)?;
    }

    value.try_into().ok()
}

fn find_first_digit_to_swap(digits: &[u32]) -> Option<usize> {
    digits.windows(2).position(|w| w[0] > w[1]).map(|p| p + 1)
}

fn find_second_digit_to_swap(digits: &[u32], first_idx: usize) -> usize {
    let min_val = digits[first_idx];

    digits[0..first_idx]
        .iter()
        .copied()
        .enumerate()
        .fold((0, u32::MAX), |acc, idx_val| {
            if idx_val.1 > min_val && idx_val.1 < acc.1 {
                idx_val
            } else {
                acc
            }
        })
        .0
}
```