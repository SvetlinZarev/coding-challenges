# [116. Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)

## Problem

### Description

You are given a perfect binary tree where all leaves are on the same level, and
every parent has two children. The binary tree has the following definition:

```c
struct Node {
    int val;
    Node *left;
    Node *right;
    Node *next;
}
```

Populate each next pointer to point to its next right node. If there is no next
right node, the next pointer should be set to `NULL`.

Initially, all next pointers are set to `NULL`.

### Constraints


* The number of nodes in the tree is in the range `[0, 2^12 - 1]`.
* `-1000 <= Node.val <= 1000`


### Examples

![image](resources/116/ex1.png)
```text
Input: root = [1,2,3,4,5,6,7]
Output: [1,#,2,3,#,4,5,6,7,#]
```

> Explanation: Given the above perfect binary tree (Figure A), your function
> should populate each next pointer to point to its next right node, just like in
> Figure B. The serialized output is in level order as connected by the next
> pointers, with '#' signifying the end of each level.

## Solution

```go
/**
 * Definition for a Node.
 * type Node struct {
 *     Val int
 *     Left *Node
 *     Right *Node
 *     Next *Node
 * }
 */



func connect(root *Node) *Node {
    if nil == root {
        return root
    }

    a := make([]*Node, 0)
    b := make([]*Node, 0)
    a = append(a, root)

    for len(a) > 0 {
        for idx, node := range a {
            if idx+1 < len(a) {
                node.Next = a[idx+1]
            }

            if nil != node.Left {
                b = append(b, node.Left)
            }

            if nil != node.Right {
                b = append(b, node.Right)
            }
        }

        a = a[:0]
        a, b = b, a
    }

    return root
}
```

## Related Problems

* [117. Populating Next Right Pointers in Each Node II](117%20-%20Populating%20Next%20Right%20Pointers%20in%20Each%20Node%20II.md)