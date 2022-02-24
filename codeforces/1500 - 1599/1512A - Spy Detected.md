[1512A - Spy Detected!](https://codeforces.com/problemset/problem/1512/A)

## Problem

### Description

You are given an array a consisting of `n` (`n≥3`) positive integers. It is
known that in this array, all the numbers except one are the same (for example,
in the array `[4,11,4,4]` all numbers except one are equal to `4`).

Print the index of the element that does not equal others. The numbers in the
array are numbered from one.

### Input

The first line contains a single integer `t` (`1≤t≤100`). Then `t` test cases
follow.

The first line of each test case contains a single integer `n` (`3≤n≤100`) — the
length of the array `a`.

The second line of each test case contains `n`
integers` a1,a2,…,an` (`1≤ai≤100`).

It is guaranteed that all the numbers except one in the `a` array are the same.

### Output

For each test case, output a single integer — the index of the element that is
not equal to others.

## Solution

### Boyer-Moore majority voting algorithm

```rust
use std::error::Error;
use std::io::BufRead;
use std::io::Write;
use std::ops::Sub;

fn main() -> Result<(), Box<dyn Error>> {
    let stdin = std::io::stdin();
    let mut input = stdin.lock();

    let stdout = std::io::stdout();
    let mut output = stdout.lock();

    let mut buffer = String::with_capacity(32);

    input.read_line(&mut buffer)?;
    let tests = buffer.trim().parse::<u32>()?;

    for _ in 0..tests {
        // read & discard the number of elements
        buffer.clear();
        input.read_line(&mut buffer)?;

        buffer.clear();
        input.read_line(&mut buffer)?;

        let (mut c1, mut i1, mut v1) = (0_i32, 0, "");
        let (mut c2, mut i2, mut v2) = (0_i32, 0, "");

        for (idx, n) in buffer.split_ascii_whitespace().enumerate() {
            if n == v1 {
                c1 += 1;
            } else if n == v2 {
                c2 += 1;
            } else if c1 == 0 {
                c1 = 1;
                i1 = idx;
                v1 = n;
            } else if c2 == 0 {
                c2 = 1;
                i2 = idx;
                v2 = n;
            } else {
                c1 -= 1;
                c2 -= 1;
            }

            if c1 != 0 && c2 != 0 && c1.sub(c2).abs() >= 1 {
                let to_print = if c1 > c2 { i2 } else { i1 } + 1;
                writeln!(output, "{}", to_print)?;
                break;
            }
        }
    }

    Ok(())
}
```