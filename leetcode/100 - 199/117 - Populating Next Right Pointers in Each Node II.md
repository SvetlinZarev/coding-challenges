# [117. Populating Next Right Pointers in Each Node II](https://leetcode.com/problems/populating-next-right-pointers-in-each-node-ii/)

## Problem

### Description

Given a binary tree:

```c 
struct Node {
    int val;
    Node *left;
    Node *right;
    Node *next;
}
```

Populate each next pointer to point to its next right node. If there is
no `next` right node, the next pointer should be set to `NULL`.

Initially, all `next` pointers are set to `NULL`.

#### Follow-up

* You may only use constant extra space.
* The recursive approach is fine. You may assume implicit stack space does not
  count as extra space for this problem.

### Constraints

* The number of nodes in the tree is in the range `[0, 6000]`.
* `-100 <= Node.val <= 100`

### Examples

![tree diagram](resources/117/ex1.png)

```text
Input: root = [1,2,3,4,5,null,7]
Output: [1,#,2,3,#,4,5,7,#]
```

> Explanation: Given the above binary tree (Figure A), your function should
> populate each next pointer to point to its next right node. The serialized
> output is in level order as connected by the next pointers, with '#'
> signifying the end of each level.

## Solutions

### The solution from #116

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

### Another implementation

```go
func connect(root *Node) *Node {
    if nil == root {
		return nil
	}
	
	a := make([]*Node, 0)
	b := make([]*Node, 0)
	a = append(a, root)

	for len(a) > 0 {
		var last *Node = nil

		for _, v := range a {
			if v.Left != nil {
				b = append(b, v.Left)
				if last != nil {
					last.Next = v.Left
				}
				last = v.Left
			}

			if v.Right != nil {
				b = append(b, v.Right)
				if last != nil {
					last.Next = v.Right
				}
				last = v.Right
			}
		}

		a = a[0:0]
		a, b = b, a

	}

	return root
}
```

## Related problems

* [116. Populating Next Right Pointers in Each Node](116%20-%20Populating%20Next%20Right%20Pointers%20in%20Each%20Node.md)