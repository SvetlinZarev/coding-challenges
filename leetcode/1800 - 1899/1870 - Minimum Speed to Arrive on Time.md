# [1870. Minimum Speed to Arrive on Time](https://leetcode.com/problems/minimum-speed-to-arrive-on-time/)

## Problem

### Description

You are given a floating-point number `hour`, representing the amount of time
you have to reach the office. To commute to the office, you must take `n` trains
in sequential order. You are also given an integer array dist of length `n`,
where `dist[i]` describes the distance (in kilometers) of the `i`th train ride.

Each train can only depart at an integer hour, so you may need to wait in
between each train ride.

* For example, if the 1st train ride takes `1.5` hours, you must wait for an
  additional `0.5` hours before you can depart on the 2nd train ride at the 2
  hour mark.

Return the minimum positive integer speed (in kilometers per hour) that all the
trains must travel at for you to reach the office on time, or `-1` if it is
impossible to be on time.

Tests are generated such that the answer will not exceed `10^7` and hour will
have at most two digits after the decimal point.

### Constraints

* `n == dist.length`
* `1 <= n <= 10^5`
* `1 <= dist[i] <= 10^5`
* `1 <= hour <= 10^9`
* There will be at most two digits after the decimal point in hour.

### Examples

```text
Input: dist = [1,3,2], hour = 6
Output: 1
Explanation: At speed 1:
- The first train ride takes 1/1 = 1 hour.
- Since we are already at an integer hour, we depart immediately at the 1 hour mark. The second train takes 3/1 = 3 hours.
- Since we are already at an integer hour, we depart immediately at the 4 hour mark. The third train takes 2/1 = 2 hours.
- You will arrive at exactly the 6 hour mark.
```

```text
Input: dist = [1,3,2], hour = 2.7
Output: 3
Explanation: At speed 3:
- The first train ride takes 1/3 = 0.33333 hours.
- Since we are not at an integer hour, we wait until the 1 hour mark to depart. The second train ride takes 3/3 = 1 hour.
- Since we are already at an integer hour, we depart immediately at the 2 hour mark. The third train takes 2/3 = 0.66667 hours.
- You will arrive at the 2.66667 hour mark.
```

```text
Input: dist = [1,3,2], hour = 1.9
Output: -1
Explanation: It is impossible because the earliest the third train can depart is at the 2 hour mark.
```

## Solutions

This problem follows the common pattern:

```text
fn binary_search(array) -> X:
    left, right = 0, len(array)
    while left < right:
        mid = left + (right - left) / 2
        if condition(mid):
            right = mid
        else:
            left = mid + 1
    return left
```

### Binary search

```rust
use std::ops::Sub;

const NO_SOLUTION: i32 = -1;

pub fn min_speed_on_time(dist: Vec<i32>, hour: f64) -> i32 {
    assert!(dist.len() > 0);
    assert!(hour > 0.0);

    // Because each train starts at an exact hour, it's not possible to reach the target
    // if there are more trains than hours. The exception is the last train, because we do
    // not perform the `ceil()` operation on it.
    if hour <= dist.len().sub(1) as f64 {
        return NO_SOLUTION;
    }

    let mut lo = 1;
    // `i32::MAX is a safe upper bound and because of the logarithmic
    // complexity it won't take much time to reach the correct value:
    //
    // let mut hi = i32::MAX;
    //
    // But we can take advantage of the problem constraints. The 
    // "two decimal places" means that we must multiply the maximum
    // speed by a factor of 100
    let mut hi = dist.iter().copied().max().unwrap_or(0) * 100;

    while lo < hi {
        let speed = lo + (hi - lo) / 2;
        let arrival = time(&dist, speed);

        if arrival > hour {
            // It can overflow only if the number of trains is greater 
            // than the hours, but we have already checked that, so it 
            // cannot overflow here
            lo = speed + 1;
        } else {
            hi = speed;
        }
    }

    hi
}

fn time(dist: &[i32], speed: i32) -> f64 {
    let t: i32 = dist
        .iter()
        .take(dist.len() - 1)
        .copied()
        // we can use integer division instead of floats + `.ceil()` :)
        .map(|x| x / speed + (x % speed != 0) as i32)
        .sum();

    t as f64 + dist[dist.len() - 1] as f64 / speed as f64
}
```

## Related Problems

* [69. Sqrt(x)](/leetcode/000%20-%20099/69%20-%20Sqrt(x).md)
* [875. Koko Eating Bananas](/leetcode/800%20-%20899/875%20-%20Koko%20Eating%20Bananas.md)
* [1283. Find the Smallest Divisor Given a Threshold](/leetcode/1200%20-%201299/1283%20-%20Find%20the%20Smallest%20Divisor%20Given%20a%20Threshold.md)

