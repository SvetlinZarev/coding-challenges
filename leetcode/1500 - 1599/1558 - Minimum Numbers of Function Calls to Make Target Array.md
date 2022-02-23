# [1558. Minimum Numbers of Function Calls to Make Target Array](https://leetcode.com/problems/minimum-numbers-of-function-calls-to-make-target-array/)

## Problem

You are given an integer array `nums`. You have an integer array `arr` of the
same length with all values set to `0` initially. You also have the
following `modify` function:

```text
func modify(arr, op, idx){
    if op == 0{
        arr[idx] += 1;
    }
    
    if op == 2{
        for i in 0..arr.length{
            arr[i]  *= 2;
        }
    }
}
```

You want to use the `modify` function to covert `arr` to `nums` using the
minimum number of calls.

Return the minimum number of function calls to make `nums` from `arr`.

The test cases are generated so that the answer fits in a 32-bit signed integer.

#### Constraints

* `1 <= nums.length <= 10^5`
* `0 <= nums[i] <= 10^9`

#### Examples

```text
Input: nums = [1,5]
Output: 5
Explanation: Increment by 1 (second element): [0, 0] to get [0, 1] (1 operation).
Double all the elements: [0, 1] -> [0, 2] -> [0, 4] (2 operations).
Increment by 1 (both elements)  [0, 4] -> [1, 4] -> [1, 5] (2 operations).
Total of operations: 1 + 2 + 2 = 5.
```

```text
Input: nums = [2,2]
Output: 3
Explanation: Increment by 1 (both elements) [0, 0] -> [0, 1] -> [1, 1] (2 operations).
Double all the elements: [1, 1] -> [2, 2] (1 operation).
Total of operations: 2 + 1 = 3.
```

```text
Input: nums = [4,2,5]
Output: 6
Explanation: (initial)[0,0,0] -> [1,0,0] -> [1,0,1] -> [2,0,2] -> [2,1,2] -> [4,2,4] -> [4,2,5](nums).
```

#### Hints

* Work backwards: try to go from `nums` to `arr`.
* You should try to divide by 2 as much as possible, but you can only divide by
  2 if everything is even.

## Solution

```rust
pub fn min_operations(mut nums: Vec<i32>) -> i32 {
    let mut ops = 0;

    loop {
        let mut non_zero = 0;
        for n in nums.iter_mut() {
            if *n == 0 {
                continue;
            }

            if *n % 2 != 0 {
                *n -= 1;
                ops += 1;
            }

            if *n != 0 {
                non_zero += 1;
            }
        }

        if non_zero == 0 {
            break;
        }

        ops += 1;
        nums.iter_mut().for_each(|n| *n = *n / 2);
    }

    ops
}
```