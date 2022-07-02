# [1710. Maximum Units on a Truck](https://leetcode.com/problems/maximum-units-on-a-truck/)

## Problem

### Description

You are assigned to put some amount of boxes onto one truck. You are given a 2D
array `boxTypes`, where `boxTypes[i] = [numberOfBoxesi, numberOfUnitsPerBoxi]`:

* `numberOfBoxesi` is the number of boxes of type `i`.
* `numberOfUnitsPerBoxi` is the number of units in each box of the type `i`.

You are also given an integer `truckSize`, which is the maximum number of boxes
that can be put on the truck. You can choose any boxes to put on the truck as
long as the number of boxes does not exceed `truckSize`.

Return the maximum total number of units that can be put on the truck.

### Constraints

* `1 <= boxTypes.length <= 1000`
* `1 <= numberOfBoxesi, numberOfUnitsPerBoxi <= 1000`
* `1 <= truckSize <= 10^6`

### Examples

```text
Input: boxTypes = [[1,3],[2,2],[3,1]], truckSize = 4
Output: 8
Explanation: There are:
- 1 box of the first type that contains 3 units.
- 2 boxes of the second type that contain 2 units each.
- 3 boxes of the third type that contain 1 unit each.
You can take all the boxes of the first and second types, and one box of the third type.
The total number of units will be = (1 * 3) + (2 * 2) + (1 * 1) = 8.
```

```text
Input: boxTypes = [[5,10],[2,5],[4,7],[3,9]], truckSize = 10
Output: 91
```

## Solutions

### Greedy

```rust
pub fn maximum_units(mut box_types: Vec<Vec<i32>>, mut truck_size: i32) -> i32 {
    // Sort in decreasing order by the amount of units per box
    box_types.sort_unstable_by_key(|b| -b[1]);

    let mut units = 0;
    for b in box_types {
        // take as much as bossible from the given box type
        let to_take = truck_size.min(b[0]);
        truck_size -= to_take;
        units += to_take * b[1];

        // If we have filled the whole truck, then stop
        if truck_size == 0 {
            break;
        }
    }

    units
}
```
