# [1661B - Getting Zero](https://codeforces.com/problemset/problem/1661/B)

## Problem

### Description

Suppose you have an integer `v`. In one operation, you can:

* either set `v = (v+1) mod 32768`
* or set `v = (2 * v) mod 32768`

You are given `n` integers `a1,a2,…,an`. What is the minimum number of
operations you need to make each `ai` equal to `0`?

### Input

The first line contains the single integer `n` `(1 ≤ n ≤ 32768)` — the number of
integers.

The second line contains `n` integers `a1,a2,…,an`, where `(0 ≤ ai < 32768)`.

### Output

Print `n` integers. The `i`-th integer should be equal to the minimum number of
operations required to make `ai` equal to `0`.

### Examples

Input:

```text
4
19 32764 10240 49
```

Output:

```text
14 4 4 15 
```

## Solutions

Note that `32768 = 2^15`, so you can make any value equal to `0` by multiplying
it by two 15 times, since `(v * 2^15) mod 2^15 = 0`. So, the answer for each
value `ai` is at most 15.

### BFS / Shortest path on a graph

```rust
use std::collections::VecDeque;
use std::error::Error;
use std::io::{BufRead, Write};

const M: u16 = 32768;

fn main() -> Result<(), Box<dyn Error + Send + Sync>> {
    let stdin = std::io::stdin();
    let stdout = std::io::stdout();

    let mut input = stdin.lock();
    let mut output = stdout.lock();
    let mut buffer = String::with_capacity(64);

    input.read_line(&mut buffer).map_err(|e| {
        std::io::Error::new(
            std::io::ErrorKind::Other,
            format!("failed to read from stdin: {:?}", e),
        )
    })?;

    let _test_cases = buffer.trim().parse::<usize>().map_err(|e| {
        std::io::Error::new(
            std::io::ErrorKind::InvalidInput,
            format!(
                "Cannot parse the number of test cases - '{:?}': {:?}",
                e, buffer
            ),
        )
    })?;

    buffer.clear();
    input.read_line(&mut buffer).map_err(|e| {
        std::io::Error::new(
            std::io::ErrorKind::Other,
            format!("failed to read from stdin: {:?}", e),
        )
    })?;

    buffer
        .split(' ')
        .map(|x| x.trim())
        .filter(|x| !x.is_empty())
        .enumerate()
        .try_for_each::<_, Result<(), Box<dyn Error + Send + Sync>>>(|(idx, test_case)| {
            let tc = test_case.parse().map_err(|e| {
                std::io::Error::new(
                    std::io::ErrorKind::InvalidInput,
                    format!("Cannot parse test-case - '{:?}': {:?}", e, test_case),
                )
            })?;

            if idx > 0 {
                write!(output, " ")?;
            }

            write!(output, "{}", solve(tc))?;

            Ok(())
        })?;

    writeln!(output)?;
    output.flush()?;
    Ok(())
}

fn solve(n: u16) -> u16 {
    // handle corner-case here - it's better to have that IF here, instead of in the hot loop
    if n == 0 {
        return 0;
    }

    // Do not visit visited elements twice, for instance `4` can be visited
    // from `2 * 2`, but also from `1 + 1 + 1 + 1`. Each visit results in 2 
    // new nodes in the queue, so it's best to skip as many visits as possible
    let mut visited = vec![false; M as usize];
    visited[n as usize] = true;

    let mut queue = VecDeque::new();
    queue.push_back(n);

    let mut ops = 0;
    while !queue.is_empty() {
        let mut remaining = queue.len();
        while let Some(x) = queue.pop_front() {
            let a = (x + 1) % M;
            if a == 0 {
                return ops + 1;
            }

            if !visited[a as usize] {
                visited[a as usize] = true;
                queue.push_back(a);
            }

            let b = (x * 2) % M;
            if b == 0 {
                return ops + 1;
            }

            if !visited[b as usize] {
                visited[b as usize] = true;
                queue.push_back(b);
            }
            remaining -= 1;
            if remaining == 0 {
                break;
            }
        }

        ops += 1;
    }

    ops
}
```

### The official solution

It's much faster than the BFS

```rust
use std::error::Error;
use std::io::{BufRead, Write};

const M: u32 = 32768;

fn main() -> Result<(), Box<dyn Error + Send + Sync>> {
    let stdin = std::io::stdin();
    let stdout = std::io::stdout();

    let mut input = stdin.lock();
    let mut output = stdout.lock();
    let mut buffer = String::with_capacity(64);

    input.read_line(&mut buffer).map_err(|e| {
        std::io::Error::new(
            std::io::ErrorKind::Other,
            format!("failed to read from stdin: {:?}", e),
        )
    })?;

    let _test_cases = buffer.trim().parse::<usize>().map_err(|e| {
        std::io::Error::new(
            std::io::ErrorKind::InvalidInput,
            format!(
                "Cannot parse the number of test cases - '{:?}': {:?}",
                e, buffer
            ),
        )
    })?;

    buffer.clear();
    input.read_line(&mut buffer).map_err(|e| {
        std::io::Error::new(
            std::io::ErrorKind::Other,
            format!("failed to read from stdin: {:?}", e),
        )
    })?;

    buffer
        .split(' ')
        .map(|x| x.trim())
        .filter(|x| !x.is_empty())
        .enumerate()
        .try_for_each::<_, Result<(), Box<dyn Error + Send + Sync>>>(|(idx, test_case)| {
            let tc = test_case.parse().map_err(|e| {
                std::io::Error::new(
                    std::io::ErrorKind::InvalidInput,
                    format!("Cannot parse test-case - '{:?}': {:?}", e, test_case),
                )
            })?;

            if idx > 0 {
                write!(output, " ")?;
            }

            write!(output, "{}", solve(tc))?;

            Ok(())
        })?;

    writeln!(output)?;
    output.flush()?;
    Ok(())
}

fn solve(n: u32) -> u32 {
    if n == 0 {
        return 0;
    }

    // Note that `32768 = 2^15`, so you can make any value equal to `0` by multiplying
    // it by two 15 times, since `(v * 2^15) mod 2^15 = 0`. So, the answer for each
    // value `ai` is at most 15.
    let mut answer = 15;

    for add in 0..=15 {
        for mul in 0..=15 - add {
            if ((n + add) * 2u32.pow(mul)) % M == 0 {
                answer = answer.min(add + mul);
            }
        }
    }

    answer
}
```