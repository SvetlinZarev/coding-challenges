[158A - Next Round](https://codeforces.com/problemset/problem/158/A)

## Problem

* time limit per test: 3 seconds
* memory limit per test: 256 megabytes

### Description

"Contestant who earns a score equal to or greater than the `k`-th place
finisher's score will advance to the next round, as long as the contestant earns
a positive score..." — an excerpt from contest rules.

A total of `n` participants took part in the contest (`n>=k`), and you already
know their scores. Calculate how many participants will advance to the next
round.

#### Input

The first line of the input contains two integers `n`
and `k` (`1 <=k <= n <= 50`)  separated by a single space.

The second line contains `n` space-separated
integers `a1 ,a2,...,an` (`0 ≤ ai ≤ 100`), where `ai` is the score earned by the
participant who got the `i`-th place. The given sequence is non-increasing (that
is, for all `i` from `1` to `n - 1` the following condition is
fulfilled: `ai ≥ ai +1`).

#### Output

Output the number of participants who advance to the next round.

#### Examples

In the first example the participant on the 5th place earned 7 points. As the
participant on the 6th place also earned 7 points, there are 6 advancers.

In the second example nobody got a positive score.

```
Input:

8 5
10 9 8 7 7 7 5 5

Output: 
6
```

```
Input:

4 2
0 0 0 0

Output: 
0
```

## Solution

```rust
use std::io::{stdin, Read};

fn main() {
    let mut buffer = String::new();
    stdin().read_to_string(&mut buffer).unwrap();

    let mut iter = buffer.split_whitespace();
    let n = iter.next().unwrap().trim().parse::<u32>().unwrap();
    let k = iter.next().unwrap().trim().parse::<u32>().unwrap();

    let mut count = 0;
    let mut kth = 0;
    for _ in 0..k {
        kth = iter.next().unwrap().trim().parse::<u32>().unwrap();
        if kth == 0 {
            break;
        }

        count += 1;
    }

    // because the sequence is non-increasing, if it has reached 0, then 
    // there is no point in checking the rest of the numbers, because they 
    // cannot be bigger, and 0 is not accepted
    if kth > 0 {
        for _ in k..n {
            let score = iter.next().unwrap().trim().parse::<u32>().unwrap();
            if score < kth {
                break;
            }

            count += 1;
        }
    }

    println!("{}", count);
}
```
