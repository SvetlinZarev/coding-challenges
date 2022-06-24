# [121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)

## Problem

### Description

You are given an array prices where `prices[i]` is the price of a given stock on
the ith day.

You want to maximize your profit by choosing a single day to buy one stock and
choosing a different day in the future to sell that stock.

Return the maximum profit you can achieve from this transaction. If you cannot
achieve any profit, return 0.

### Constraints

* `1 <= prices.length <= 10^5`
* `0 <= prices[i] <= 10^4`

### Examples

```text
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```

 ```text
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transactions are done and the max profit = 0.
```

## Solutions

### Finding maximum difference

Because we are buying and selling at most 1 stock, we have to find the largest
difference between an element `x` and any other element that comes after it.

```rust
pub fn max_profit(prices: Vec<i32>) -> i32 {
    let mut max_profit = 0;
    let mut min_price = i32::MAX;

    for price in prices {
        // the minimum price found so far
        min_price = min_price.min(price);

        // the maximum difference found so far
        max_profit = max_profit.max(price - min_price);
    }

    max_profit
}
```

## Related problems

* [122. Best Time to Buy and Sell Stock II](122%20-%20Best%20Time%20to%20Buy%20and%20Sell%20Stock%20II.md)
