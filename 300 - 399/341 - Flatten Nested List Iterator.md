# [341. Flatten Nested List Iterator](https://leetcode.com/problems/flatten-nested-list-iterator/)

## Problem

You are given a nested list of integers `nestedList`. Each element is either an
integer or a list whose elements may also be integers or other lists. Implement
an iterator to flatten it.

#### Examples

```text
Input: nestedList = [[1,1],2,[1,1]]
Output: [1,1,2,1,1]
Explanation: By calling next repeatedly until hasNext returns false, the order of elements returned by next should be: [1,1,2,1,1].
```

```text
Input: nestedList = [1,[4,[6]]]
Output: [1,4,6]
Explanation: By calling next repeatedly until hasNext returns false, the order of elements returned by next should be: [1,4,6].
```

## Solution

```rust
#[derive(Debug)]
struct NestedIterator {
    remaining: Vec<NestedInteger>,
}


impl NestedIterator {
    fn new(mut list: Vec<NestedInteger>) -> Self {
        list.reverse();
        NestedIterator { remaining: list }
    }

    fn next(&mut self) -> i32 {
        while let Some(element) = self.remaining.pop() {
            match element {
                NestedInteger::Int(x) => {
                    return x;
                }

                NestedInteger::List(mut l) => {
                    while let Some(x) = l.pop() {
                        self.remaining.push(x);
                    }
                }
            }
        }

        panic!()
    }

    fn has_next(&mut self) -> bool {
        while let Some(element) = self.remaining.pop() {
            match element {
                NestedInteger::Int(x) => {
                    self.remaining.push(NestedInteger::Int(x));
                    return true;
                }

                NestedInteger::List(mut l) => {
                    while let Some(x) = l.pop() {
                        self.remaining.push(x);
                    }
                }
            }
        }

        false
    }
}
```