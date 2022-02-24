# [1095A - Repeating Cipher](https://codeforces.com/problemset/problem/1095/A)

## Problem

### Description

Polycarp loves ciphers. He has invented his own cipher called repeating.

Repeating cipher is used for strings. To encrypt the
string `s=s1s2…sm` (`1≤m≤10`), Polycarp uses the following algorithm:

* he writes down `s1` once,
* he writes down `s2` twice,
* he writes down `s3` three times,
* ...
* he writes down `sm` `m` times.

For example, if `s="bab"` the process is: `"b" → "baa" → "baabbb"`. So the
encrypted `s="bab"` is `"baabbb"`.

Given string `t`— the result of encryption of some string `s`. Your task is to
decrypt it, i.e. find the string `s`.

### Input

The first line contains integer `n` (`1≤n≤55`) — the length of the encrypted
string. The second line of the input contains `t` — the result of encryption of
some string `s`. It contains only lowercase Latin letters. The length of `t` is
exactly `n`.

It is guaranteed that the answer to the test exists.

### Output

Print such string `s` that after encryption it equals `t`

### Examples

```text
Input:
6
baabbb

Output:
bab
```

```text
Input:
10
ooopppssss

Output:
oops
```

## Solution

```rust
use std::error::Error;
use std::io::{stdout, BufRead, Write};

fn main() -> Result<(), Box<dyn Error>> {
    let stdin = std::io::stdin();
    let mut input = stdin.lock();

    let mut buf = String::with_capacity(64);
    let mut text = String::with_capacity(64);

    // discard the line length
    input.read_line(&mut buf)?;
    buf.clear();

    input.read_line(&mut buf)?;
    let mut iter = buf.trim().chars();

    let mut next_char = 0;
    while let Some(ch) = iter.nth(next_char) {
        text.push(ch);
        next_char += 1;
    }

    writeln!(stdout(), "{}", text)?;

    Ok(())
}
```