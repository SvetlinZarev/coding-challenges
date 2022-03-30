# [860. Lemonade Change](https://leetcode.com/problems/lemonade-change/)

## Problem

### Description

At a lemonade stand, each lemonade costs `$5`. Customers are standing in a queue
to buy from you and order one at a time (in the order specified by bills). Each
customer will only buy one lemonade and pay with either a `$5`, `$10`, or `$20`
bill. You must provide the correct change to each customer so that the net
transaction is that the customer pays `$5`.

Note that you do not have any change in hand at first.

Given an integer array `bills` where `bills[i]` is the bill the `i`th customer
pays, return `true` if you can provide every customer with the correct change,
or `false` otherwise.

### Constraints

* `1 <= bills.length <= 10^5`
* `bills[i]` is either `5`, `10`, or `20`.

### Examples

```text
Input: bills = [5,5,5,10,20]
Output: true
Explanation: 
From the first 3 customers, we collect three $5 bills in order.
From the fourth customer, we collect a $10 bill and give back a $5.
From the fifth customer, we give a $10 bill and a $5 bill.
Since all customers got correct change, we output true.
```

```text
Input: bills = [5,5,10,10,20]
Output: false
Explanation: 
From the first two customers in order, we collect two $5 bills.
For the next two customers in order, we collect a $10 bill and give back a $5 bill.
For the last customer, we can not give the change of $15 back because we only have two $10 bills.
Since not every customer received the correct change, the answer is false.
```

## Solutions

### Greedy

```rust
pub fn lemonade_change(bills: Vec<i32>) -> bool {
    let mut five = 0;
    let mut ten = 0;

    for b in bills {
        match b {
            5 => five += 1,
            10 => ten += 1,
            _ => {}
        };

        let mut x = b;
        while x > 10 && ten > 0 {
            ten -= 1;
            x -= 10;
        }

        while x > 5 && five > 0 {
            five -= 1;
            x -= 5;
        }

        if x != 5 {
            return false;
        }
    }

    true
}
```