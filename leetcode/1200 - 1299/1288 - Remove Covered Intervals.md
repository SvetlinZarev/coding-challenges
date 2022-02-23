# [1288. Remove Covered Intervals](https://leetcode.com/problems/remove-covered-intervals/)

## Problem

Given an array `intervals` where `intervals[i] = [li, ri]` represent the
interval `[li, ri)`, remove all intervals that are covered by another interval
in the list.

The interval `[a, b)` is covered by the interval `[c, d)` if and only
if `c <= a` and `b <= d`.

Return the number of remaining intervals.

#### Constraints

* `1 <= intervals.length <= 1000`
* `intervals[i].length == 2`
* `0 <= li <= ri <= 10^5`
* All the given intervals are unique.

#### Examples

```text


Input: intervals = [[1,4],[3,6],[2,8]]
Output: 2
Explanation: Interval [3,6] is covered by [2,8], therefore it is removed.
```

```text
Input: intervals = [[1,4],[2,3]]
Output: 1
```

```text
Input: intervals = [[1,2],[1,4],[3,4]]
Output: 1
```

## Solution

1. Sort the intervals in decreasing order by their *end*
2. Stable-sort the intervals by their start
3. Now we have the widest interval starting at `i` at the front, so all the
   other intervals starting with the same number are within it, thus the only
   way to get a non-overlapping interval is to get one that expands further to
   right

```rust
pub fn remove_covered_intervals(mut intervals: Vec<Vec<i32>>) -> i32 {
    intervals.sort_unstable_by(|a, b| b[1].cmp(&a[1]));
    intervals.sort_by(|a, b| a[0].cmp(&b[0]));

    let mut end = 0;
    let mut count = 0;

    for ivl in intervals.iter() {
        if end < ivl[1] {
            end = ivl[1];
            count += 1;
        }
    }

    count
}
```

The sorting step can be optimized, so we need to sort only once:

```rust
use std::cmp::Ordering;

pub fn remove_covered_intervals(mut intervals: Vec<Vec<i32>>) -> i32 {
    intervals.sort_unstable_by(|a, b| match a[0].cmp(&b[0]) {
        Ordering::Less => Ordering::Less,
        Ordering::Greater => Ordering::Greater,
        Ordering::Equal => b[1].cmp(&a[1]),
    });

    let mut end = 0;
    let mut count = 0;

    for ivl in intervals.iter() {
        if end < ivl[1] {
            end = ivl[1];
            count += 1;
        }
    }

    count
}
```