# [682. Baseball Game](https://leetcode.com/problems/baseball-game/)

## Problem

### Description

You are keeping score for a baseball game with strange rules. The game consists
of several rounds, where the scores of past rounds may affect future rounds'
scores.

At the beginning of the game, you start with an empty record. You are given a
list of strings `ops`, where `ops[i]` is the `i`th operation you must apply to
the record and is one of the following:

* An integer `x` - Record a new score of `x`.
* `+` - Record a new score that is the sum of the previous two scores. It is
  guaranteed there will always be two previous scores.
* `D` - Record a new score that is double the previous score. It is guaranteed
  there will always be a previous score.
* `C` - Invalidate the previous score, removing it from the record. It is
  guaranteed there will always be a previous score.

Return the sum of all the scores on the record.

### Constraints

* `1 <= ops.length <= 1000`
* `ops[i]` is `C`, `D`, `+`, or a string representing an integer in the
  range `[-3 * 10^4, 3 * 10^4]`.
* For operation `+`, there will always be at least two previous scores on the
  record.
* For operations `C` and `D`, there will always be at least one previous score
  on the record.

### Examples

```text
Input: ops = ["5","2","C","D","+"]
Output: 30
Explanation:
"5" - Add 5 to the record, record is now [5].
"2" - Add 2 to the record, record is now [5, 2].
"C" - Invalidate and remove the previous score, record is now [5].
"D" - Add 2 * 5 = 10 to the record, record is now [5, 10].
"+" - Add 5 + 10 = 15 to the record, record is now [5, 10, 15].
The total sum is 5 + 10 + 15 = 30.
```

```text
Input: ops = ["5","-2","4","C","D","9","+","+"]
Output: 27
Explanation:
"5" - Add 5 to the record, record is now [5].
"-2" - Add -2 to the record, record is now [5, -2].
"4" - Add 4 to the record, record is now [5, -2, 4].
"C" - Invalidate and remove the previous score, record is now [5, -2].
"D" - Add 2 * -2 = -4 to the record, record is now [5, -2, -4].
"9" - Add 9 to the record, record is now [5, -2, -4, 9].
"+" - Add -4 + 9 = 5 to the record, record is now [5, -2, -4, 9, 5].
"+" - Add 9 + 5 = 14 to the record, record is now [5, -2, -4, 9, 5, 14].
The total sum is 5 + -2 + -4 + 9 + 5 + 14 = 27.
```

```text
Input: ops = ["1"]
Output: 1
```

## Solutions

### Simulation

```rust
pub fn cal_points(ops: Vec<String>) -> i32 {
    let mut stack = vec![];

    for op in ops {
        match op.as_str() {
            "+" => {
                let len = stack.len();
                let a = stack[len - 1];
                let b = stack[len - 2];
                stack.push(a + b)
            }
            "D" => {
                let len = stack.len();
                let a = stack[len - 1];
                stack.push(a * 2);
            }
            "C" => {
                stack.pop().unwrap();
            }
            x => stack.push(x.parse::<i32>().unwrap()),
        }
    }

    stack.into_iter().sum()
}
```