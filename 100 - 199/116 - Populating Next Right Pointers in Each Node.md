# [116. Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)

## Problem

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