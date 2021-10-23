# [946. Validate Stack Sequences](https://leetcode.com/problems/validate-stack-sequences/)

## Problem

Given two integer arrays `pushed` and `popped` each with distinct values,
return `true` if this could have been the result of a sequence of `push`
and `pop` operations on an initially empty stack, or `false` otherwise.

#### Constraints

* `1 <= pushed.length <= 1000`
* `0 <= pushed[i] <= 1000`
* `popped.length == pushed.length`
* All the elements of `pushed` are unique.
* `popped` is a permutation of `pushed`.

#### Examples

```text
Input: pushed = [1,2,3,4,5], popped = [4,5,3,2,1]
Output: true
Explanation: We might do the following sequence:
push(1), push(2), push(3), push(4),
pop() -> 4,
push(5),
pop() -> 5, pop() -> 3, pop() -> 2, pop() -> 1
```

```text
Input: pushed = [1,2,3,4,5], popped = [4,3,5,1,2]
Output: false
Explanation: 1 cannot be popped before 2.
```

## Solution

```rust
pub fn validate_stack_sequences(pushed: Vec<i32>, mut popped: Vec<i32>) -> bool {
    let mut stack = Vec::with_capacity(pushed.len());

    // reverse it, so we can use `.pop()` instead of '.remove()`, 
    // or having to deal with indexes
    popped.reverse();

    for x in pushed.into_iter() {
        stack.push(x);

        while !(stack.is_empty() || popped.is_empty()) {
            let &top = stack.last().unwrap();
            let &pop = popped.last().unwrap();

            if top != pop {
                break;
            }

            stack.pop();
            popped.pop();
        }
    }

    stack.is_empty()
}
```