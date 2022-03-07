# [365. Water and Jug Problem](https://leetcode.com/problems/water-and-jug-problem/)

## Problem

### Description

You are given two jugs with capacities `a_cap` and `b_cap` liters. There is an
infinite amount of water supply available. Determine whether it is possible to
measure exactly `targetCapacity` liters using these two jugs.

If `targetCapacity` liters of water are measurable, you must have targetCapacity
liters of water contained within one or both buckets by the end.

Operations allowed:

* Fill any of the jugs with water.
* Empty any of the jugs.
* Pour water from one jug into another till the other jug is completely full, or
  the first jug itself is empty.

### Constraints

* `1 <= jug1Capacity, jug2Capacity, targetCapacity <= 106`

### Examples

```text
Input: jug1Capacity = 3, jug2Capacity = 5, targetCapacity = 4
Output: true
Explanation: The famous Die Hard example 
```

```text
Input: jug1Capacity = 2, jug2Capacity = 6, targetCapacity = 5
Output: false
```

```text
Input: jug1Capacity = 1, jug2Capacity = 2, targetCapacity = 3
Output: true
```

## Solutions

### DFS/BFS

We can assume it's a graph problem where all possible operations are edges. Then
we search for a path in the directed graph.

```rust
use std::collections::HashSet;

pub fn can_measure_water(a_cap: i32, b_cap: i32, t: i32) -> bool {
  if t > a_cap + b_cap {
    return false;
  }

  let mut visited = HashSet::new();
  let mut stack = vec![(0, 0), (a_cap, 0), (0, b_cap), (a_cap, b_cap)];

  while let Some((a, b)) = stack.pop() {
    if a == t || b == t || (a + b) == t {
      return true;
    }

    if !visited.insert((a, b)) {
      continue;
    }

    // pour a to b
    let b_free = b_cap - b;
    if b_free != 0 && a != 0 {
      let transferred = a.min(b_free);
      let a1 = a - transferred;
      let b1 = b + transferred;

      stack.push((a1, b1));
      if a1 > 0 {
        // empty the second jug
        stack.push((a1, 0));
        // fill the second jug
        stack.push((a1, b_cap))
      }
      if b1 != b_cap {
        // empty the first jug
        stack.push((0, b1));
        //fill the first jug
        stack.push((a_cap, b1))
      }
    }

    // pour b to a
    let a_free = a_cap - a;
    if a_free != 0 && b != 0 {
      let transferred = b.min(a_free);
      let a1 = a + transferred;
      let b1 = b - transferred;

      stack.push((a1, b1));
      if b1 > 0 {
        // empty the first jug
        stack.push((0, b1));
        //fill the first jug
        stack.push((a_cap, b1));
      }
      if a1 != a_cap {
        // empty the second jug
        stack.push((a1, 0));
        // fill the second jug
        stack.push((a1, b_cap));
      }
    }
  }

  false
}
```

### Math

```text
   // Pseudocode
    bool canMeasureWater(int a_cap, int b_cap, int target) {
        int a = gcd(a_cap, b_cap);
        int b = gcd(a, target);
		
		// if both their gcd are equal only then we can find "target".
        return a==b && a_cap + b_cap >= target;
    }
```

## Resources

* ["Die Hard" problem](https://www.math.tamu.edu/~dallen/hollywood/diehard/diehard.htm)