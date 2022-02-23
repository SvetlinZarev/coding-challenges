# [17. Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/)

# Problem

Given a string containing digits from 2-9 inclusive, return all possible letter
combinations that the number could represent. Return the answer in any order.

A mapping of digit to letters (just like on the telephone buttons) is given
below. Note that 1 does not map to any letters.

```text
1 => nothing
2 => abc
3 => def
4 => ghi
5 => jkl
6 => mno
7 +> pqrs
8 => tuv
9 => wxyz
```

#### Constraints

* `0 <= digits.length <= 4`
* `digits[i]` is a digit in the range `['2', '9']`

#### Examples

```text
Input: digits = ""
Output: []
```

```text
Input: digits = "2"
Output: ["a","b","c"]
```

```text
Input: digits = "23"
Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

## Solutions

### Recursive solution

```rust
const DIGIT_MAP: [[char; 4]; 10] = [
    ['0', '0', '0', '0'], //0
    ['0', '0', '0', '0'], //1
    ['a', 'b', 'c', '0'], //2
    ['d', 'e', 'f', '0'], //3
    ['g', 'h', 'i', '0'], //4
    ['j', 'k', 'l', '0'], //5
    ['m', 'n', 'o', '0'], //6
    ['p', 'q', 'r', 's'], //7
    ['t', 'u', 'v', '0'], //8
    ['w', 'x', 'y', 'z'], //9
];

pub fn letter_combinations(digits: String) -> Vec<String> {
    let mut result = vec![];
    if digits.is_empty() {
        return result;
    }

    let mut buffer = String::with_capacity(16);
    dfs(&mut result, &mut buffer, digits.as_bytes());
    result
}

fn dfs(result: &mut Vec<String>, buffer: &mut String, digits: &[u8]) {
    if digits.len() == 0 {
        result.push(buffer.clone());
        return;
    }

    let idx = (digits[0] - b'0') as usize;
    for ch in DIGIT_MAP[idx].iter().copied().filter(|ch| *ch != '0') {
        buffer.push(ch);
        dfs(result, buffer, &digits[1..]);
        buffer.pop();
    }
}
```

### Iterative solution

```rust
const DIGIT_MAP: [[char; 4]; 10] = [
    ['0', '0', '0', '0'], //0
    ['0', '0', '0', '0'], //1
    ['a', 'b', 'c', '0'], //2
    ['d', 'e', 'f', '0'], //3
    ['g', 'h', 'i', '0'], //4
    ['j', 'k', 'l', '0'], //5
    ['m', 'n', 'o', '0'], //6
    ['p', 'q', 'r', 's'], //7
    ['t', 'u', 'v', '0'], //8
    ['w', 'x', 'y', 'z'], //9
];

pub fn letter_combinations(digits: String) -> Vec<String> {
    let mut result = vec![];
    if digits.is_empty() {
        return result;
    }

    DIGIT_MAP[(digits.as_bytes()[0] - b'0') as usize]
        .iter()
        .copied()
        .filter(|ch| *ch != '0')
        .for_each(|seed| {
            let mut string = String::with_capacity(digits.len());
            string.push(seed);
            result.push(string);
        });

    let mut queue = vec![];
    while result[0].len() < digits.len() {
        for mut combination in result.drain(..) {
            let next = combination.len();
            let next_idx = (digits.as_bytes()[next] - b'0') as usize;

            DIGIT_MAP[next_idx]
                .iter()
                .copied()
                .rev()
                .take(3)
                .filter(|ch| *ch != '0')
                .for_each(|ch| {
                    let mut next_comb = String::with_capacity(combination.capacity());
                    next_comb.push_str(&combination);
                    next_comb.push(ch);
                    queue.push(next_comb);
                });

            combination.push(DIGIT_MAP[next_idx][0]);
            queue.push(combination);
        }

        std::mem::swap(&mut queue, &mut result);
    }

    result
}
```