# [1144C - Two Shuffled Sequences](https://codeforces.com/problemset/problem/1144/C)

## Problem

### Description

Two integer sequences existed initially — one of them was strictly increasing,
and the other one — strictly decreasing.

Strictly increasing sequence is a sequence of integers `[x1<x2<⋯<xk]`. And
strictly decreasing sequence is a sequence of integers `[y1>y2>⋯>yl]`. Note that
the empty sequence and the sequence consisting of one element can be considered
as increasing or decreasing.

They were merged into one sequence `a`. After that sequence `a` got shuffled.
For example, some of the possible resulting sequences `a` for an increasing
sequence `[1,3,4]` and a decreasing sequence `[10,4,2]` are
sequences `[1,2,3,4,4,10]` or `[4,2,1,10,4,3]` . This shuffled sequence `a` is
given in the input.

Your task is to find any two suitable initial sequences. One of them should be
strictly increasing and the other one — strictly decreasing. Note that the empty
sequence and the sequence consisting of one element can be considered as
increasing or decreasing.

If there is a contradiction in the input and it is impossible to split the given
sequence `a` to increasing and decreasing sequences, print "`NO`".

### Input

The first line of the input contains one integer `n` (`1≤n≤2*10^5`) — the number
of elements in `a`.

The second line of the input contains `n` integers `a1,a2,…,an` (`0≤ai≤2*10^5`),
where `ai` is the `i`-th element of `a`.

### Output

If there is a contradiction in the input and it is impossible to split the given
sequence `a` to increasing and decreasing sequences, print "`NO`" in the first
line.

Otherwise print "`YES`" in the first line and any two suitable sequences. Note
that the empty sequence and the sequence consisting of one element can be
considered as increasing or decreasing.

In the second line print `ni`— the number of elements in the strictly increasing
sequence. `ni` can be zero, in this case the increasing sequence is empty.

In the third line print `ni` integers `inc1,inc2,…,incni` in the increasing
order of its values (`inc1<inc2<⋯<incni`) — the strictly increasing sequence
itself. You can keep this line empty if `ni=0` (or just print the empty line).

In the fourth line print `nd` — the number of elements in the strictly
decreasing sequence. `nd` can be zero, in this case the decreasing sequence is
empty.

In the fifth line print `nd` integers` dec1,dec2,…,decnd` in the decreasing
order of its values (`dec1>dec2>⋯>decnd`) — the strictly decreasing sequence
itself. You can keep this line empty if `nd=0` (or just print the empty line).

`ni+nd` should be equal to `n` and the union of printed sequences should be a
permutation of the given sequence (in case of "`YES`" answer).


#### Examples
```text
Input:
7
7 2 7 3 3 1 4

Output:
YES
2
3 7 
5
7 4 3 2 1 
```

```text
Input:
5
4 3 1 5 3

Output:
YES
1
3 
4
5 4 3 1 
```

```text
Input:
5
1 1 2 1 2

Output:
NO
```

```text
Input:
5
0 1 2 3 4

Output:
YES
0

5
4 3 2 1 0 
```

## Solution
```rust
use std::error::Error;
use std::io::{BufRead, Write};

fn main() -> Result<(), Box<dyn Error>> {
    let stdin = std::io::stdin();
    let stdout = std::io::stdout();

    let mut input = stdin.lock();
    let mut out = stdout.lock();
    let mut buf = String::with_capacity(64);

    input.read_line(&mut buf)?;
    let elements = buf.trim().parse::<usize>()?;

    buf.clear();
    input.read_line(&mut buf)?;

    let mut data = Vec::with_capacity(elements);
    for s in buf.trim().split_ascii_whitespace() {
        let skill = s.parse::<u32>()?;
        data.push(skill);
    }
    data.sort_unstable();

    let mut a = vec![];
    let mut b = vec![];
    for x in data.into_iter() {
        if a.is_empty() {
            a.push(x);
        } else if b.is_empty() {
            b.push(x);
        } else if a.len() > b.len() {
            if b.last() < Some(&x) {
                b.push(x);
            } else if a.last() < Some(&x) {
                a.push(x);
            } else {
                break;
            }
        } else {
            if a.last() < Some(&x) {
                a.push(x);
            } else if b.last() < Some(&x) {
                b.push(x);
            } else {
                break;
            }
        }
    }

    if a.len() + b.len() < elements {
        writeln!(out, "NO")?;
    } else {
        b.reverse();
        writeln!(out, "YES")?;

        writeln!(out, "{}", a.len())?;
        for idx in 0..a.len() {
            if idx == 0 {
                write!(out, "{}", a[idx])?;
            } else {
                write!(out, " {}", a[idx])?;
            }
        }
        writeln!(out)?;

        writeln!(out, "{}", b.len())?;
        for idx in 0..b.len() {
            if idx == 0 {
                write!(out, "{}", b[idx])?;
            } else {
                write!(out, " {}", b[idx])?;
            }
        }
        writeln!(out)?;
    }
    Ok(())
}
```