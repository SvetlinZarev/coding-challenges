# [57. Insert Interval](https://leetcode.com/problems/insert-interval/)

You are given an array of non-overlapping intervals `intervals`
where `intervals[i] = [starti, endi]` represent the start and the end of the `i`
th interval and `intervals` is sorted in ascending order by `starti`. You are
also given an interval `newInterval = [start, end]` that represents the start
and end of another interval.

Insert `newInterval` into intervals such that intervals is still sorted in
ascending order by `starti` and `intervals` still does not have any overlapping
intervals (merge overlapping intervals if necessary).

Return `intervals` after the insertion.

#### Constraints

* `0 <= intervals.length <= 10^4`
* `intervals[i].length == 2`
* `0 <= starti <= endi <= 10^5`
* `intervals` is sorted by `starti` in ascending order.
* `newInterval.length == 2`
* `0 <= start <= end <= 10^5`

#### Examples

```text
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
```

```text
Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].
```

## Solution

```rust
pub fn insert(mut intervals: Vec<Vec<i32>>, new_interval: Vec<i32>) -> Vec<Vec<i32>> {
    if intervals.is_empty() {
        // This is the only interval, nothing to do
        return vec![new_interval];
    }

    // Find out the position where it must be inserted in order to
    // preserve the sorting by "start-i"
    let insert_pos = intervals
        .binary_search_by(|ivl| ivl[0].cmp(&new_interval[0]))
        .unwrap_or_else(|e| e);

    let mut sln = vec![];

    // Move the elements that does not need to be merged/processed
    sln.extend(intervals.drain(..insert_pos));

    // Either push or merge the new interval with the last one
    if let Some(last) = sln.last_mut() {
        if last[1] < new_interval[0] {
            sln.push(new_interval);
        } else {
            last[1] = last[1].max(new_interval[1]);
        }
    } else {
        sln.push(new_interval);
    }

    // Either merge or move the remaining intervals
    for ivl in intervals.into_iter() {
        let last = sln.len() - 1;

        if sln[last][1] >= ivl[0] {
            sln[last][1] = sln[last][1].max(ivl[1]);
            continue;
        }

        sln.push(ivl);
    }

    sln
}
```