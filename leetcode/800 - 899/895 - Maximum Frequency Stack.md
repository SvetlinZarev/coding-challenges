# [895. Maximum Frequency Stack](https://leetcode.com/problems/maximum-frequency-stack/)

## Problem

### Description

Design a stack-like data structure to push elements to the stack and pop the
most frequent element from the stack.

Implement the `FreqStack` class:

* `FreqStack()` constructs an empty frequency stack.
* `void push(int val)` pushes an integer val onto the top of the stack.
* `int pop()` removes and returns the most frequent element in the stack.
* If there is a tie for the most frequent element, the element closest to the
  stack's top is removed and returned.

### Constraints

* `0 <= val <= 10^9`
* At most `2 * 10^4` calls will be made to push and pop.
* It is guaranteed that there will be at least one element in the stack before
  calling pop.

### Examples

```text
Input
["FreqStack", "push", "push", "push", "push", "push", "push", "pop", "pop", "pop", "pop"]
[[], [5], [7], [5], [7], [4], [5], [], [], [], []]
Output
[null, null, null, null, null, null, null, 5, 7, 5, 4]

Explanation
FreqStack freqStack = new FreqStack();
freqStack.push(5); // The stack is [5]
freqStack.push(7); // The stack is [5,7]
freqStack.push(5); // The stack is [5,7,5]
freqStack.push(7); // The stack is [5,7,5,7]
freqStack.push(4); // The stack is [5,7,5,7,4]
freqStack.push(5); // The stack is [5,7,5,7,4,5]
freqStack.pop();   // return 5, as 5 is the most frequent. The stack becomes [5,7,5,7,4].
freqStack.pop();   // return 7, as 5 and 7 is the most frequent, but 7 is closest to the top. The stack becomes [5,7,5,4].
freqStack.pop();   // return 5, as 5 is the most frequent. The stack becomes [5,7,4].
freqStack.pop();   // return 4, as 4, 5 and 7 is the most frequent, but 4 is closest to the top. The stack becomes [5,7].
```

## Solutions

### Using HashMap + TreeMap

* We track the frequency of each element in the hash map `<element, frequency>`
* We track the most frequent element in the tree map: assuming it's ordered from
  smallest to largest, then the last/rightmost leaf will contain the most
  frequent elements
* We track the order of the elements in a vector/stack, which is contained in
  the leaf nodes

```rust
use std::collections::hash_map::Entry;
use std::collections::{BTreeMap, HashMap};

#[derive(Default)]
struct FreqStack {
    freq: HashMap<i32, u32>,
    ordr: BTreeMap<u32, Vec<i32>>,
}

impl FreqStack {
    fn new() -> Self {
        Default::default()
    }

    fn push(&mut self, val: i32) {
        // Find out the frequency of this element
        let count = *self.freq.entry(val).and_modify(|x| *x += 1).or_insert(1);

        // push it to the leaf node responsible for elements with that frequency  
        self.ordr.entry(count).or_default().push(val);
    }

    fn pop(&mut self) -> i32 {
        // find the most frequent element and pop() it from the stack
        let (&freq, values) = self.ordr.iter_mut().last().unwrap();
        let element = values.pop().unwrap();

        // If there are no more elements with that frequency we have to remove 
        // the vec/stack from the tree, so that we can keep the invariant that 
        // `last()` is returning the most frequent elements
        if values.is_empty() {
            self.ordr.remove(&freq);
        }

        // decrement the frequency of that element, optionally we can remove it from the hashmap 
        // if its frequency becomes 0 in order to reduce the memory usage
        match self.freq.entry(element) {
            Entry::Vacant(_) => unreachable!(),
            Entry::Occupied(mut e) => {
                let value = e.get_mut();
                if *value > 1 {
                    *value -= 1;
                } else {
                    e.remove();
                }
            }
        }

        // return thr most frequent element
        element
    }
}
```