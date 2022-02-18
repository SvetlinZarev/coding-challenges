# [402. Remove K Digits](https://leetcode.com/problems/remove-k-digits/)

## Problem

Given string `num` representing a non-negative integer `num`, and an integer `k`
, return the smallest possible integer after removing `k` digits from `num`.

**Note:** removing leading zeroes does not count as removing digits!

#### Constraints

* `1 <= k <= num.length <= 10^5`
* `num` consists of only digits.
* `num` does not have any leading zeros except for the zero itself.

#### Examples

```text
Input: num = "1432219", k = 3
Output: "1219"
Explanation: Remove the three digits 4, 3, and 2 to form the new number 1219 which is the smallest.
```

```text
Input: num = "10200", k = 1
Output: "200"
Explanation: Remove the leading 1 and the number is 200. Note that the output must not contain leading zeroes.
```

```text
Input: num = "10", k = 2
Output: "0"
Explanation: Remove all the digits from the number and it is left with nothing which is 0.
```

## Solution

#### Observations:

* If the digits are in increasing order, we have to remove from the
  back: `n=12345, k=2` => `smallest=123`
* If the digits are in decreasing order, we have to remove from the
  front: `n=54321, k=2` => `smallest=321`
* If they are in mixed order, i.e. `n=45639, k=2`, then the smallest one
  is `439`, i.e. we take from the back of the first increasing subsequence
  until `k > 0` or the top of the stack becomes smaller or equal to the next
  digit

```rust
pub fn remove_kdigits(num: String, k: i32) -> String {
    let num = num.as_bytes();
    let mut k = k as usize;
    if k >= num.len() {
        return "0".to_owned();
    }

    let mut idx = 1;
    let mut answer = Vec::with_capacity(num.len());
    answer.push(num[0]);

    while idx <= num.len() {
        // push onto the stack until the next number becomes smaller than the top of the stack
        while idx < num.len() && num[idx - 1] <= num[idx] {
            answer.push(num[idx]);
            idx += 1;
        }

        // remove from the top of the stack, until we are either not allowed
        // to remove more digits, or the next number becomes equal or greater 
        // than the stack top
        while let Some(top) = answer.last().copied() {
            if k == 0 { // not allowed to remove more digits
                break;
            }

            if idx < num.len() && top <= num[idx] {
                break;
            }

            answer.pop();
            k -= 1;
        }

        // skip leading zeroes
        while answer.is_empty() && idx < num.len() && num[idx] == b'0' {
            idx += 1;
        }

        if idx < num.len() {
            answer.push(num[idx]);
        }

        idx += 1;
    }

    if answer.is_empty() {
        answer.push(b'0');
    }
    String::from_utf8(answer).unwrap()
}
```

## Topics

* Monotonic stack