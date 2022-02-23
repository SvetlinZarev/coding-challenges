# [412. Fizz Buzz](https://leetcode.com/problems/fizz-buzz/)

## Problem

Given an integer `n`, return a string array answer (1-indexed) where:

* `answer[i] == "FizzBuzz"` if `i` is divisible by 3 and 5.
* `answer[i] == "Fizz"` if `i` is divisible by 3.
* `answer[i] == "Buzz"` if `i` is divisible by 5.
* `answer[i] == i` (as a string) if none of the above conditions are `true`.

#### Constraints

* `1 <= n <= 10^4`

#### Examples

```text
Input: n = 3
Output: ["1","2","Fizz"]
```

```text
Input: n = 5
Output: ["1","2","Fizz","4","Buzz"]
```

```text
Input: n = 15
Output: ["1","2","Fizz","4","Buzz","Fizz","7","8","Fizz","Buzz","11","Fizz","13","14","FizzBuzz"]
```

## Solution

```rust
pub fn fizz_buzz(n: i32) -> Vec<String> {
    assert!(n >= 1);

    let mut solution = Vec::with_capacity(n as usize);
    for x in 1..=n {
        let answer = match (x % 3 == 0, x % 5 == 0) {
            (true, true) => "FizzBuzz".to_owned(),
            (true, false) => "Fizz".to_owned(),
            (false, true) => "Buzz".to_owned(),
            (false, false) => format!("{}", x),
        };
        solution.push(answer);
    }
    solution
}
```