# [421. Maximum XOR of Two Numbers in an Array](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/)

## Problem

Given an integer array `nums`, return the maximum result
of `nums[i] XOR nums[j]`, where `0 <= i <= j < n`.

#### Constraints

* `1 <= nums.length <= 2 * 10^5`
* `0 <= nums[i] <= 2^31 - 1`

#### Examples

```text
Input: nums = [3, 10, 5, 25, 2, 8]
Output: 28
Explanation: The maximum result is 5 XOR 25 = 28.
```

```text
Input: nums = [14, 70, 53, 83, 49, 91, 36, 80, 92, 51, 66, 70]
Output: 127
```

## Solutions

### Naive bruteforce

```rust
pub fn find_maximum_xor(nums: Vec<i32>) -> i32 {
    let mut max = 0;

    for i in 0..nums.len() {
        for j in i + 1..nums.len() {
            max = max.max(nums[i] ^ nums[j]);
        }
    }

    max
}
```

### Using a Trie

```rust
#[derive(Debug, Clone, Default)]
struct Node {
    children: [Option<Box<Node>>; 2],
}

impl Node {
    pub fn insert(&mut self, n: i32) {
        // Build the Trie starting from the MSB
        do_insert(self, n, 31);
    }
}

fn do_insert(node: &mut Node, num: i32, bit: u32) {
    let b = ((num & (1 << bit)) != 0) as usize;
    if node.children[b].is_none() {
        node.children[b] = Some(Box::new(Node::default()));
    }

    if bit > 0 {
        do_insert(node.children[b].as_mut().unwrap(), num, bit - 1);
    }
}

pub fn find_maximum_xor(nums: Vec<i32>) -> i32 {
    let mut trie = Node::default();

    for x in nums.iter().copied() {
        trie.insert(x);
    }

    let mut best = 0;
    for x in nums.iter().copied() {
        let mut node = &trie;
        let mut xor_value = 0;

        for bit in (0..32).rev() {
            let is_set = (x & (1 << bit)) != 0;
            // If the current bit is 1, then we need to look for 0 and vice-versa
            match &node.children[(is_set ^ true) as usize] {
                None => {
                    // There is no node with value `(is_set ^ true) as usize` 
                    // (i.e. the inverted value),which means that there must be 
                    // a node with value `is_set as usize`. But because the bits 
                    // at both numbers are the same, i.e, the XOR == 0, we do 
                    // not update the xor_value variable
                    node = node.children[is_set as usize].as_ref().unwrap();
                }

                Some(child) => {
                    // the XOR of the current bit index is 1, so update it in 
                    // the current result
                    xor_value |= 1 << bit;
                    node = child;
                }
            }
        }

        best = best.max(xor_value);
    }
    best
}
```

## Related Problems

* [1707. Maximum XOR With an Element From Array](/1700%20-%201799/1707%20-%20Maximum%20XOR%20With%20an%20Element%20From%20Array.md)