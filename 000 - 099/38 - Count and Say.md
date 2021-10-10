# [38. Count and Say](https://leetcode.com/problems/count-and-say/)

## Problem

The count-and-say sequence is a sequence of digit strings defined by the
recursive formula:

* `countAndSay(1)` = "1"
* `countAndSay(n)` is the way you would "say" the digit string
  from `countAndSay(n-1)`, which is then converted into a different digit
  string.

To determine how you "say" a digit string, split it into the minimal number of
groups so that each group is a contiguous section all of the same character.
Then for each group, say the number of characters, then say the character. To
convert the saying into a digit string, replace the counts with a number and
concatenate every saying.

Given a positive integer n, return the nth term of the count-and-say sequence.

#### Constraints

* `1 <= n <= 30`

#### Examples

```text
Input: n = 1
Output: "1"
Explanation: This is the base case.
```

```text
Input: n = 4
Output: "1211"
Explanation:
countAndSay(1) = "1"
countAndSay(2) = say "1" = one 1 = "11"
countAndSay(3) = say "11" = two 1's = "21"
countAndSay(4) = say "21" = one 2 + one 1 = "12" + "11" = "1211"
```

## Solution

```rust
const INT_TO_CH: [char; 10] = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9'];

pub fn count_and_say(n: i32) -> String {
    debug_assert!(n > 0);
    let mut buf_a = String::with_capacity(32);
    let mut buf_b = String::with_capacity(32);

    for i in 1..n + 1 {
        if i == 1 {
            buf_a.push('1');
        } else {
            buf_b.clear();
            process_string(&buf_a, &mut buf_b);
            std::mem::swap(&mut buf_a, &mut buf_b);
        }
    }

    buf_a
}


fn process_string(src: &str, dst: &mut String) {
    let mut count = 0;
    let mut prev = '\0';

    for ch in src.chars() {
        if ch != prev && prev != '\0' {
            write_ls_num(dst, count, prev);

            prev = ch;
            count = 1;
            continue;
        }

        count += 1;
        prev = ch;
    }

    write_ls_num(dst, count, prev);
}

fn write_ls_num(dst: &mut String, count: usize, ch: char) {
    // Formatting an integer/char to a string is much slower,
    // than a str.push() with a looked up value

    if count > 9 {
        std::fmt::Write::write_fmt(dst, format_args!("{}", count)).unwrap();
    } else {
        // For some reason, the look up on my machine
        // is faster than `count + '0'`)
        dst.push(INT_TO_CH[count]);
    }

    dst.push(ch);
}
```