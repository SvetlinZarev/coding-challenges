# [1707. Maximum XOR With an Element From Array](https://leetcode.com/problems/maximum-xor-with-an-element-from-array/)

## Problem

You are given an array `nums` consisting of non-negative integers. You are also
given a `queries` array, where `queries[i] = [xi, mi]`.

The answer to the `i`th query is the maximum bitwise XOR value of `xi` and any
element of `nums` that does not exceed `mi`. In other words, the answer
is `max(nums[j] XOR xi)` for all `j` such that` nums[j] <= mi`. If all elements
in `nums` are larger than `mi`, then the answer is `-1`.

Return an integer array answer where `answer.length == queries.length`
and `answer[i]` is the answer to the `i`th query.

#### Constraints

* `1 <= nums.length, queries.length <= 10^5`
* `queries[i].length == 2`
* `0 <= nums[j], xi, mi <= 10^9`

#### Examples

```text
Input: nums = [0,1,2,3,4], queries = [[3,1],[1,3],[5,6]]
Output: [3,3,7]
Explanation:
1) 0 and 1 are the only two integers not greater than 1. 0 XOR 3 = 3 and 1 XOR 3 = 2. The larger of the two is 3.
2) 1 XOR 2 = 3.
3) 5 XOR 2 = 7.
```

```text
Input: nums = [5,2,4,6,6,3], queries = [[12,4],[8,1],[6,3]]
Output: [15,-1,5]
```

## Solution

### Using a Trie

```rust
use std::collections::HashMap;

#[derive(Debug, Clone, Default)]
struct Node {
    children: [Option<Box<Node>>; 2],
}

impl Node {
    pub fn insert(&mut self, n: i32) {
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

pub fn maximize_xor(mut nums: Vec<i32>, mut queries: Vec<Vec<i32>>) -> Vec<i32> {
    // preserve the original indexes of the queries, because we need them to construct the result
    let mut result_indexes = HashMap::new();
    for (idx, query) in queries.iter().enumerate() {
        result_indexes
            .entry((query[0], query[1]))
            .or_insert(vec![])
            .push(idx);
    }

    // sort the nums and the queries, in order to be able to partially build the trie
    // using only the numbers lower than the current query limit
    nums.sort_unstable();
    queries.sort_unstable_by(|a, b| a[1].cmp(&b[1]));

    let mut result = vec![0; queries.len()];
    let mut trie = Node::default();

    // we have inserted all numbers with a lesser index into the trie 
    let mut next_idx = 0;

    // There are duplicate queries, so remember the last one in order to avoid 
    // recomputing the duplicates. We remember the last one, because they are sorted
    let mut last_query = None;

    for query in queries.iter() {
        // check if we have already processed this query and skip it if we did
        if Some(query) == last_query {
            continue;
        }
        last_query = Some(query);

        let val = query[0]; // the number to XOR with 
        let lim = query[1]; // do not xor with numbers greater than this

        // add all numbers less than `val` into the trie
        for idx in next_idx..nums.len() {
            let n = nums[idx];
            if n > lim {
                break;
            }

            trie.insert(n);
            next_idx = idx + 1;
        }

        // If next_idx is 0, then we do not have any values in the trie, 
        // which means that the result is -1. Otherwise, compute the result
        let mut xored = -1;
        if next_idx != 0 {
            let mut node = &trie;
            xored = 0;

            // find the maximum XOR value
            for bit in (0..32).rev() {
                let is_set = (val & (1 << bit)) != 0;
                // If the current bit is 1, then we need to look for 0 and vice-versa
                match &node.children[(is_set ^ true) as usize] {
                    None => {
                        // There is no node with value `(is_set ^ true) as usize` (i.e. the inverted value),
                        // which means that there must be a node with value `is_set as usize`
                        node = node.children[is_set as usize].as_ref().unwrap();
                    }

                    Some(child) => {
                        // the XOR of the current bit index is 1
                        xored |= 1 << bit;
                        node = child;
                    }
                }
            }
        }

        // There might be duplicate queries, so update all positions
        for &idx in result_indexes.get(&(val, lim)).unwrap() {
            result[idx] = xored;
        }
    }

    result
}
```

### A bit faster Trie impl

Uses an array instead of a Hashmap to track the result index

```rust
#[derive(Debug, Clone, Default)]
struct Node {
    children: [Option<Box<Node>>; 2],
}

impl Node {
    pub fn insert(&mut self, n: i32) {
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

pub fn maximize_xor(mut nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i32> {
    let mut queries = queries.into_iter().enumerate().collect::<Vec<_>>();
    queries.sort_unstable_by(|a, b| a.1[1].cmp(&b.1[1]));
    nums.sort_unstable();

    let mut result = vec![0; queries.len()];
    let mut trie = Node::default();

    let mut next_idx = 0;
    for (pos, query) in queries.iter() {
        let val = query[0];
        let lim = query[1];

        for idx in next_idx..nums.len() {
            let n = nums[idx];
            if n > lim {
                break;
            }

            trie.insert(n);
            next_idx = idx + 1;
        }

        let mut xored = -1;
        if next_idx != 0 {
            let mut node = &trie;
            xored = 0;

            for bit in (0..32).rev() {
                let is_set = (val & (1 << bit)) != 0;
                // If the current bit is 1, then we need to look for 0 and vice-versa
                match &node.children[(is_set ^ true) as usize] {
                    None => {
                        // There is no node with value `(is_set ^ true) as usize` (i.e. the inverted value),
                        // which means that there must be a node with value `is_set as usize`
                        node = node.children[is_set as usize].as_ref().unwrap();
                    }

                    Some(child) => {
                        // the XOR of the current bit index is 1
                        xored |= 1 << bit;
                        node = child;
                    }
                }
            }
        }

        result[*pos] = xored;
    }

    result
}
```

## Related Problems

* [421. Maximum XOR of Two Numbers in an Array](/leetcode/400%20-%20499/421%20-%20Maximum%20XOR%20of%20Two%20Numbers%20in%20an%20Array.md)