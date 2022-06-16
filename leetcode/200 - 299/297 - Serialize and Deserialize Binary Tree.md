# [297. Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)

## Problem

### Description

Serialization is the process of converting a data structure or object into a
sequence of bits so that it can be stored in a file or memory buffer, or
transmitted across a network connection link to be reconstructed later in the
same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no
restriction on how your serialization/deserialization algorithm should work. You
just need to ensure that a binary tree can be serialized to a string and this
string can be deserialized to the original tree structure.

Clarification: The input/output format is the same
as [how LeetCode serializes a binary tree](https://support.leetcode.com/hc/en-us/articles/360011883654-What-does-1-null-2-3-mean-in-binary-tree-representation-)
You do not necessarily need to follow this format, so please be creative and
come up with different approaches yourself.

### Constraints

* The number of nodes in the tree is in the range `[0, 10^4]`.
* `-1000 <= Node.val <= 1000`

### Examples

![image](resources/297/ex1.jpg)

```text
Input: root = [1,2,3,null,null,4,5]
Output: [1,2,3,null,null,4,5]
```

```text
Input: root = []
Output: []
```

## Solutions

### Recursive pre-order traversal

```rust
use std::cell::RefCell;
use std::rc::Rc;

// Note the `as _` syntax - this is because LeetCode
// already imports `std::io::Write` and the names clash, 
// thus we have to use this syntax to resolve the issue
use std::fmt::Write as _;

// API provided by LeetCode
#[derive(Debug, PartialEq, Eq)]
pub struct TreeNode {
    pub val: i32,
    pub left: Option<Rc<RefCell<TreeNode>>>,
    pub right: Option<Rc<RefCell<TreeNode>>>,
}

impl TreeNode {
    #[inline]
    pub fn new(val: i32) -> Self {
        TreeNode {
            val,
            left: None,
            right: None,
        }
    }
}

struct Codec {}

impl Codec {
    fn new() -> Self {
        Self {}
    }

    fn serialize(&self, root: Option<Rc<RefCell<TreeNode>>>) -> String {
        let mut serialized = String::new();
        serialize(root.as_ref(), &mut serialized);
        serialized
    }

    fn deserialize(&self, data: String) -> Option<Rc<RefCell<TreeNode>>> {
        deserialize(&data, &mut 0)
    }
}

// Do a pre-order serialization
fn serialize(mut root: Option<&Rc<RefCell<TreeNode>>>, dst: &mut String) {
    if !dst.is_empty() {
        dst.push(' ');
    }

    match root {
        None => dst.push('_'),
        Some(node) => {
            let node_ref = node.borrow();
            write!(dst, "{}", node_ref.val).expect("cannot fail");

            serialize(node_ref.left.as_ref(), dst);
            serialize(node_ref.right.as_ref(), dst);
        }
    }
}

// Do a pre-order de-serialization
fn deserialize(input: &str, from: &mut usize) -> Option<Rc<RefCell<TreeNode>>> {
    let begin = *from;
    let slice = &input[begin..];

    if let Some(end) = slice.find(' ') {
        // Notes:
        // * Because `slice` starts at `begin`, the value of `end` 
        //   is relative to begin, thus we need to add `begin` to it
        // * Because `end` points to a whitespace, we have to add 1 
        //   to it, so the next position starts frem a valid value
        // * We should take the minimum of the input length and the 
        //   **absolute end**, because it can point after the input 
        //   in the case of the very last value in the string
        *from = input.len().min(begin + end + 1);

        // If there is no child node, then return `None`
        if &slice[..end] == "_" {
            return None;
        }

        let value = slice[..end].parse().unwrap();
        return Some(Rc::new(RefCell::new(TreeNode {
            val: value,
            left: deserialize(input, from),
            right: deserialize(input, from),
        })));
    }

    // We have parsed the whole string, so there are no more nodes to parse
    None
}
```

## Related Problems

* [144. Binary Tree Preorder Traversal](/leetcode/100%20-%20199/144%20-%20Binary%20Tree%20Preorder%20Traversal.md)
