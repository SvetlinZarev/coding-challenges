# [435. Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)

## Problem

### Description

Given an array of intervals `intervals` where `intervals[i] = [starti, endi]`,
return the minimum number of intervals you need to remove to make the rest of
the intervals non-overlapping.

### Constraints

* `1 <= intervals.length <= 10^5`
* `intervals[i].length == 2`
* `-5 * 10^4 <= starti < endi <= 5 * 10^4`

### Examples

```text
Input: intervals = [[1,2],[2,3],[3,4],[1,3]]
Output: 1
Explanation: [1,3] can be removed and the rest of the intervals are non-overlapping.
```

```text
Input: intervals = [[1,2],[1,2],[1,2]]
Output: 2
Explanation: You need to remove two [1,2] to make the rest of the intervals non-overlapping.
```

```text
Input: intervals = [[1,2],[2,3]]
Output: 0
Explanation: You don't need to remove any of the intervals since they're already non-overlapping.
```

## Solutions

### Greedy

```rust
pub fn erase_overlap_intervals(mut intervals: Vec<Vec<i32>>) -> i32 {
    intervals.sort_unstable_by(|a, b| a[1].cmp(&b[1]));

    let mut answer = 0;
    let mut rightmost_end = i32::MIN;

    for interval in intervals {
        if interval[0] >= rightmost_end {
            // it's guaranteed that the new interval end will be greater
            // or equal that the current end, due to the sorting
            rightmost_end = interval[1];
        } else {
            answer += 1;
        }
    }
    answer
}
```