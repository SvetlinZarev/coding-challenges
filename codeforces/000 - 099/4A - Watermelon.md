[4A - Watermelon](https://codeforces.com/problemset/problem/4/A)

## Problem

* time limit per test: 1 second
* memory limit per test: 64 megabytes

### Description

One hot summer day Pete and his friend Billy decided to buy a watermelon. They
chose the biggest and the ripest one, in their opinion. After that the
watermelon was weighed, and the scales showed w kilos. They rushed home, dying
of thirst, and decided to divide the berry, however they faced a hard problem.

Pete and Billy are great fans of even numbers, that's why they want to divide
the watermelon in such a way that each of the two parts weighs even number of
kilos, at the same time it is not obligatory that the parts are equal. The boys
are extremely tired and want to start their meal as soon as possible, that's why
you should help them and find out, if they can divide the watermelon in the way
they want. For sure, each of them should get a part of positive weight.

#### Input

The first (and the only) input line contains integer
number `w` (`1 <= w <= 100`) — the weight of the watermelon bought by the boys.

#### Output

Print `YES`, if the boys can divide the watermelon into two parts, each of them
weighing even number of kilos; and `NO` in the opposite case.

## Solution

#### Observations

* The sum of any two odd numbers is an even number (i.e. `1 + 1 == 2`)
* The sum of any two even numbers is an even number (i.e. `2 + 2 == 4`)
* The sum of any odd and even number is an odd number (i.e. `3 + 4 == 7`)

Therefore, an odd number can never result in `YES`. And the only even number
that cannot be produced from even numbers is 2.

### Rust solution

```rust
use std::io::stdin;

const YES: &str = "YES";
const NO: &str = "NO";

fn main() {
    let weight = read_input();

    let answer = match weight & 1 == 0 && weight > 2 {
        true => YES,
        false => NO,
    };

    println!("{}", answer);
}

fn read_input() -> u32 {
    let mut buffer = String::new();

    let sin = stdin();
    sin.read_line(&mut buffer).unwrap();

    buffer.trim().parse().unwrap()
}
```