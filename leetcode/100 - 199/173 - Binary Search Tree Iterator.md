# [173. Binary Search Tree Iterator](https://leetcode.com/problems/binary-search-tree-iterator/)

## Problem

### Description

Implement the `BSTIterator` class that represents an iterator over the in-order
traversal of a binary search tree (BST):

* `BSTIterator(TreeNode root)` Initializes an object of the `BSTIterator` class.
  The root of the BST is given as part of the constructor. The pointer should be
  initialized to a non-existent number smaller than any element in the BST.
* `boolean hasNext()` Returns `true` if there exists a number in the traversal
  to the right of the pointer, otherwise returns `false`.
* `int next()` Moves the pointer to the right, then returns the number at the
  pointer.

Notice that by initializing the pointer to a non-existent smallest number, the
first call to `next()` will return the smallest element in the BST.

You may assume that `next()` calls will always be valid. That is, there will be
at least a next number in the in-order traversal when `next()` is called.

### Constraints

* The number of nodes in the tree is in the range `[1, 105]`.
* `0 <= Node.val <= 10^6`
* At most `10^5` calls will be made to `hasNext`, and `next`.

### Examples

![image](resources/173/ex1.png)

```text
Input
["BSTIterator", "next", "next", "hasNext", "next", "hasNext", "next", "hasNext", "next", "hasNext"]
[[[7, 3, 15, null, null, 9, 20]], [], [], [], [], [], [], [], [], []]
Output
[null, 3, 7, true, 9, true, 15, true, 20, false]

Explanation
BSTIterator bSTIterator = new BSTIterator([7, 3, 15, null, null, 9, 20]);
bSTIterator.next();    // return 3
bSTIterator.next();    // return 7
bSTIterator.hasNext(); // return True
bSTIterator.next();    // return 9
bSTIterator.hasNext(); // return True
bSTIterator.next();    // return 15
bSTIterator.hasNext(); // return True
bSTIterator.next();    // return 20
bSTIterator.hasNext(); // return False
```

## Solutions

### Using a stack

```rust
use std::cell::RefCell;
use std::rc::Rc;

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


#[derive(Default)]
struct BSTIterator {
    stack: Vec<Rc<RefCell<TreeNode>>>,
}

impl BSTIterator {
    fn new(root: Option<Rc<RefCell<TreeNode>>>) -> Self {
        let mut stack = vec![];

        let mut node = root;
        while let Some(next) = node {
            node = next.borrow().left.clone();
            stack.push(next);
        }

        Self { stack }
    }

    fn next(&mut self) -> i32 {
        let node = self.stack.pop().unwrap();
        let node_ref = node.borrow();
        let value = node_ref.val;

        let mut next = node_ref.right.clone();
        while let Some(n) = next {
            next = n.borrow().left.clone();
            self.stack.push(n);
        }

        value
    }

    fn has_next(&self) -> bool {
        !self.stack.is_empty()
    }
}
```

## Related Problems

* [897. Increasing Order Search Tree](/leetcode/800%20-%20899/897%20-%20Increasing%20Order%20Search%20Tree.md)
