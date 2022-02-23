# [740. Delete and Earn](https://leetcode.com/problems/delete-and-earn/)

## Problem

You are given an integer array `nums`. You want to maximize the number of points
you get by performing the following operation any number of times:

* Pick any `nums[i]` and delete it to earn `nums[i]` points. Afterwards, you
  must delete every element equal to `nums[i]` - 1 and every element equal
  to `nums[i] + 1`.

Return the maximum number of points you can earn by applying the above operation
some number of times.

#### Constraints

* `1 <= nums.length <= 2 * 10^4`
* `1 <= nums[i] <= 10^4`

#### Examples

```text
Input: nums = [3,4,2]
Output: 6
Explanation: You can perform the following operations:
- Delete 4 to earn 4 points. Consequently, 3 is also deleted. nums = [2].
- Delete 2 to earn 2 points. nums = [].
You earn a total of 6 points.
```

```text
Input: nums = [2,2,3,3,3,4]
Output: 9
Explanation: You can perform the following operations:
- Delete a 3 to earn 3 points. All 2's and 4's are also deleted. nums = [3,3].
- Delete a 3 again to earn 3 points. nums = [3].
- Delete a 3 once more to earn 3 points. nums = [].
You earn a total of 9 points.
```

#### Hints

> If you take a number, you might as well take them all. Keep track of what
> the value is of the subset of the input with maximum M when you either take
> or don't take M.

## Solution

```rust
pub fn delete_and_earn(mut nums: Vec<i32>) -> i32 {
    nums.sort_unstable();

    let mut data = vec![];
    let mut entry = None;
    for n in nums {
        entry = Some(match entry {
            None => (n, 1),
            Some((v, c)) => {
                if n == v {
                    (n, c + 1)
                } else {
                    data.push((v, c));
                    (n, 1)
                }
            }
        });
    }

    if let Some(entry) = entry {
        data.push(entry);
    }

    let mut dp = vec![0; data.len()];
    for idx in 0..data.len() {
        let mut add = 0;
        if idx >= 1 {
            if data[idx].0 - 1 != data[idx - 1].0 {
                add = dp[idx - 1];
            } else if idx >= 3 {
                add = add.max(dp[idx - 3]);
            }
        }

        if idx >= 2 {
            add = add.max(dp[idx - 2]);
        }

        dp[idx] = data[idx].0 * data[idx].1 + add;
    }

    //println!("{:?}", dp);
    dp.iter().copied().rev().take(2).max().unwrap_or(0)
}
```