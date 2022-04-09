# [973. K Closest Points to Origin](https://leetcode.com/problems/k-closest-points-to-origin/)

## Problem

### Description

Given an array of points where `points[i] = [xi, yi]` represents a point on
the `X-Y` plane and an integer `k`, return the `k` closest points to the
origin `(0, 0)`.

The distance between two points on the `X-Y` plane is the Euclidean distance (
i.e., `√(x1 - x2)^2 + (y1 - y2)^2`).

You may return the answer in any order. The answer is guaranteed to be unique (
except for the order that it is in).

### Constraints

* `1 <= k <= points.length <= 10^4`
* `-10^4 < xi, yi < 10^4`

### Examples

```text
Input: points = [[1,3],[-2,2]], k = 1
Output: [[-2,2]]
Explanation:
The distance between (1, 3) and the origin is sqrt(10).
The distance between (-2, 2) and the origin is sqrt(8).
Since sqrt(8) < sqrt(10), (-2, 2) is closer to the origin.
We only want the closest k = 1 points from the origin, so the answer is just [[-2,2]].
```

```text


Input: points = [[3,3],[5,-1],[-2,4]], k = 2
Output: [[3,3],[-2,4]]
Explanation: The answer [[-2,4],[3,3]] would also be accepted.
```

## Solutions

### Using a priority queue

```rust
use std::cmp::Ordering;
use std::collections::BinaryHeap;

struct Dist {
    dist: i64,
    point: Vec<i32>,
}

impl Dist {
    fn new(point: Vec<i32>) -> Self {
        assert_eq!(point.len(), 2);

        let dist = point[0].pow(2) as i64 + point[1].pow(2) as i64;
        Dist { dist, point }
    }
}

impl Ord for Dist {
    fn cmp(&self, other: &Self) -> Ordering {
        self.dist.cmp(&other.dist)
    }
}

impl PartialOrd for Dist {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

impl PartialEq for Dist {
    fn eq(&self, other: &Self) -> bool {
        self.dist.eq(&other.dist)
    }
}

impl Eq for Dist {
    //
}

pub fn k_closest(points: Vec<Vec<i32>>, k: i32) -> Vec<Vec<i32>> {
    let mut heap = BinaryHeap::with_capacity(k as usize);

    for point in points.into_iter() {
        let dist = Dist::new(point);

        if heap.len() < heap.capacity() {
            heap.push(dist);
        } else {
            let top = heap.peek().unwrap();
            if &dist < top {
                heap.pop();
                heap.push(dist);
            }
        }
    }

    heap.into_iter().map(|p| p.point).collect()
}
```

## Related Problems

* [215. Kth Largest Element in an Array](/leetcode/200%20-%20299/215%20-%20Kth%20Largest%20Element%20in%20an%20Array.md)
* [347. Top K Frequent Elements](/leetcode/300%20-%20399/347%20-%20Top%20K%20Frequent%20Elements.md)
* [703. Kth Largest Element in a Stream](/leetcode/700%20-%20799/703%20-%20Kth%20Largest%20Element%20in%20a%20Stream.md)
* [1985. Find the Kth Largest Integer in the Array](/leetcode/1900%20-%201999/1985%20-%20Find%20the%20Kth%20Largest%20Integer%20in%20the%20Array.md)

