# [852. Peak Index in a Mountain Array](https://leetcode.com/problems/peak-index-in-a-mountain-array/)

## Problem

### Description

Let's call an array `arr` a mountain if the following properties hold:

* `arr.length >= 3`
* There exists some `i` with `0 < i < arr.length - 1` such that:
    * `arr[0] < arr[1] < ... arr[i-1] < arr[i]`
    * `arr[i] > arr[i+1] > ... > arr[arr.length - 1]`

Given an integer array `arr` that is guaranteed to be a mountain, return any `i`
such
that `arr[0] < arr[1] < ... arr[i - 1] < arr[i] > arr[i + 1] > ... > arr[arr.length - 1]`
.

### Constraints

* `3 <= arr.length <= 10^4`
* `0 <= arr[i] <= 10^6`
* `arr` is guaranteed to be a mountain array.

### Examples

```text
Input: arr = [0,1,0]
Output: 1
```

```text
Input: arr = [0,2,1,0]
Output: 1
```

```text
Input: arr = [0,10,5,2]
Output: 1
```

### Follow up

Follow up: Finding the `O(n)` is straightforward, could you find an `O(log(n))`
solution?

## Solutions

### Binary search (O(n log n))

```rust
pub fn peak_index_in_mountain_array(arr: Vec<i32>) -> i32 {
    let mut lo = 0;
    let mut hi = arr.len() - 1;

    while lo < hi {
        let mid = (hi - lo) / 2 + lo;
        if arr[mid] < arr[mid + 1] {
            lo = mid + 1;
        } else {
            hi = mid;
        }
    }

    hi as i32
}
```

## Related Problems

* [162. Find Peak Element](/leetcode/100%20-%20199/162%20-%20Find%20Peak%20Element.md)