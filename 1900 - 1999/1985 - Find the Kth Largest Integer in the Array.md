# [1985. Find the Kth Largest Integer in the Array](https://leetcode.com/problems/find-the-kth-largest-integer-in-the-array/)

## Problem

You are given an array of strings `nums` and an integer `k`. Each string
in `nums` represents an integer without leading zeros.

Return the string that represents the `k`th largest integer in `nums`.

**Note:** Duplicate numbers should be counted distinctly. For example, if `nums`
is `["1","2","2"]`, `2` is the first largest integer, `2` is the second-largest
integer, and `1` is the third-largest integer.

#### Constraints

* `1 <= k <= nums.length <= 10^4`
* `1 <= nums[i].length <= 100`
* `nums[i]` consists of only digits.
* `nums[i]` will not have any leading zeros.

#### Examples

```text
Input: nums = ["3","6","7","10"], k = 4
Output: "3"
Explanation:
The numbers in nums sorted in non-decreasing order are ["3","6","7","10"].
The 4th largest integer in nums is "3".
```

```text
Input: nums = ["2","21","12","1"], k = 3
Output: "2"
Explanation:
The numbers in nums sorted in non-decreasing order are ["1","2","12","21"].
The 3rd largest integer in nums is "2".
```

```text
Input: nums = ["0","0"], k = 2
Output: "0"
Explanation:
The numbers in nums sorted in non-decreasing order are ["0","0"].
The 2nd largest integer in nums is "0".
```

#### Hints

* If two numbers have different lengths, which one will be larger?
* The longer number is the larger number.
* If two numbers have the same length, which one will be larger?
* Compare the two numbers starting from the most significant digit. Once you
  have found the first digit that differs, the one with the larger digit is the
  larger number.

## Solution

```rust
use std::cmp::{Ordering, Reverse};
use std::collections::BinaryHeap;

#[derive(Eq, PartialEq)]
struct NumStr(String);

impl Ord for NumStr {
    fn cmp(&self, other: &Self) -> Ordering {
        match self.0.len().cmp(&other.0.len()) {
            Ordering::Less => Ordering::Less,
            Ordering::Greater => Ordering::Greater,
            Ordering::Equal => self.0.cmp(&other.0),
        }
    }
}

impl PartialOrd for NumStr {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

pub fn kth_largest_number(nums: Vec<String>, k: i32) -> String {
    let mut pq = BinaryHeap::with_capacity(k as usize);

    for n in nums.into_iter() {
        let ns = NumStr(n);

        if (k as usize) > pq.len() {
            pq.push(Reverse(ns));
        } else {
            if let Some(Reverse(smallest)) = pq.peek() {
                if &ns > smallest {
                    pq.pop();
                    pq.push(Reverse(ns));
                }
            }
        }
    }

    pq.pop().unwrap().0.0
}
```

## Related Questions

* [215. Kth Largest Element in an Array](/200%20-%20299/215%20-%20Kth%20Largest%20Element%20in%20an%20Array.md)