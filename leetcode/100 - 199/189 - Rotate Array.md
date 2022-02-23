# [189. Rotate Array](https://leetcode.com/problems/rotate-array/)

## Problem

Given an array, rotate the array to the right by `k` steps, where `k` is
non-negative.

#### Constraints

* `1 <= nums.length <= 10^5`
* `-2^31 <= nums[i] <= 2^31 - 1`
* `0 <= k <= 10^5`

#### Examples

```text
Input: nums = [1,2,3,4,5,6,7], k = 3
Output: [5,6,7,1,2,3,4]
Explanation:
rotate 1 steps to the right: [7,1,2,3,4,5,6]
rotate 2 steps to the right: [6,7,1,2,3,4,5]
rotate 3 steps to the right: [5,6,7,1,2,3,4]
```

```text
Input: nums = [-1,-100,3,99], k = 2
Output: [3,99,-1,-100]
Explanation: 
rotate 1 steps to the right: [99,-1,-100,3]
rotate 2 steps to the right: [3,99,-1,-100]
```

## Solution

### Long & complex solution

```rust
pub fn rotate(nums: &mut Vec<i32>, k: i32) {
    let k = k as usize % nums.len();
    if nums.len() <= 1 || k == 0 {
        return;
    }

    // It's more efficient :)
    if nums.len() == k * 2 {
        rotate_half(nums);
        return;
    }

    let cycle = gcd(nums.len(), k);
    rotate_cycle(nums, k, cycle);
}

fn rotate_half(nums: &mut Vec<i32>) {
    let h_len = nums.len() / 2;
    for i in 0..h_len {
        nums.swap(i, i + h_len);
    }
}

fn rotate_cycle(nums: &mut Vec<i32>, k: usize, cycle: usize) {
    let m = nums.len() / cycle;

    for i in 0..cycle {
        let mut tmp = nums[i];
        let mut idx = i;

        for _ in 0..m {
            idx = (idx + k) % nums.len() as usize;
            std::mem::swap(&mut tmp, &mut nums[idx]);
        }
    }
}

fn gcd(mut a: usize, mut b: usize) -> usize {
    while b != 0 {
        let t = b;
        b = a % b;
        a = t;
    }

    a
}
```

### Short & simple solution

```rust
pub fn rotate(nums: &mut Vec<i32>, k: i32) {
    let n = nums.len() as i32;
    let k = (k % n) as usize;
    nums.reverse();
    nums[..k].reverse();
    nums[k..].reverse();
}
```