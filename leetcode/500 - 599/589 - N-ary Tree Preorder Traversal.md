# [589. N-ary Tree Preorder Traversal](https://leetcode.com/problems/n-ary-tree-preorder-traversal/)

## Problem

### Description

Given the root of an `n`-ary tree, return the preorder traversal of its nodes'
values.

Nary-Tree input serialization is represented in their level order traversal.
Each group of children is separated by the `null` value (See examples)

### Constraints

* The number of nodes in the tree is in the range `[0, 10^4]`.
* `0 <= Node.val <= 10^4`
* The height of the n-ary tree is less than or equal to 1000.

### Examples

![inage](resources/589/ex1.png)

```text
Input: root = [1,null,3,2,4,null,5,6]
Output: [1,3,5,6,2,4]
```

![image](resources/589/ex2.png)

```text
Input: root = [1,null,2,3,4,5,null,null,6,7,null,8,null,9,10,null,null,11,null,12,null,13,null,null,14]
Output: [1,2,3,6,7,11,14,4,8,12,5,9,13,10]
```

## Solutions

### Pre-order DFS

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}

    public Node(int _val) {
        val = _val;
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
    public List<Integer> preorder(Node root) {
        var result = new ArrayList<Integer>();
        if (root != null) {
            preorder(result, root);
        }
        return result;
    }

    void preorder(List<Integer> result, Node root) {
        result.add(root.val);
        for (Node node : root.children) {
            preorder(result, node);
        }
    }
}
```

## Related Problems

* [144. Binary Tree Preorder Traversal](/leetcode/100%20-%20199/144%20-%20Binary%20Tree%20Preorder%20Traversal.md)

