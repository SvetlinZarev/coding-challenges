# [287. Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)

## Problem

Given an array of integers `nums` containing `n + 1` integers where each integer
is in the range `[1, n]` inclusive.

There is only one repeated number in `nums`, return this repeated number.

You must solve the problem without modifying the array `nums` and uses only
constant extra space.

#### Constraints

* `1 <= n <= 10^5`
* `nums.length == n + 1`
* `1 <= nums[i] <= n`
* All the integers in `nums` appear only once except for precisely one integer
  which appears two or more times.

#### Examples

```text
Input: nums = [1,3,4,2,2]
Output: 2
```

```text
Input: nums = [3,1,3,4,2]
Output: 3
```

## Solution

### Cycle detection

We can view the array as linked list, where each number points to its next node.
Then we can
apply [Floyd's](https://en.wikipedia.org/wiki/Cycle_detection#Floyd's_tortoise_and_hare)
cycle detection algorithm. Once we detect the cycle we can find out where the
cycle starts - and that's the answer to this problem.

```rust
pub fn find_duplicate(nums: Vec<i32>) -> i32 {
    let mut slow = nums[0];
    let mut fast = nums[0];

    loop {
        slow = nums[slow as usize];
        fast = nums[fast as usize];
        fast = nums[fast as usize];

        if slow == fast { // we have found the "meeting point"
            // reset "slow" back to the list's "head", in out case the 0th index
            // in order to find out where the cycle starts
            slow = nums[0];
            while slow != fast { // "fast" and "slow" will meet where the cycle starts
                slow = nums[slow as usize];
                fast = nums[fast as usize];
            }

            return slow;
        }
    }
}
```

## Related Problems

* [141. Linked List Cycle](/100%20-%20199/141%20-%20Linked%20List%20Cycle.md)
* [142. Linked List Cycle II](/100%20-%20199/142%20-%20Linked%20List%20Cycle%20II.md)