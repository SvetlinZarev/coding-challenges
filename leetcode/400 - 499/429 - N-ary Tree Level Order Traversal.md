# [429. N-ary Tree Level Order Traversal](https://leetcode.com/problems/n-ary-tree-level-order-traversal/)

## Problem

### Description

Given an `n`-ary tree, return the level order traversal of its nodes' values.

Nary-Tree input serialization is represented in their level order traversal,
each group of children is separated by the `null` value (See examples).

### Constraints

* The height of the `n`-ary tree is less than or equal to 1000
* The total number of nodes is between `[0, 10^4]`

### Examples

#### Example 1

![image](resources/429/ex1.png)

```text
Input: root = [1,null,3,2,4,null,5,6]
Output: [[1],[3,2,4],[5,6]]
```

### Example 2

![image](resources/429/ex2.png)

```text
Input: root = [1,null,2,3,4,5,null,null,6,7,null,8,null,9,10,null,null,11,null,12,null,13,null,null,14]
Output: [[1],[2,3,4,5],[6,7,8,9,10],[11,12,13],[14]]
```

## Solutions

#### Node definition provided by LeetCode

```go
type Node struct {
	Val      int
	Children []*Node
}
```

### BFS

```go
func levelOrder(root *Node) [][]int {
	result := make([][]int, 0)
	current := make([]*Node, 0)
	next := make([]*Node, 0)

	if nil != root {
		current = append(current, root)
	}

	for len(current) > 0 {
		level := make([]int, 0)

		for _, node := range current {
			level = append(level, node.Val)
			next = append(next, node.Children...)
		}

		result = append(result, level)

		temp := current
		current = next
		next = temp[0:0]
	}

	return result
}
```

### DFS

```go
func levelOrder(root *Node) [][]int {
	result := make([][]int, 0)
	dfs(root, &result, 0)
	return result
}

func dfs(root *Node, result *[][]int, depth int) {
	if nil == root {
		return
	}

	if len(*result) < depth+1 {
		*result = append(*result, make([]int, 0))
	}

	(*result)[depth] = append((*result)[depth], root.Val)

	for _, node := range root.Children {
		dfs(node, result, depth+1)
	}
}
```