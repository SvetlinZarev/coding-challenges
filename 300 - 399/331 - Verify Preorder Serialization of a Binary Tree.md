# [331. Verify Preorder Serialization of a Binary Tree](https://leetcode.com/problems/verify-preorder-serialization-of-a-binary-tree/)

## Problem

One way to serialize a binary tree is to use preorder traversal. When we
encounter a non-`null` node, we record the node's value. If it is a `null` node,
we record using a sentinel value such as `#`

```text
      9
    /   \
   3     2
  / \     \
 4   1     6
```

For example, the above binary tree can be serialized to the
string `9,3,4,#,#,1,#,#,2,#,6,#,#`, where `#` represents a null node.

Given a string of comma-separated values preorder, return `true` if it is a
correct preorder traversal serialization of a binary tree.

It is guaranteed that each comma-separated value in the string must be either an
integer or a character `#` representing null pointer.

You may assume that the input format is always valid. For example, it could
never contain two consecutive commas, such as `1,,3`.

**Note:** You are not allowed to reconstruct the tree.

#### Constraints

* `1 <= preorder.length <= 10^4`
* `preorder` consist of integers in the range `[0, 100]`
  and `#` separated by commas `,`.

#### Examples

```text
Input: preorder = "9,3,4,#,#,1,#,#,2,#,6,#,#"
Output: true
```

```text
Input: preorder = "1,#"
Output: false
```

```text
Input: preorder = "9,#,#,1"
Output: false
```

## Solution

```rust
pub fn is_valid_serialization(preorder: String) -> bool {
    let mut nodes = 1isize;
    let mut split = preorder.split(',');

    for x in split {
        match x {
            "#" => nodes -= 1, // leaf node -> subtract from the nodes count
            _ if nodes == 0 => return false, // non-root nodes *must* have a parent
            _ => nodes += 1, // a child node -> increment the node count
        }

        if nodes < 0 {
            return false; // there were too many null leaf nodes
        }
    }

    nodes == 0
}
```