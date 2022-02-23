# [239. Sliding Window Maximum](https://leetcode.com/submissions/detail/552518632/)

## Problem

You are given an array of integers `nums`, there is a sliding window of size `k`
which is moving from the very left of the array to the very right. You can only
see the `k` numbers in the window. Each time the sliding window moves right by
one position.

Return the max sliding window.

#### Constraints

* `1 <= nums.length <= 10^5`
* `-10^4 <= nums[i] <= 10^4`
* `1 <= k <= nums.length`

#### Examples

```text
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
Explanation: 
Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

```text
Input: nums = [1], k = 1
Output: [1]
```

```text
Input: nums = [1,-1], k = 1
Output: [1,-1]
```

```text
Input: nums = [9,11], k = 2
Output: [11]```

```text
Input: nums = [4,-2], k = 2
Output: [4]
```

## Solution

```rust
use std::collections::VecDeque;

pub fn max_sliding_window(nums: Vec<i32>, k: i32) -> Vec<i32> {
    let mut result = Vec::with_capacity(nums.len() - k as usize + 1);
    let mut queue = VecDeque::with_capacity((k * 2) as usize); //monotonic queue

    for idx in 0..nums.len() {
        //remove old values
        while let Some(&pos) = queue.front() {
            if pos + k as usize > idx {
                break;
            }

            queue.pop_front();
        }

        // remove smallest values
        while let Some(&pos) = queue.back() {
            let old = nums[pos];
            if old > nums[idx] {
                break;
            }

            queue.pop_back();
        }

        queue.push_back(idx);

        // max is at the front of the queue
        if idx + 1 >= k as usize {
            if let Some(&max) = queue.front() {
                result.push(nums[max]);
            }
        }
    }

    result
}
```

## Topics

* Monotonic queue