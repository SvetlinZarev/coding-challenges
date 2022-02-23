# [986. Interval List Intersections](https://leetcode.com/problems/interval-list-intersections/)

## Problem

You are given two lists of closed intervals, `firstList` and `secondList`,
where `firstList[i] = [starti, endi]` and `secondList[j] = [startj, endj]`. Each
list of intervals is pairwise disjoint and in sorted order.

Return the intersection of these two interval lists.

A closed interval `[a, b]` (with `a <= b`) denotes the set of real numbers `x`
with `a <= x <= b`.

The intersection of two closed intervals is a set of real numbers that are
either empty or represented as a closed interval. For example, the intersection
of `[1, 3]` and `[2, 4]` is `[2, 3]`.

#### Constraints

* `0 <= firstList.length, secondList.length <= 1000`
* `firstList.length + secondList.length >= 1`
* `0 <= starti < endi <= 10^9`

#### Examples

```text
Input: 
-> firstList = [[0,2],[5,10],[13,23],[24,25]], 
-> secondList = [[1,5],[8,12],[15,24],[25,26]]

Output: 
-> [[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]
```

```text
Input: firstList = [[1,3],[5,9]], secondList = []
Output: []
```

```text
Input: firstList = [], secondList = [[4,8],[10,12]]
Output: []
```

```text
Input: firstList = [[1,7]], secondList = [[3,10]]
Output: [[3,7]]
```


## Solution

```rust
pub fn interval_intersection(
    first_list: Vec<Vec<i32>>,
    second_list: Vec<Vec<i32>>,
) -> Vec<Vec<i32>> {
    let mut result = vec![];

    let mut a = 0;
    let mut b = 0;

    while a < first_list.len() && b < second_list.len() {
        let x = &first_list[a];
        let y = &second_list[b];
        
        // The X interval starts after the Y interval
        if x[0] > y[1] {
            b += 1;
            continue;
        }

        // The X interval ends before the start of Y
        if x[1] < y[0] {
            a += 1;
            continue;
        }

        let start = x[0].max(y[0]);
        let end = x[1].min(y[1]);
        result.push(vec![start, end]);

        if x[1] < y[1] {
            a += 1;
        } else {
            b += 1;
        }
    }

    result
}
```

## Related Problems

* [56. Merge Intervals](/leetcode/000%20-%20099/56%20-%20Merge%20Intervals.md)