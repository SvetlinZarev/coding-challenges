# [1500A - Going Home](https://codeforces.com/problemset/problem/1500/A)

## Problem

### Description

It was the third month of remote learning, Nastya got sick of staying at
dormitory, so she decided to return to her hometown. In order to make her trip
more entertaining, one of Nastya's friend presented her an integer array `a`.

Several hours after starting her journey home Nastya remembered about the
present. To entertain herself she decided to check, are there four different
indices `x`,`y`,`z`,`w` such that `ax+ay=az+aw`.

Her train has already arrived the destination, but she still hasn't found the
answer. Can you help her unravel the mystery?

### Input

The first line contains the single integer `n` (`4 ≤ n ≤ 200_000`) — the size of
the array.

The second line contains `n` integers `a1,a2,…,an (1≤ ai ≤ 2.5*10^6)`.

### Output

Print `YES` if there are such four indices, and `NO` otherwise.

If such indices exist, print these indices `x`, `y`, `z`
and `w` (`1 ≤ x,y,z,w ≤ n`).

If there are multiple answers, print any of them.

### Constraints

* time limit per test: 2 seconds
* memory limit per test: 256 megabytes

### Examples

### Example 1

Input:

```text
6
2 1 5 2 7 4
```

Output:

```text
YES
2 3 1 6 
```

### Example 2

Input:

```text
5
1 3 1 9 20
```

Output:

```text
NO
```

## Solutions

```rust
use std::collections::HashMap;
use std::error::Error;
use std::io::{stdin, BufRead};

fn main() -> Result<(), Box<dyn Error>> {
    let input = stdin();
    let mut input = input.lock();
    let mut buffer = Vec::with_capacity(32);

    input.read_until(b'\n', &mut buffer)?;
    let elements = {
        let string = unsafe { std::str::from_utf8_unchecked(&buffer) };
        string
            .trim()
            .parse()
            .map_err(|_e| format!("Failed to parse: '{}'", string))
    }?;

    let mut numbers = Vec::with_capacity(elements);
    let mut prev = 0;
    let mut seqs = 0;

    loop {
        buffer.clear();
        if 0 == input.read_until(b' ', &mut buffer)? {
            break;
        }

        let string = unsafe { std::str::from_utf8_unchecked(&buffer) }.trim();
        if string.is_empty() {
            continue;
        }

        let number = string
            .parse::<u32>()
            .map_err::<Box<dyn Error>, _>(|_e| format!("Failed to parse: '{}'", string).into())?;
        numbers.push(number);

        // Fast path: If there are 4 elements with the same value we can exit early
        // If we do not exit early here, then the "two sum" later will yield a huge 
        // list of pairs with common index, which will significantly slow down the 
        // already slow linear search
        if prev == number {
            seqs += 1;
        } else {
            seqs = 1;
            prev = number;
        }

        if seqs == 4 {
            print_answer(
                numbers.len() - 4,
                numbers.len() - 3,
                numbers.len() - 2,
                numbers.len() - 1,
            );
            return Ok(());
        }
    }

    if elements != numbers.len() {
        return Err(format!("Expected {} elements, but got {}", elements, numbers.len()).into());
    }

    let mut two_sum = HashMap::with_capacity(64.max(elements / 4));
    for i in 0..numbers.len() {
        let x = numbers[i];

        for j in i + 1..numbers.len() {
            let y = numbers[j];

            let pairs = two_sum.entry(x + y).or_insert(vec![]);
            for &(a, b) in pairs.iter() {
                if a != i && a != j && b != i && b != j {
                    print_answer(a, b, i, j);
                    return Ok(());
                }
            }

            pairs.push((i, j));
        }
    }

    println!("NO");
    Ok(())
}

fn print_answer(a: usize, b: usize, c: usize, d: usize) {
    // +1, because the output indexes are 1 based
    println!("YES\n{} {} {} {}", a + 1, b + 1, c + 1, d + 1);
}
```