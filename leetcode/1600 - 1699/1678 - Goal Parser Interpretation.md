# [1678. Goal Parser Interpretation](https://leetcode.com/problems/goal-parser-interpretation/)

## Problem

### Description

You own a Goal Parser that can interpret a string `command`. The command
consists of an alphabet of `G`, `()` and/or `(al)` in some order. The Goal
Parser will interpret `G` as the string `G`, `()` as the string `o`, and `(al)`
as the string `al`. The interpreted strings are then concatenated in the
original order.

Given the string command, return the Goal Parser's interpretation of command.

### Constraints

* `1 <= command.length <= 100`
* `command` consists of `G`, `()`, and/or `(al)` in some order.

### Examples

```text
Input: command = "G()(al)"
Output: "Goal"
Explanation: The Goal Parser interprets the command as follows:
G -> G
() -> o
(al) -> al
The final concatenated result is "Goal".
```

```text
Input: command = "G()()()()(al)"
Output: "Gooooal"
```

```text
Input: command = "(al)G(al)()()G"
Output: "alGalooG"
```

## Solutions

```rust
pub fn interpret(command: String) -> String {
    let command = command.as_bytes();

    let mut answer = String::new();
    let mut idx = 0;
    while idx < command.len() {
        match command[idx] {
            b'(' => {
                if command[idx + 1] == b')' {
                    answer.push('o');
                    idx += 1;
                } else if command[idx + 1] == b'a' {
                    answer.push_str("al");
                    idx += 3;
                } else {
                    panic!("invalid input");
                }
            }
            x => answer.push(x as char),
        }

        idx += 1;
    }

    answer
}
```