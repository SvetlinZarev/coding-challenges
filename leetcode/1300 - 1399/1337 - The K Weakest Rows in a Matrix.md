# [1337. The K Weakest Rows in a Matrix](https://leetcode.com/problems/the-k-weakest-rows-in-a-matrix/)

## Problem

### Description

You are given an `m x n` binary matrix `mat` of `1`'s (representing soldiers)
and `0`'s (representing civilians). The soldiers are positioned in front of the
civilians. That is, all the `1`'s will appear to the left of all the 0's in each
row.

A row `i` is weaker than a row `j` if one of the following is true:

* The number of soldiers in row `i` is less than the number of soldiers in
  row `j`.
* Both rows have the same number of soldiers and `i < j`.

Return the indices of the `k` weakest rows in the matrix ordered from weakest to
strongest.

### Constraints

* `m == mat.length`
* `n == mat[i].length`
* `2 <= n, m <= 100`
* `1 <= k <= m`
* `matrix[i][j]` is either `0` or `1`.

### Examples

```text
Input: mat = 
[[1,1,0,0,0],
 [1,1,1,1,0],
 [1,0,0,0,0],
 [1,1,0,0,0],
 [1,1,1,1,1]], 
k = 3
Output: [2,0,3]
Explanation: 
The number of soldiers in each row is: 
- Row 0: 2 
- Row 1: 4 
- Row 2: 1 
- Row 3: 2 
- Row 4: 5 
The rows ordered from weakest to strongest are [2,0,3,1,4].
```

```text
Input: mat = 
[[1,0,0,0],
 [1,1,1,1],
 [1,0,0,0],
 [1,0,0,0]], 
k = 2
Output: [0,2]
Explanation: 
The number of soldiers in each row is: 
- Row 0: 1 
- Row 1: 4 
- Row 2: 1 
- Row 3: 1 
The rows ordered from weakest to strongest are [0,2,3,1].
```

## Solutions

### Using a Priority Queue

Time complexity: `O(n log k)` where `n` is the number of rows and `k` is the
number of smallest rows we are looking for

```rust
use std::collections::BinaryHeap;

pub fn k_weakest_rows(mat: Vec<Vec<i32>>, k: i32) -> Vec<i32> {
    assert!(k >= 1);
    assert!(mat.len() >= k as usize);

    let mut pq = BinaryHeap::with_capacity(k as usize);

    for (idx, row) in mat.into_iter().enumerate() {
        // We can use `.take_while()` instead of `.filter()` because the proplem statement
        // guarantees us that there will not be any 1s after a 0. Thus we can do fewer
        // loops by stopping early
        let count = row.into_iter().take_while(|&x| x == 1).count();

        // One option is to always push and if the length i smore than `k`, then pop,
        // but we can take advantage of tha fact that `.peek()` is O(1) and thus do
        // fewer O(log N) `.pop()` operations by popping only if the current element
        // is smaller than the PQ's largest one.
        if pq.len() < k as usize {
            pq.push((count, idx));
        } else {
            // SAFETY: we will always have exact;y `k` elements in the PQ
            let &(max_count, max_count_idx) = unsafe { pq.peek().unwrap_unchecked() };
            if count < max_count || (count == max_count && idx < max_count_idx) {
                pq.pop();
                pq.push((count, idx));
            }
        }
    }

    // Iterate in reverse order, because we are poping the largest elements first
    let mut answer = vec![0; k as usize];
    for x in answer.iter_mut().rev() {
        // SAFETY: we will always have exact;y `k` elements in the PQ
        *x = unsafe { pq.pop().unwrap_unchecked() }.1 as i32;
    }

    answer
}
```

Instead of counting the `1`s one by one, we can do a binary search to find where
the `0`s begin. That index is also our `1`s count:

```rust
use std::collections::BinaryHeap;

pub fn k_weakest_rows(mat: Vec<Vec<i32>>, k: i32) -> Vec<i32> {
    assert!(k >= 1);
    assert!(mat.len() >= k as usize);

    let mut pq = BinaryHeap::with_capacity(k as usize);

    for (idx, row) in mat.into_iter().enumerate() {
        // Because the array is sorted (i.e. there are no 1s after a 0, we can use a binary
        // search to find the first index of a 0. That's our 1s count
        let count = count_ones(&row);

        // One option is to always push and if the length i smore than `k`, then pop,
        // but we can take advantage of tha fact that `.peek()` is O(1) and thus do
        // fewer O(log N) `.pop()` operations by popping only if the current element
        // is smaller than the PQ's largest one.
        if pq.len() < k as usize {
            pq.push((count, idx));
        } else {
            // SAFETY: we will always have exact;y `k` elements in the PQ
            let &max_tuple = unsafe { pq.peek().unwrap_unchecked() };

            // Because we did not deconstruct our "max" tuple, we can just do a 
            // single comparison, unlike in the previous example. That's because 
            // we ordered it's fields in the right order - i.e. `(count, idx)`
            // and that single comparison is equivalent to the more complex one 
            // from the previous implementation
            if (count, idx) < max_tuple {
                pq.pop();
                pq.push((count, idx));
            }
        }
    }

    // Iterate in reverse order, because we are poping the largest elements first
    let mut answer = vec![0; k as usize];
    for x in answer.iter_mut().rev() {
        // SAFETY: we will always have exact;y `k` elements in the PQ
        *x = unsafe { pq.pop().unwrap_unchecked() }.1 as i32;
    }

    answer
}

fn count_ones(array: &[i32]) -> usize {
    let mut lo = 0;
    let mut hi = array.len();

    while lo < hi {
        let mid = (hi - lo) / 2 + lo;
        if array[mid] <= 0 {
            hi = mid;
        } else {
            lo = mid + 1;
        }
    }

    lo
}
```

### Using sorting instead of a priority queue

Time complexity: `O(n log n)` where `n` is the number of rows

```rust
pub fn k_weakest_rows(mat: Vec<Vec<i32>>, k: i32) -> Vec<i32> {
    assert!(k >= 1);
    assert!(mat.len() >= k as usize);

    let mut rows = mat
        .into_iter()
        .enumerate()
        // Or we can use the `binary search` trick from the previous solution
        .map(|(idx, row)| (row.into_iter().sum::<i32>(), idx))
        .collect::<Vec<_>>();

    // Note that if we construct the tuple `(count, idx), we need to do 
    // only 1 sort with the default comparator. But if we do it like
    // `(idx, count)`, then we either need to sort twice AND use a custom 
    // comparator
    rows.sort_unstable();

    rows.into_iter()
        .take(k as usize)
        .map(|(_, idx)| idx as i32)
        .collect()
}
```