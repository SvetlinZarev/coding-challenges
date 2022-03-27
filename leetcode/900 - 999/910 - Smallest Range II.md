# [910. Smallest Range II](https://leetcode.com/problems/smallest-range-ii/)

## Problem

### Description

You are given an integer array `nums` and an integer `k`.

For each index `i` where `0 <= i < nums.length`, change `nums[i]` to be
either `nums[i] + k` or `nums[i] - k`.

The score of `nums` is the difference between the `maximum` and `minimum`
elements in `nums`.

Return the minimum score of `nums` after changing the values at each index.

### Constraints

* `1 <= nums.length <= 10^4`
* `0 <= nums[i] <= 10^4`
* `0 <= k <= 10^4`

### Examples

```text
Input: nums = [1], k = 0
Output: 0
Explanation: The score is max(nums) - min(nums) = 1 - 1 = 0.
```

```text
Input: nums = [0,10], k = 2
Output: 6
Explanation: Change nums to be [2, 8]. The score is max(nums) - min(nums) = 8 - 2 = 6.
```

```text
Input: nums = [1,3,6], k = 3
Output: 3
Explanation: Change nums to be [4, 6, 3]. The score is max(nums) - min(nums) = 6 - 3 = 3.
```

```text
Input: nums = [7,7,8], k = 5
Output: 1
```

### Solutions

```rust
pub fn smallest_range_ii(mut nums: Vec<i32>, k: i32) -> i32 {
    assert!(nums.len() >= 1);
    nums.sort_unstable();

    let last = nums[nums.len() - 1];
    let first = nums[0];

    let mut answer = last - first;
    // Fast path: If `K` is zero, then nothing changes, so we can tell 
    // immediately the answer. Also, if the difference is less then `K`, 
    // then we have to either add or remove `K` from all elements, because
    // if we add it to some and remove it from others, we'll get a worse 
    // result. But if we apply the same operation to all elements, then
    // it's equivalent to not doing anything.
    //
    //Examples: 
    // * Case 1 (k == diff): `[7,10],k=3` => If we do `7+3` & `10-3` it's as 
    //   if we did not do anything
    // * Case 2 (k > diff): `[7,10],k=5` => If we do `7+5` & `10-5` we get a 
    //   worse result, so it would have been better to apply the same operation 
    //   to both elements
    if k == 0 || answer <= k {
        return answer;
    }


    // Fast path: We can divide the range into 3 groups:
    // 1.) [min..=min+k]
    // 3.) [min+k+1..=max-k-1]
    // 3.) [max-k..=max]
    //
    // It's obvious why we'll always add `k` to group 1 and 
    // subtract `k` from group 3. Thus if we ignore the middle
    // group, the best we can get is `[min+k..=max-k]`, which
    // leads to: `max - k - (min+k) = max - min - 2*k
    //
    // So far we have removed 2K from our range, which means that
    // we have at least 2K remaining for range 2. We have 3 cases:
    // 1.) Subtract `k` from all elements in the middle range
    // 2.) Add `k` to all elements in the middle range
    // 3.) Add `k' to some elements and subtract `k` from others
    //
    // But whatever we do we remain in the range [min+k+1..=max-k-1],
    // thus if the range is at least `4k`, the answer is the following:
    if answer >= 4 * k {
        return answer -2 * k;
    }

    nums.windows(2).map(|w| (w[0], w[1])).for_each(|(a, b)| {
        let lo = std::cmp::min(first + k, b - k);
        let hi = std::cmp::max(last - k, a + k);
        answer = answer.min(hi - lo);
    });

    answer
}
```