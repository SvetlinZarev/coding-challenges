# [122. Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)

## Problem

You are given an integer array prices where `prices[i]` is the price 
of a given stock on the `i`th day.

On each day, you may decide to buy and/or sell the stock. You can only 
hold at most one share of the stock at any time. However, you can buy 
it then immediately sell it on the same day.

Find and return the maximum profit you can achieve.


#### Constraints

* `1 <= prices.length <= 3 * 10^4`
* `0 <= prices[i] <= 10^4`

#### Examples

```text
Input: prices = [7,1,5,3,6,4]
Output: 7
Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
Total profit is 4 + 3 = 7.
```

```text
Input: prices = [1,2,3,4,5]
Output: 4
Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
Total profit is 4.
```

```text
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: There is no way to make a positive profit, so we never 
buy the stock to achieve the maximum profit of 0.
```

## Solutions

## Single pass

* `[1, 7, 2, 3, 6, 7, 6, 7]`


The graph for that array is: 

```text
                                   D: A + B + C                                                                   
                               |--------------------|                                                             
                               |                    |                                                             
                               |                    |                                                             
                               |                    |                                                             
          |                    |                                                                                  
         7|             \      |              C: +1--\           --                                               
          |            /||     |                --/ | ---\    --/ |                                               
         6|            ||\     |             --/    |     ---/    |                                               
          |           / | \    |            /|      |      |      |                                               
         5|           | |  |   |      B: +2/ |      |      |      |                                               
          |          /  |  \              /  |      |      |      |                                               
         4|         /   |   \            /   |      |      |      |                                               
          |         |   |    \  A: +1   /    |      |      |      |                                               
         3|        /    |     |      --/     |      |      |      |                                               
          |        |    |     \  ---/ |      |      |      |      |                                               
         2|       /     |      -/     |      |      |      |      |                                               
          |       |     |      |      |      |      |      |      |                                               
         1|      /      |      |      |      |      |      |      |                                               
          |      |      |      |      |      |      |      |      |                                               
          +-----------------------------------------------------------------                                      
          0      1      2      3      4      5      6      7      8                                               
                                                                                    
```

From the above graph, we can observe that the sum `A+B+C` is equal to the difference `D` 
corresponding to the difference between the heights of the consecutive peak and valley.

```rust
pub fn max_profit(prices: Vec<i32>) -> i32 {
    let mut profit = 0;

    for idx in 1..prices.len() {
        profit += 0.max(prices[idx] - prices[idx - 1]);
    }

    profit
}
```
