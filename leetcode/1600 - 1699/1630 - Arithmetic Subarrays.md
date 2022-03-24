# [1630. Arithmetic Subarrays](https://leetcode.com/problems/arithmetic-subarrays/)

## Problem

### Description

A sequence of numbers is called arithmetic if it consists of at least two
elements, and the difference between every two consecutive elements is the same.
More formally, a sequence `s` is arithmetic if and only
if `s[i+1] - s[i] == s[1] - s[0]` for all valid `i`.

* For example, these are arithmetic sequences:

```text
1, 3, 5, 7, 9
7, 7, 7, 7
3, -1, -5, -9
```

* The following sequence is not arithmetic:

```text
1, 1, 2, 5, 7
```

You are given an array of `n` integers, `nums`, and two arrays of `m` integers
each, `l` and `r`, representing the `m` range queries, where the `i`th query is
the range `[l[i], r[i]]`. All the arrays are 0-indexed.

Return a list of boolean elements `answer`, where `answer[i]` is `true` if the
subarray `nums[l[i]], nums[l[i]+1], ... , nums[r[i]]` can be rearranged to form
an arithmetic sequence, and `false` otherwise.

### Constraints

* `n == nums.length`
* `m == l.length`
* `m == r.length`
* `2 <= n <= 500`
* `1 <= m <= 500`
* `0 <= l[i] < r[i] < n`
* `-10^5 <= nums[i] <= 10^5`

### Examples

```text
Input: nums = [4,6,5,9,3,7], l = [0,0,2], r = [2,3,5]
Output: [true,false,true]
Explanation:
In the 0th query, the subarray is [4,6,5]. This can be rearranged as [6,5,4], which is an arithmetic sequence.
In the 1st query, the subarray is [4,6,5,9]. This cannot be rearranged as an arithmetic sequence.
In the 2nd query, the subarray is [5,9,3,7]. This can be rearranged as [3,5,7,9], which is an arithmetic sequence.
```

```text
Input: nums = [-12,-9,-3,-12,-6,15,20,-25,-20,-15,-10], l = [0,1,6,4,8,7], r = [4,4,9,7,9,10]
Output: [false,true,false,false,true,true]
```

## Solutions

### Naive implementation (O(n * m * logm))

For each range, we create a copy which we sort in order to check if it is
arithmetic:

```rust
pub fn check_arithmetic_subarrays(nums: Vec<i32>, l: Vec<i32>, r: Vec<i32>) -> Vec<bool> {
    let mut answer = vec![false; l.len()];

    l.iter()
        .map(|x| *x as usize)
        .zip(r.iter().map(|x| *x as usize + 1))
        .enumerate()
        .for_each(|(idx, (a, b))| {
            if b - a == 1 {
                answer[idx] = true;
                return;
            }

            let mut slice = nums[a..b].to_vec();
            slice.sort_unstable();

            let diff = slice[1] - slice[0];
            answer[idx] = slice.windows(2).all(|w| w[1] - w[0] == diff)
        });

    answer
}
```

We can slightly optimize that solution by avoiding the unnecessary slice
allocations:

```rust
pub fn check_arithmetic_subarrays(nums: Vec<i32>, l: Vec<i32>, r: Vec<i32>) -> Vec<bool> {
    let mut answer = vec![false; l.len()];
    let mut buffer = vec![];

    l.iter()
        .map(|x| *x as usize)
        .zip(r.iter().map(|x| *x as usize + 1))
        .enumerate()
        .for_each(|(idx, (a, b))| {
            if b - a == 1 {
                answer[idx] = true;
                return;
            }

            buffer.clear();
            buffer.extend_from_slice(&nums[a..b]);
            buffer.sort_unstable();

            let diff = buffer[1] - buffer[0];
            answer[idx] = buffer.windows(2).all(|w| w[1] - w[0] == diff)
        });

    answer
}
```

### O(n * m) implementation

Instead of copying and sorting each slice over and over again, we can calculate
the expected position within the slice for each number. I.e. we can sort it in
O(m) time instead of O(m*logm) time.

If the slice is arithmetic, each number can be represented as `base + idx*step`,
where `base` is the smallest number, `step` is the difference between two
consecutive elements of the arithmetic slice, and `idx` is the 0-index position
of the number. Given that we can calculate the expected position of each number
from the known `base` and `step`.

Base is the minimum number. And the step can be obtained by subtracting the
smallest number from the largest number. Then dividing that by the number of
elements minus one.

If the slice is arithmetic, then each "position" will be visited exactly once:

```rust
pub fn check_arithmetic_subarrays(nums: Vec<i32>, l: Vec<i32>, r: Vec<i32>) -> Vec<bool> {
    let mut answer = vec![false; l.len()];
    let mut buffer = vec![];

    l.iter()
        .map(|x| *x as usize)
        .zip(r.iter().map(|x| *x as usize + 1))
        .enumerate()
        .for_each(|(range_idx, (a, b))| {
            assert!(a < b);

            // Find the smallest and the largest numbers in the range
            // specified by `l[i]` and `r[i]`.
            let mut largest = i32::MIN;
            let mut smallest = i32::MAX;

            for &x in &nums[a..b] {
                largest = largest.max(x);
                smallest = smallest.min(x);
            }

            // if the smallest is equal to he largest, then this is an arithmetic slice,
            // where the difference brtween the elements is equal to zero
            if smallest == largest {
                answer[range_idx] = true;
                return;
            }

            // If the difference between the largest and the smallest is not
            // divisible by the number of elements minus 1, then the slice
            // is not arithmetic - i.e. the difference between some two numbers
            // will not be equal to the rest of the differences
            if (largest - smallest) as usize % (b - a - 1) != 0 {
                return;
            }

            // Calculate the step for the arithmetic sequence
            let step = (largest - smallest) / (b - a - 1) as i32;

            // We'll use this buffer to keep track if we have already visited this
            // numer. If we have visited all numbers => then the slice is arithmetic,
            // otherwise it is not. We can visit each number at most once
            buffer.resize(b - a, 0);

            // Keep track how many of the numbers we have already visited. We'll track the
            // count here instead of doing an additional loop over the `buffer` slice later
            let mut filled_positions = 0;

            // Loop over each number in the slice and calculate where its "sorted" position should be.
            // We can do this in O(n) instead of using a traditional sorting algorithm.
            for &x in &nums[a..b] {
                if (x - smallest) % step != 0 {
                    // if we subtract the smallest number from the value it
                    // should be a multiple of the step. If it's not, then
                    // the slice is not arithmetic
                    break;
                }

                let position = ((x - smallest) / step) as usize;
                if buffer[position] == range_idx + 1 {
                    // we have already visited this number, thus at least
                    // one position will remain unvisited => not an arithmetic
                    // slice
                    break;
                }

                // mark the position as visited
                buffer[position] = range_idx + 1;

                // count the newly visited number
                filled_positions += 1;
            }

            // if we have visited all numbers in the slice, then the slice is arithmetic
            answer[range_idx] = filled_positions == b - a;
        });

    answer
}
```