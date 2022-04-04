# [1385. Find the Distance Value Between Two Arrays](https://leetcode.com/problems/find-the-distance-value-between-two-arrays/)

## Problem

### Description

Given two integer arrays `arr1` and `arr2`, and the integer `d`, return the
distance value between the two arrays.

The distance value is defined as the number of elements `arr1[i]` such that
there is not any element `arr2[j]` where `|arr1[i]-arr2[j]| <= d`.

**Note:** the problem statement says that all numbers in `arr2` should be either
bigger or smaller than the elements in `arr1` with a difference of at
least `d+1`.

### Constraints

* `1 <= arr1.length, arr2.length <= 500`
* `-1000 <= arr1[i], arr2[j] <= 1000`
* `0 <= d <= 100`

### Examples

```text
Input: arr1 = [4,5,8], arr2 = [10,9,1,8], d = 2
Output: 2
```

```text
Input: arr1 = [1,4,2,3], arr2 = [-4,-3,6,10,20,30], d = 3
Output: 2
```

```text
Input: arr1 = [2,1,100,3], arr2 = [-5,-2,10,-3,7], d = 6
Output: 1
```

## Solutions

### Binary search

```rust
pub fn find_the_distance_value(arr1: Vec<i32>, arr2: Vec<i32>, d: i32) -> i32 {
    let a = arr1;
    let b = {
        let mut array = arr2;
        array.sort_unstable();
        array.dedup();
        array
    };

    let mut count = 0;
    for x in a.into_iter() {
        match b.binary_search(&x) {
            Ok(_) => continue,
            Err(idx) => {
                // if there are smaller elements
                if idx > 0 && (x - b[idx - 1]).abs() <= d {
                    continue;
                }

                // if there are larger elements. Note that `idx` points to the position
                // where the first larger element should be
                if idx < b.len() && (x - b[idx]).abs() <= d {
                    continue;
                }
            }
        }

        count += 1;
    }

    count
}
```

Instead of sorting the `arr2` array, this can be solved by using a tree, such
as `BTreeSet` and finding the immediate smaller/larger elements and calculating
their difference.