# [56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)

## Problem

Given an array of intervals where `intervals[i] = [starti, endi]`, merge all
overlapping intervals, and return an array of the non-overlapping intervals that
cover all the intervals in the input.

#### Constraints

* `1 <= intervals.length <= 10^4`
* `intervals[i].length == 2`
* `0 <= starti <= endi <= 10^4`

#### Examples

```text
Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
```

```text
Input: intervals = [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping.
```

## Solution

```rust
const START: usize = 0;
const END: usize = 1;

pub fn merge(mut intervals: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    intervals.sort_unstable();

    let mut current = None;

    let mut result = vec![];
    for interval in intervals.into_iter() {
        match &mut current {
            None => current = Some(interval),
            Some(merged) => {
                if merged[END] >= interval[START] {
                    merged[END] = merged[END].max(interval[END]);
                } else {
                    let merged = std::mem::replace(merged, interval);
                    result.push(merged)
                }
            }
        }
    }

    if let Some(interval) = current {
        result.push(interval);
    }
    result
}
```