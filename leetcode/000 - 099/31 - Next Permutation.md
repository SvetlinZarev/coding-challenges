# [31. Next Permutation](https://leetcode.com/problems/next-permutation/)

## Problem

### Description

A permutation of an array of integers is an arrangement of its members into a
sequence or linear order.

* For example, for `arr = [1,2,3]`, the following are considered permutations of
  arr:
    * `[1,2,3]`
    * `[1,3,2]`
    * `[3,1,2]`
    * `[2,3,1]`

The next permutation of an array of integers is the next lexicographically
greater permutation of its integer. More formally, if all the permutations of
the array are sorted in one container according to their lexicographical order,
then the next permutation of that array is the permutation that follows it in
the sorted container. If such arrangement is not possible, the array must be
rearranged as the lowest possible order (i.e., sorted in ascending order).

* For example, the next permutation of `arr = [1,2,3]` is `[1,3,2]`.
* Similarly, the next permutation of `arr = [2,3,1]` is `[3,1,2]`.
* While the next permutation of `arr = [3,2,1]` is `[1,2,3]` because `[3,2,1]`
  does not have a lexicographical larger rearrangement.

Given an array of integers nums, find the next permutation of `nums`.

The replacement must be in place and use only constant extra memory.

### Constraints

* `1 <= nums.length <= 100`
* `0 <= nums[i] <= 100`

### Examples

```text
Input: nums = [1,2,3]
Output: [1,3,2]
```

```text
Input: nums = [3,2,1]
Output: [1,2,3]
```

```text
Input: nums = [1,1,5]
Output: [1,5,1]
```

## Solutions

#### Algorithm:

1. Iterate the array from right to left and find the first integer that is
   smaller than the previous one
    * If there i sno such number, then we are currently in lexicographically
      largest permutation, so we need to generate the lexicographically smallest
      one - i.e. - sort the numbers in ascending order.
2. Find the smallest number that is larger than the number we've found in the
   previous step. Search only on the right portion of the array, at the right of
   that number.
    * There is always at least one such number - i.e. the one right next to this
      one.
3. Swap the number from step 1 and 2.
4. Sort the number to the right of the original position of the number from step
   1 in ascending order

```rust
pub fn next_permutation(nums: &mut Vec<i32>) {
    assert!(nums.len() >= 1);

    let sort_from = match numbers_to_swap(&nums) {
        None => {
            // There is no lexicographically larger next permutation, so we wrap
            // around and start from the smallest one
            0
        }
        Some((a, b)) => {
            nums.swap(a, b);
            a + 1
        }
    };

    nums[sort_from..].sort_unstable();
}

fn numbers_to_swap(array: &[i32]) -> Option<(usize, usize)> {
    let mut idx = array.len() - 1;

    while idx > 0 {
        idx -= 1;

        // We've found a number that's smaller than the previous one
        if array[idx] < array[idx + 1] {
            let mut pos = idx + 1;

            // Find the smallest number that is larger than the one we've just found
            for (loc, &val) in array.iter().enumerate().skip(idx + 1).rev() {
                if val > array[idx] {
                    pos = loc;
                    break;
                }
            }

            return Some((idx, pos));
        }
    }

    None
}
```

## Related Problems

* [556. Next Greater Element III](/leetcode/500%20-%20599/556%20-%20Next%20Greater%20Element%20III.md)