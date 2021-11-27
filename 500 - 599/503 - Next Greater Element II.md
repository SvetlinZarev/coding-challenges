# [503. Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/)

## Problem

Given a circular integer array `nums` (i.e., the next element
of `nums[nums.length - 1]` is `nums[0]`), return the next greater number for
every element in `nums`.

The next greater number of a number `x` is the first greater number to its
traversing-order next in the array, which means you could search circularly to
find its next greater number. If it doesn't exist, return `-1` for this number.

#### Examples

```text
Input: nums = [1,2,1]
Output: [2,-1,2]
```

> Explanation: The first 1's next greater number is 2;
> The number 2 can't find next greater number.
> The second 1's next greater number needs to search circularly, which is also 2.

```text
Input: nums = [1,2,3,4,3]
Output: [2,3,4,-1,4]
```

## Solution

````rust
pub fn next_greater_elements(nums: Vec<i32>) -> Vec<i32> {
    let mut stack = Vec::with_capacity(nums.len().min(16));
    let mut greatest = vec![-1; nums.len()];

    nums.iter()
        .copied()
        .cycle()
        .enumerate()
        .take(nums.len() * 2)
        .for_each(|(idx, n)| {
            while let Some(&pos) = stack.last() {
                if nums[pos] >= n {
                    break;
                }

                stack.pop();
                greatest[pos] = n;
            }

            stack.push(idx % nums.len());
        });

    greatest
}
````

## Related Problems

* [496. Next Greater Element I](/400%20-%20499/496%20-%20Next%20Greater%20Element%20I.md)