# [150. Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/)

## Problem

Evaluate the value of an arithmetic expression in Reverse Polish Notation.

Valid operators are `+,` `-,` *`,` and `/`. Each operand may be an integer or
another expression.

**Note** that division between two integers should truncate toward zero.

It is guaranteed that the given RPN expression is always valid. That means the
expression would always evaluate to a result, and there will not be any division
by zero operation.

#### Constraints

* `1 <= tokens.length <= 10^4`
* `tokens[i]` is either an operator: `+`, `-`, `*`, or `/`, or an integer in the
  range `[-200, 200]`.

#### Examples

```text
Input: tokens = ["2","1","+","3","*"]
Output: 9
Explanation: ((2 + 1) * 3) = 9
```

```text
Input: tokens = ["4","13","5","/","+"]
Output: 6
Explanation: (4 + (13 / 5)) = 6
```

```text
Input: tokens = ["10","6","9","3","+","-11","*","/","*","17","+","5","+"]
Output: 22
Explanation: ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
= ((10 * (6 / (12 * -11))) + 17) + 5
= ((10 * (6 / -132)) + 17) + 5
= ((10 * 0) + 17) + 5
= (0 + 17) + 5
= 17 + 5
= 22
```

## Solution

```rust
pub fn eval_rpn(tokens: Vec<String>) -> i32 {
    let mut stack = vec![];

    for token in tokens.into_iter() {
        match token.as_str() {
            "+" => {
                let b = stack.pop().unwrap();
                let a = stack.pop().unwrap();
                stack.push(a + b);
            }

            "-" => {
                let b = stack.pop().unwrap();
                let a = stack.pop().unwrap();
                stack.push(a - b);
            }

            "*" => {
                let a = stack.pop().unwrap();
                let b = stack.pop().unwrap();
                stack.push(a * b);
            }

            "/" => {
                let b = stack.pop().unwrap();
                let a = stack.pop().unwrap();
                stack.push(a / b);
            }

            num => stack.push(num.parse::<i32>().unwrap()),
        }
    }

    stack.pop().unwrap()
}
```