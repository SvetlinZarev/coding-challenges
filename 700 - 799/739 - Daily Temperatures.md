# [739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/)

## Problem

Given an array of integers `temperatures` represents the daily temperatures,
return an array `answer` such that `answer[i]` is the number of days you have to
wait after the `i`th day to get a warmer temperature. If there is no future day
for which this is possible, keep `answer[i] == 0` instead.

#### Constraints

* `1 <= temperatures.length <= 10^5`
* `30 <= temperatures[i] <= 100`

#### Examples

```text
Input: temperatures = [73,74,75,71,69,72,76,73]
Output: [1,1,4,2,1,1,0,0]
```

```text
Input: temperatures = [30,40,50,60]
Output: [1,1,1,0]
```

```text
Input: temperatures = [30,60,90]
Output: [1,1,0]
```

## Solutions

### Using Monotonic Stack

```rust
pub fn daily_temperatures(temperatures: Vec<i32>) -> Vec<i32> {
    let mut solution = vec![0; temperatures.len()];
    let mut stack = Vec::with_capacity(16);

    for (idx, val) in temperatures.iter().copied().enumerate() {
        while let Some(&(top, pos)) = stack.last() {
            if top >= val {
                break;
            }

            stack.pop();
            solution[pos] = (idx - pos) as i32;
        }

        stack.push((val, idx));
    }

    solution
}
```