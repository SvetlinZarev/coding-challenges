# [22. Generate Parentheses](https://leetcode.com/problems/generate-parentheses/)

## Problem

Given `n` pairs of parentheses, write a function to generate all combinations of
well-formed parentheses.

#### Constraints

* `1 <= n <= 8`

#### Examples

```text
Input: n = 3
Output: ["((()))","(()())","(())()","()(())","()()()"]
```

```text
Input: n = 1
Output: ["()"]
```

## Solution

```rust
pub fn generate_parenthesis(n: i32) -> Vec<String> {
    let mut result = vec![];
    if n == 0 {
        return result;
    }

    let mut buffer = String::with_capacity((n * 2) as usize);
    backtrack(&mut result, &mut buffer, n, 0, 0);
    result
}

fn backtrack(
    result: &mut Vec<String>,
    buffer: &mut String,
    brackets: i32,
    opening: i32,
    closing: i32,
) {
    if buffer.len() == (brackets * 2) as usize {
        result.push(buffer.clone());
        return;
    }

    if opening < brackets {
        buffer.push('(');
        backtrack(result, buffer, brackets, opening + 1, closing);
        buffer.pop();
    }

    if closing < opening {
        buffer.push(')');
        backtrack(result, buffer, brackets, opening, closing + 1);
        buffer.pop();
    }
}
```