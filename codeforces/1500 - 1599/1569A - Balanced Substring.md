# [1569A - balanced Substring](https://codeforces.com/contest/1569/problem/A)

## Problem

### Description

You are given a string `s`, consisting of `n` letters, each letter is
either '`a`' or '`b`'. The letters in the string are numbered from `1` to `n`.

`s[l; r]`is a continuous substring of letters from index `l` to `r`of the string
inclusive.

A string is called balanced if the number of letters '`a`' in it is equal to the
number of letters '`b`'. For example, strings "`baba`" and "`aabbab`" are
balanced and strings "`aaab`" and "`b`" are not.

Find any non-empty balanced substring `s[l;r]`of string `s`. Print its `l`
and `r` (`1≤l≤r≤n`). If there is no such substring, then print `−1 −1`.

### Input

The first line contains a single integer `t` (`1≤t≤1000`) — the number of
testcases.

Then the descriptions of `t`testcases follow.

The first line of the testcase contains a single integer `n`(`1≤n≤50`) — the
length of the string.

The second line of the testcase contains a string `s`, consisting of `n`letters,
each letter is either '`a`' or '`b`'.

### Output

For each testcase print two integers. If there exists a non-empty balanced
substring `s[l;r]`, then print `l r` (`1≤l≤r≤n`). Otherwise, print `−1 −1`.

### Examples

```text
Input:
4
1
a
6
abbaba
6
abbaba
9
babbabbaa

Output:
-1 -1
1 6
3 6
2 5
```

## Solution

We need to find **any** balanced substring (i.e. **not** the longest), so it's
enough to find if there is at least one occurrence of the substring `ba` or `ab`
, because they are the shortest possible balanced substrings.

```rust
use std::error::Error;
use std::io::BufRead;
use std::io::Write;

type Failure = dyn Error + Send + Sync;

fn main() -> Result<(), Box<Failure>> {
    let stdin = std::io::stdin();
    let mut input = stdin.lock();

    let stdout = std::io::stdout();
    let mut output = stdout.lock();

    let mut buf = String::with_capacity(64);

    input.read_line(&mut buf)?;
    let tests = buf.trim().parse::<u32>()?;

    for _ in 0..tests {
        buf.clear();

        // discard the string length
        input.read_line(&mut buf)?;
        buf.clear();

        // read and process the test case
        input.read_line(&mut buf)?;
        let line = buf.trim_end().as_bytes();
        let balanced_substring_idx = line
            .windows(2)
            .enumerate()
            .find(|&(_, w)| (w[0] == b'a' && w[1] == b'b') || (w[0] == b'b' && w[1] == b'a'))
            .map(|(idx, _)| idx as i32);

        let (l, r) = match balanced_substring_idx {
            None => (-1, -1),
            Some(idx) => (idx + 1, idx + 2), // +1 +2, because the output should be 1-based
        };

        writeln!(output, "{} {}", l, r)?;
    }

    Ok(())
}
```