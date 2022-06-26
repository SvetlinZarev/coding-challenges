# [1423. Maximum Points You Can Obtain from Cards](https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/)

## Problem

### Description

There are several cards arranged in a row, and each card has an associated
number of points. The points are given in the integer array `cardPoints`.

In one step, you can take one card from the beginning or from the end of the
row. You **have to take exactly `k`** cards.

Your score is the sum of the points of the cards you have taken.

Given the integer array `cardPoints` and the integer `k`, return the maximum
score you can obtain.

### Constraints

* `1 <= cardPoints.length <= 10^5`
* `1 <= cardPoints[i] <= 10^4`
* `1 <= k <= cardPoints.length`

### Examples

#### Example 1

```text
Input: cardPoints = [1,2,3,4,5,6,1], k = 3
Output: 12
```

> Explanation: After the first step, your score will always be 1. However,
> choosing the rightmost card first will maximize your total score. The optimal
> strategy is to take the three cards on the right, giving a final score of 1 +
> 6 + 5 = 12

#### Example 2

```text
Input: cardPoints = [2,2,2], k = 2
Output: 4
```

> Explanation: Regardless of which two cards you take, your score will always be
> 4

#### Example 3

 ```text
Input: cardPoints = [9,7,7,9,7,7,9], k = 7
Output: 55
```

> Explanation: You have to take all the cards. Your score is the sum of points
> of all cards.

## Solutions

### Sliding Window (with loops)

Because we have to take cards either from the left or right, we will end up with
a continuous array of "discarded" cards at the middle.

So we can make the problem simpler by finding the sub-array of
length `(card_points.len() - k)` that has minimal sum. Then we just have to
subtract that minimal sum from total sum

```rust
pub fn max_score<P: AsRef<[i32]>>(card_points: P, k: i32) -> i32 {
    let points = card_points.as_ref();
    assert!(k > 0 && k as usize <= points.len());

    // We have to to draw K cards, which means we have to discard
    // `number_of_cards - K` cards
    let discard = points.len() - k as usize;

    let mut l = 0;
    let mut r = 0;

    let mut min_window = i32::MAX;
    let mut cur_window = 0;
    let mut total_sum = 0;

    while r < points.len() {
        total_sum += points[r];
        cur_window += points[r];

        r += 1;

        if r - l > discard {
            cur_window -= points[l];
            l += 1;
        }

        if r - l == discard {
            min_window = min_window.min(cur_window);
        }
    }

    total_sum - min_window
}
```

### Sliding Window (with iterators)

The same idea as above, but implemented with iterators:

```rust
pub fn max_score<P: AsRef<[i32]>>(card_points: P, k: i32) -> i32 {
    let points = card_points.as_ref();
    assert!(k > 0 && k as usize <= points.len());

    // We have to to draw K cards, which means we have to discard
    // `number_of_cards - K` cards
    let discard = points.len() - k as usize;

    let mut cur_window = points[..discard].iter().sum::<i32>();
    let mut min_window = cur_window;
    let mut total_sum = cur_window;

    points.windows(discard + 1).for_each(|w| {
        total_sum += w[discard];
        cur_window += w[discard] - w[0];
        min_window = min_window.min(cur_window);
    });

    total_sum - min_window
}
```

## Related Problems

* [1658. Minimum Operations to Reduce X to Zero](/leetcode/1600%20-%201699/1658%20-%20Minimum%20Operations%20to%20Reduce%20X%20to%20Zero.md)
