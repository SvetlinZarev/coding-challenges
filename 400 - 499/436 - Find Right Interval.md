# [436. Find Right Interval](https://leetcode.com/problems/find-right-interval/)

## Problem

You are given an array of intervals, where `intervals[i] = [starti, endi]` and
each `starti` is unique.

The right interval for an interval `i` is an interval `j` such
that `startj >= endi` and `startj` is minimized.

Return an array of right interval indices for each interval `i`. If no right
interval exists for interval `i`, then put `-1` at index `i`.

#### Constraints

* `1 <= intervals.length <= 2 * 10^4`
* `intervals[i].length == 2`
* `-10^6 <= starti <= endi <= 10^6`
* The start point of each interval is unique.

#### Examples

```text
Input: intervals = [[1,2]]
Output: [-1]
Explanation: There is only one interval in the collection, so it outputs -1.
```

```text
Input: intervals = [[3,4],[2,3],[1,2]]
Output: [-1,0,1]
Explanation: There is no right interval for [3,4].
The right interval for [2,3] is [3,4] since start0 = 3 is the smallest start that is >= end1 = 3.
The right interval for [1,2] is [2,3] since start1 = 2 is the smallest start that is >= end2 = 2.
```

```text
Input: intervals = [[1,4],[2,3],[3,4]]
Output: [-1,2,-1]
Explanation: There is no right interval for [1,4] and [3,4].
The right interval for [2,3] is [3,4] since start2 = 3 is the smallest start that is >= end1 = 3.
```

## Solution

### Sorting + Binary Search

```rust
pub fn find_right_interval(intervals: Vec<Vec<i32>>) -> Vec<i32> {
    let mut intervals = intervals.into_iter().enumerate().collect::<Vec<_>>();
    intervals.sort_by(|(_, a), (_, b)| a[0].cmp(&b[0]));

    let mut solution = vec![-1; intervals.len()];

    for (sln, left) in intervals.iter() {
        match intervals.binary_search_by(|(_, right)| right[0].cmp(&left[1])) {
            Ok(idx) => {
                // If there is an exact match, get its original index
                solution[*sln] = intervals[idx].0 as i32;
            }

            Err(idx) => {
                // If there is not an exact match, then find the first
                // interval that satisfies the requirements
                if idx < intervals.len() {
                    solution[*sln] = intervals[idx].0 as i32;
                }
            }
        }
    }

    solution
}
```

Or with a fewer lines of code:

```rust
pub fn find_right_interval(intervals: Vec<Vec<i32>>) -> Vec<i32> {
    let mut intervals = intervals.into_iter().enumerate().collect::<Vec<_>>();
    intervals.sort_by(|(_, a), (_, b)| a[0].cmp(&b[0]));

    let mut solution = vec![-1; intervals.len()];

    for (sln, left) in intervals.iter() {
        let idx = intervals
            .binary_search_by(|(_, right)| right[0].cmp(&left[1]))
            .unwrap_or_else(|e| e);

        if idx < intervals.len() {
            solution[*sln] = intervals[idx].0 as i32;
        }
    }

    solution
}
```

### Using a BTreeMap

```rust
use std::collections::BTreeMap;

pub fn find_right_interval(intervals: Vec<Vec<i32>>) -> Vec<i32> {
    let mut solution = vec![-1; intervals.len()];
    let mut map = BTreeMap::new();

    for (idx, interval) in intervals.iter().enumerate() {
        map.insert(interval[0], idx as i32);
    }

    for (idx, interval) in intervals.iter().enumerate() {
        if let Some((_, &pos)) = map.range((interval[1]..)).next() {
            solution[idx] = pos;
        }
    }

    solution
}
```