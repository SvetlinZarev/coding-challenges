# [1376. Time Needed to Inform All Employees](https://leetcode.com/problems/time-needed-to-inform-all-employees/)

## Problem

### Description

A company has `n` employees with a unique ID for each employee from `0`
to `n - 1`. The head of the company is the one with `headID`.

Each employee has one direct manager given in the manager array
where `manager[i]` is the direct manager of the `i`-th
employee, `manager[headID] = -1`. Also, it is guaranteed that the subordination
relationships have a tree structure.

The head of the company wants to inform all the company employees of an urgent
piece of news. He will inform his direct subordinates, and they will inform
their subordinates, and so on until all employees know about the urgent news.

The `i`-th employee needs `informTime[i]` minutes to inform all of his direct
subordinates (i.e., After `informTime[i]` minutes, all his direct subordinates
can start spreading the news).

Return the number of minutes needed to inform all the employees about the urgent
news.

### Constraints

* `1 <= n <= 10^5`
* `0 <= headID < n`
* `manager.length == n`
* `0 <= manager[i] < n`
* `manager[headID] == -1`
* `informTime.length == n`
* `0 <= informTime[i] <= 1000`
* `informTime[i] == 0` if employee `i` has no subordinates.
* It is guaranteed that all the employees can be informed.

### Examples

```text
Input: n = 1, headID = 0, manager = [-1], informTime = [0]
Output: 0
Explanation: The head of the company is the only employee in the company.
```

```text
Input: n = 6, headID = 2, manager = [2,2,-1,2,2,2], informTime = [0,0,1,0,0,0]
Output: 1
Explanation: The head of the company with id = 2 is the direct manager of all the employees in the company and needs 1 minute to inform them all.
The tree structure of the employees in the company is shown.
```

## Solutions

### Recursive DFS

```rust
use std::collections::HashMap;

pub fn num_of_minutes(_n: i32, head_id: i32, manager: Vec<i32>, inform_time: Vec<i32>) -> i32 {
    let mut graph = HashMap::new();
    for (idx, man) in manager.into_iter().enumerate() {
        if man >= 0 {
            graph.entry(man as usize).or_insert(vec![]).push(idx);
        }
    }
    dfs(&graph, &inform_time, head_id as usize, 0)
}

fn dfs(managers: &HashMap<usize, Vec<usize>>, itime: &[i32], person: usize, time: i32) -> i32 {
    let time = time + itime[person];
    let mut longest = time;

    if let Some(subordinates) = managers.get(&person) {
        for &man in subordinates {
            let t = dfs(managers, itime, man, time);
            longest = longest.max(t);
        }
    }

    longest
}
```

### Iterative DFS

```rust
use std::collections::HashMap;

pub fn num_of_minutes(_n: i32, head_id: i32, manager: Vec<i32>, inform_time: Vec<i32>) -> i32 {
    let mut org_chart = HashMap::new();
    for (idx, man) in manager.into_iter().enumerate() {
        if man >= 0 {
            org_chart.entry(man as usize).or_insert(vec![]).push(idx);
        }
    }

    let mut stack = vec![];
    stack.push((head_id as usize, 0i32));

    let mut longest = 0;
    while let Some((boss, time)) = stack.pop() {
        let time = time + inform_time[boss];
        longest = longest.max(time);

        if let Some(managed_by) = org_chart.get(&boss) {
            for managed in managed_by.iter().copied() {
                stack.push((managed, time));
            }
        }
    }

    longest
}
```