# [630. Course Schedule III](https://leetcode.com/problems/course-schedule-iii/)

## Problem

### Description

There are `n` different online courses numbered from `1` to `n`. You are given
an array `courses` where `courses[i] = [durationi, lastDayi]` indicate that
the `i`th course should be taken continuously for `durationi` days and must be
finished before or on `lastDayi`.

You will start on the 1st day and you cannot take two or more courses
simultaneously.

Return the maximum number of courses that you can take.

### Constraints

### Examples

#### Example 1

```text
Input: courses = [[100,200],[200,1300],[1000,1250],[2000,3200]]
Output: 3
```

> **Explanation:**
>
> There are totally 4 courses, but you can take 3 courses at most:
> * First, take the 1st course, it costs 100 days so you will finish it on the
    100th day, and ready to take the next course on the 101st day.
>
> * Second, take the 3rd course, it costs 1000 days so you will finish it on the
    1100th day, and ready to take the next course on the 1101st day.
>
> * Third, take the 2nd course, it costs 200 days so you will finish it on the
    > 1300th day.
>
> * The 4th course cannot be taken now, since you will finish it on the 3300th
    day, which exceeds the closed date.

#### Example 2

```text
Input: courses = [[1,2]]
Output: 1
```

#### Example 3

```text
Input: courses = [[3,2],[4,3]]
Output: 0
```

## Solutions

### Greedy + BinaryHeap

```rust
use std::cmp::Ordering;
use std::collections::BinaryHeap;

pub fn schedule_course(mut courses: Vec<Vec<i32>>) -> i32 {
    courses.sort_unstable_by(|a, b| match a[1].cmp(&b[1]) {
        Ordering::Less => Ordering::Less,
        Ordering::Greater => Ordering::Greater,
        Ordering::Equal => a[0].cmp(&b[0]),
    });

    let mut end = 0;
    let mut pq = BinaryHeap::new();

    for c in courses.iter() {
        let cost = c[0];
        let deadline = c[1];

        // Fast path: This course is impossible to take, because it requires
        // more days to complete than we have available days. So skip it early
        // in order to avoid pushing & popping & other operations
        if cost > deadline {
            continue;
        }

        end += cost;
        pq.push(cost);

        if deadline < end {
            end -= pq.pop().unwrap();
        }
    }

    pq.len() as i32
}
```

## Related Problems

* [1642. Furthest Building You Can Reach](/leetcode/1600%20-%201699/1642%20-%20Furthest%20Building%20You%20Can%20Reach.md)
