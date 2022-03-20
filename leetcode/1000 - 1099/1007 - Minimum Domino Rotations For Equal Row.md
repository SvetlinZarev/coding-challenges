# [1007. Minimum Domino Rotations For Equal Row](https://leetcode.com/problems/minimum-domino-rotations-for-equal-row/)

## Problem

### Description

In a row of dominoes, `tops[i]` and `bottoms[i]` represent the top and bottom
halves of the `i`th domino. (A domino is a tile with two numbers from `1` to `6`

- one on each half of the tile.)

We may rotate the `i`th domino, so that `tops[i]` and `bottoms[i]` swap values.

Return the minimum number of rotations so that all the values in tops are the
same, or all the values in bottoms are the same.

If it cannot be done, return `-1`.

### Constraints

* `2 <= tops.length <= 2 * 10^4`
* `bottoms.length == tops.length`
* `1 <= tops[i], bottoms[i] <= 6`

### Examples

#### Example 1

![image](resources/1007/domino.png)

```text
Input: tops = [2,1,2,4,2,2], bottoms = [5,2,6,2,3,2]
Output: 2
Explanation: 
The first figure represents the dominoes as given by tops and bottoms: before we do any rotations.
If we rotate the second and fourth dominoes, we can make every value in the top row equal to 2, as indicated by the second figure.
```

#### Example 2

```text
Input: tops = [3,5,1,2,3], bottoms = [3,6,3,3,4]
Output: -1
Explanation: 
In this case, it is not possible to rotate the dominoes to make one row of values equal.
```

## Solutions

### Using majority element

In order to be able to make a top or bottom row, at least one of the sides must
have at least 50% of the elements

```rust
pub fn min_domino_rotations(tops: Vec<i32>, bottoms: Vec<i32>) -> i32 {
    let t = majority_element(&tops);
    let b = majority_element(&bottoms);

    match (t, b) {
        (None, None) => -1,
        (Some((x, c)), None) => {
            if c == tops.len() {
                return 0;
            }

            min_rotations(&tops, &bottoms, x).unwrap_or(-1)
        }
        (None, Some((x, c))) => {
            if c == tops.len() {
                return 0;
            }

            min_rotations(&bottoms, &tops, x).unwrap_or(-1)
        }

        (Some((x, cx)), Some((y, cy))) => {
            if cx == tops.len() || cy == tops.len() {
                return 0;
            }

            let a = min_rotations(&tops, &bottoms, x);
            let b = min_rotations(&bottoms, &tops, y);

            match (a, b) {
                (Some(ax), Some(bx)) => ax.min(bx),
                (Some(ax), None) => ax,
                (None, Some(bx)) => bx,
                (None, None) => -1,
            }
        }
    }
}

fn min_rotations(a: &[i32], b: &[i32], element: i32) -> Option<i32> {
    assert_eq!(a.len(), b.len());

    let mut answer = -1;
    for (&x, &y) in a.iter().zip(b.iter()) {
        if x != element {
            if y != element {
                answer = -1;
                break;
            }

            answer += 1;
        }
    }

    if answer >= 0 {
        return Some(answer + 1); //because we started from -1, instead of 0
    }

    None
}

fn majority_element(array: &[i32]) -> Option<(i32, usize)> {
    let mut x = 0;
    let mut c = 0;

    for &val in array {
        if c == 0 {
            x = val;
            c += 1;
        } else if x == val {
            c += 1;
        } else {
            c -= 1;
        }
    }

    c = 0;
    for &val in array {
        if val == x {
            c += 1;
        }
    }

    if c >= (array.len() + 1) / 2 {
        return Some((x, c));
    }

    None
}
```

### Try out all possibilities

Because the number of distinct values is very low (from 1 to 6) we can try out
every possible number:

```rust
pub fn min_domino_rotations(tops: Vec<i32>, bottoms: Vec<i32>) -> i32 {
    assert_eq!(tops.len(), bottoms.len());

    'next: for value in 1..7 {
        let mut answer_tops = 0;
        let mut answer_bottoms = 0;

        for (&t, &b) in tops.iter().zip(bottoms.iter()) {
            match (t == value, b == value) {
                (true, true) => { /* nothing to do */ }
                (true, false) => answer_bottoms += 1,
                (false, true) => answer_tops += 1,
                (false, false) => continue 'next, // not possible to reach a solution using "value"
            }
        }

        return answer_bottoms.min(answer_tops);
    }

    -1
}
```