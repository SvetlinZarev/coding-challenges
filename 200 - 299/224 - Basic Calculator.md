# [224. Basic Calculator](https://leetcode.com/problems/basic-calculator/)

## Problem

Given a string `s` representing a valid expression, implement a basic calculator
to evaluate it, and return the result of the evaluation.

Note: You are not allowed to use any built-in function which evaluates strings
as mathematical expressions, such as `eval()`.

#### Constraints

* `1 <= s.length <= 3 * 10^5`
* `s` consists of digits, `+`, `-`, `(`, `)`, and ` `.
* `s` represents a valid expression.
* `+` is not used as a unary operation (i.e., `+1` and `+(2 + 3)` is invalid).
* `-` could be used as a unary operation (i.e., `-1` and `-(2 + 3)` is valid).
* There will be no two consecutive operators in the input.
* Every number and running calculation will fit in a signed 32-bit integer.

#### Examples

```text
Input: s = "1 + 1"
Output: 2
```

```text
Input: s = " 2-1 + 2 "
Output: 3
```

```text
Input: s = "(1+(4+5+2)-3)+(6+8)"
Output: 23
```

## Solution

```rust
use std::cmp::Ordering;

#[derive(Debug, Copy, Clone, Eq, PartialEq)]
enum Op {
    Add,
    Sub,
    Mul,
    Div,
    Opn,
    Clo,
}

impl Ord for Op {
    fn cmp(&self, other: &Self) -> Ordering {
        match (self, other) {
            (Op::Add, Op::Add) => Ordering::Equal,
            (Op::Sub, Op::Sub) => Ordering::Equal,
            (Op::Mul, Op::Mul) => Ordering::Equal,
            (Op::Div, Op::Div) => Ordering::Equal,
            (Op::Opn, Op::Opn) => Ordering::Equal,
            (Op::Clo, Op::Clo) => Ordering::Equal,
            //
            (Op::Add, Op::Sub) => Ordering::Equal,
            (Op::Sub, Op::Add) => Ordering::Equal,
            (Op::Mul, Op::Div) => Ordering::Equal,
            (Op::Div, Op::Mul) => Ordering::Equal,
            (Op::Opn, Op::Clo) => Ordering::Equal,
            (Op::Clo, Op::Opn) => Ordering::Equal,
            //
            (Op::Add, Op::Mul) => Ordering::Less,
            (Op::Add, Op::Div) => Ordering::Less,
            (Op::Add, Op::Opn) => Ordering::Less,
            (Op::Add, Op::Clo) => Ordering::Less,
            (Op::Sub, Op::Mul) => Ordering::Less,
            (Op::Sub, Op::Div) => Ordering::Less,
            (Op::Sub, Op::Opn) => Ordering::Less,
            (Op::Sub, Op::Clo) => Ordering::Less,
            (Op::Mul, Op::Opn) => Ordering::Less,
            (Op::Mul, Op::Clo) => Ordering::Less,
            (Op::Div, Op::Opn) => Ordering::Less,
            (Op::Div, Op::Clo) => Ordering::Less,
            //
            (Op::Mul, Op::Add) => Ordering::Greater,
            (Op::Mul, Op::Sub) => Ordering::Greater,
            (Op::Div, Op::Add) => Ordering::Greater,
            (Op::Div, Op::Sub) => Ordering::Greater,
            (Op::Opn, Op::Add) => Ordering::Greater,
            (Op::Opn, Op::Sub) => Ordering::Greater,
            (Op::Opn, Op::Mul) => Ordering::Greater,
            (Op::Opn, Op::Div) => Ordering::Greater,
            (Op::Clo, Op::Add) => Ordering::Greater,
            (Op::Clo, Op::Sub) => Ordering::Greater,
            (Op::Clo, Op::Mul) => Ordering::Greater,
            (Op::Clo, Op::Div) => Ordering::Greater,
        }
    }
}

impl PartialOrd for Op {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

impl Op {
    fn eval(self, a: i32, b: i32) -> i32 {
        match self {
            Op::Add => a + b,
            Op::Sub => a - b,
            Op::Mul => a * b,
            Op::Div => a / b,
            _ => panic!("Cannot be evaluated: {:?}", self),
        }
    }
}

#[derive(Debug, Copy, Clone, Eq, PartialEq)]
enum Token {
    Val(i32),
    Op(Op),
}

fn tokenize<T: AsRef<str>>(s: T) -> Vec<Token> {
    let mut brackets = vec![];
    let mut tokens = vec![];
    let mut sign = 1;
    let mut number = None;

    for &ch in s.as_ref().as_bytes() {
        match ch {
            b'(' => {
                debug_assert_eq!(number, None);

                if sign == -1 {
                    tokens.push(Token::Val(-1));
                    tokens.push(Token::Op(Op::Mul));
                    sign = 1;
                }

                tokens.push(Token::Op(Op::Opn));
                brackets.push(());
            }

            b')' => {
                let mut push = true;

                if let Some(number) = number.take() {
                    if let Some(&last) = tokens.last() {
                        if last == Token::Op(Op::Opn) {
                            push = false;
                            tokens.pop();
                        }
                    }

                    tokens.push(Token::Val(number * sign));
                    sign = 1;
                }

                if push {
                    tokens.push(Token::Op(Op::Clo));
                }
                brackets.pop().unwrap();
            }

            b'-' => {
                if let Some(number) = number.take() {
                    tokens.push(Token::Val(number * sign));
                    tokens.push(Token::Op(Op::Sub));
                    sign = 1;
                } else if let Some(&Token::Op(Op::Opn)) = tokens.last() {
                    sign = -1;
                } else if tokens.is_empty() {
                    sign = -1;
                } else {
                    tokens.push(Token::Op(Op::Sub));
                }
            }

            b'+' => {
                if let Some(number) = number.take() {
                    tokens.push(Token::Val(number * sign));
                    sign = 1;
                }

                tokens.push(Token::Op(Op::Add));
            }

            b'/' => {
                if let Some(number) = number.take() {
                    tokens.push(Token::Val(number * sign));
                    sign = 1;
                }

                tokens.push(Token::Op(Op::Div));
            }

            b'*' => {
                if let Some(number) = number.take() {
                    tokens.push(Token::Val(number * sign));
                    sign = 1;
                }

                tokens.push(Token::Op(Op::Mul));
            }

            b' ' => {
                if let Some(number) = number.take() {
                    tokens.push(Token::Val(number * sign));
                    sign = 1;
                }
            }

            b'0'..=b'9' => match number {
                None => number = Some((ch - b'0') as i32),
                Some(value) => number = Some(value * 10 + (ch - b'0') as i32),
            },

            _ => {
                panic!("Unexpected character: {}", ch as char);
            }
        }
    }

    assert_eq!(0, brackets.len());
    if let Some(number) = number.take() {
        tokens.push(Token::Val(number * sign));
    }

    tokens
}

// This method is called by LeetCode
pub fn calculate(s: String) -> i32 {
    let tokens = tokenize(s);
    let mut values = vec![];
    let mut operators = vec![];

    for token in tokens.into_iter() {
        match token {
            Token::Val(v) => {
                values.push(v);
            }

            Token::Op(op @ Op::Opn) => {
                operators.push(op);
            }

            Token::Op(Op::Clo) => {
                while let Some(op) = operators.pop() {
                    if op == Op::Opn {
                        break;
                    }

                    eval(&mut values, op);
                }
            }

            Token::Op(op) => {
                if let Some(&last_op) = operators.last() {
                    if last_op != Op::Opn {
                        if last_op >= op {
                            operators.pop();
                            eval(&mut values, last_op);
                        }
                    }
                }

                operators.push(op);
            }
        }
    }

    while let Some(op) = operators.pop() {
        eval(&mut values, op);
    }

    values.pop().unwrap()
}


fn eval(values: &mut Vec<i32>, op: Op) {
    let a = values
        .pop()
        .ok_or_else(|| format!("Current op: {:?};", op))
        .unwrap();
    let b = values
        .pop()
        .ok_or_else(|| format!("Current op: {:?};", op))
        .unwrap();

    let c = op.eval(b, a); // they are popped in reverse order
    values.push(c);
}
```