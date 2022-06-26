# [1436. Destination City](https://leetcode.com/problems/destination-city/)

## Problem

### Description

You are given the array `paths`, where `paths[i] = [cityAi, cityBi]` means there
exists a direct path going from `cityAi` to `cityBi`. Return the destination
city, that is, the city without any path outgoing to another city.

It is guaranteed that the graph of paths forms a line without any loop,
therefore, there will be exactly one destination city.

### Constraints

* `1 <= paths.length <= 100`
* `paths[i].length == 2`
* `1 <= cityAi.length, cityBi.length <= 10`
* `cityAi != cityBi`
* All strings consist of lowercase and uppercase English letters and the space
  character.

### Examples

```text
Input: paths = [["London","New York"],["New York","Lima"],["Lima","Sao Paulo"]]
Output: "Sao Paulo" 
```

> Explanation: Starting at "London" city you will reach "Sao Paulo" city which
> is the destination city. Your trip consist of: "London" -> "New York" -> "
> Lima"
> -> "Sao Paulo".

```text
Input: paths = [["B","C"],["D","B"],["C","A"]]
Output: "A"
Explanation: All possible trips are: 
"D" -> "B" -> "C" -> "A". 
"B" -> "C" -> "A". 
"C" -> "A". 
"A". 
Clearly the destination city is "A".
```

```text
Input: paths = [["A","Z"]]
Output: "Z"
```

## Solutions

### Nodes with out-degree of zero

Due to the problem's constraints only the destination city will have an
out-degree of zero

```rust
use std::collections::HashSet;

pub fn dest_city<P: AsRef<[Vec<String>]>>(paths: P) -> String {
    let paths = paths.as_ref();

    let src = paths
        .iter()
        .map(|p| p[0].as_str())
        .collect::<HashSet<&str>>();

    for p in paths.iter() {
        if !src.contains(&p[1].as_str()) {
            return p[1].clone();
        }
    }

    // unreachable because there are no loops
    unreachable!()
}
```