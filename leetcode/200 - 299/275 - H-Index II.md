# [275. H-Index II](https://leetcode.com/problems/h-index-ii/)

## Problem

### Description

Given an array of integers `citations` where `citations[i]` is the number of
citations a researcher received for their `i`th paper and citations is sorted in
an ascending order, return compute the researcher's h-index.

According to the definition of h-index on Wikipedia: A scientist has an
index `h` if `h` of their `n` papers have at least `h` citations each, and the
other `n − h` papers have no more than `h` citations each.

If there are several possible values for `h`, the maximum one is taken as the
h-index.

You must write an algorithm that runs in logarithmic time.

### Constraints

* `n == citations.length`
* `1 <= n <= 10^5`
* `0 <= citations[i] <= 1000`
* `citations` is sorted in ascending order.

### Examples

```text
Input: citations = [0,1,3,5,6]
Output: 3
Explanation: [0,1,3,5,6] means the researcher has 5 papers in total and each of them had received 0, 1, 3, 5, 6 citations respectively.
Since the researcher has 3 papers with at least 3 citations each and the remaining two with no more than 3 citations each, their h-index is 3.
```

```text
Input: citations = [1,2,100]
Output: 2
```

## Solutions

### Binary search

```rust
pub fn h_index(citations: Vec<i32>) -> i32 {
    // Note that according to the problem statement, the vector
    // citations is already sorted in ascending order

    let mut lo = 0;
    let mut hi = citations.len();

    while lo < hi {
        let mid = lo + (hi - lo) / 2;

        // because the array is sorted in ascending order, the number of citations
        // that have at least H citations is the difference of `LENGTH - IDX`:
        if (citations[mid] as usize) < citations.len() - mid {
            lo = mid + 1;
        } else {
            hi = mid;
        }
    }

    (citations.len() - hi) as i32
}
```
