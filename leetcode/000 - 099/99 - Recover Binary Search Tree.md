# [99. Recover Binary Search Tree](https://leetcode.com/problems/recover-binary-search-tree/)

## Problem

### Description

You are given the `root` of a binary search tree (BST), where the values of
**exactly** two nodes of the tree were swapped by mistake. Recover the tree
without changing its structure.

### Constraints

* The number of nodes in the tree is in the range `[2, 1000]`.
* `-2^31 <= Node.val <= 2^31 - 1`

### Examples

#### Example 1

![image](resources/99/ex1.jpg)

```text
Input: root = [1,3,null,null,2]
Output: [3,1,null,null,2]
Explanation: 3 cannot be a left child of 1 because 3 > 1. Swapping 1 and 3 makes the BST valid.
```

#### Example 2

![image](resources/99/ex2.jpg)

```text
Input: root = [3,1,4,null,null,2]
Output: [2,1,4,null,null,3]
Explanation: 2 cannot be in the right subtree of 3 because 2 < 3. Swapping 2 and 3 makes the BST valid.
```

## Solutions

### In-Order traversal with additional memory #1

* Time complexity `O(n log n)`
* Space complexity `O(n)`

The idea is that the in-order traversal of the tree should yield the node values
in increasing order. Thus the following algorithm:

1. Using in-order traversal colect the node values in a vector
2. Sort the vector
3. Using in-order traversal overwrite the original node values

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

pub fn recover_tree(root: &mut Option<Rc<RefCell<TreeNode>>>) {
    let mut data = collect_in_order(root.clone());
    data.sort_unstable();
    replace_in_order(root.clone(), data);
}

fn collect_in_order(root: Option<Rc<RefCell<TreeNode>>>) -> Vec<i32> {
    let mut stack = vec![];
    let mut answer = vec![];

    expand_left(root.clone(), &mut stack);
    while let Some(node) = stack.pop() {
        let node_ref = node.borrow();
        answer.push(node_ref.val);

        expand_left(node_ref.right.clone(), &mut stack);
    }

    answer
}

fn replace_in_order(root: Option<Rc<RefCell<TreeNode>>>, data: Vec<i32>) {
    let mut stack = vec![];
    let mut counter = 0;

    expand_left(root.clone(), &mut stack);
    while let Some(node) = stack.pop() {
        let mut node_ref = node.borrow_mut();
        node_ref.val = data[counter];
        counter += 1;

        expand_left(node_ref.right.clone(), &mut stack);
    }
}

fn expand_left(root: Option<Rc<RefCell<TreeNode>>>, stack: &mut Vec<Rc<RefCell<TreeNode>>>) {
    let mut node = root;
    while let Some(n) = node {
        node = n.borrow().left.clone();
        stack.push(n);
    }
}
```

### In-Order traversal with additional memory #2

* Time complexity `O(n)`
* Space complexity `O(n)`

We can improve the above solution by replacing the `O(n log n)` sorting with
an `O(n)` swap operation.

1. Find the index of the number that is bigger than the next number. This is our
   first number to swap.
2. Continue looping through the array until we
    * reach the end of the array, or
    * we find the last smaller than #1 number
3. Swap those two numbers

Everything is the same except for the sorting:

```rust
pub fn recover_tree(root: &mut Option<Rc<RefCell<TreeNode>>>) {
    let mut data = collect_in_order(root.clone());

    // find the index of the first misplaced number. Because the array representation
    // of a proper BST tree should be sorted, the first misplaced number should be
    // smaller than the previous one
    let mut i = 0;
    while data[i] < data[i + 1] {
        i += 1;
    }

    // Loop through the rest of the array in order to find where the `i`th value belongs
    let mut j = i;
    while j < data.len() - 1 && data[i] > data[j + 1] {
        j += 1;
    }

    // put the misplaced values in their correct positions
    data.swap(i, j);

    replace_in_order(root.clone(), data);
}
```

### Inorder traversal without additional memory

We use the same idea as above, but instead of collecting all values in an array
we do it in-place while iterating over the tree nodes

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

pub fn recover_tree(root: &mut Option<Rc<RefCell<TreeNode>>>) {
    if root.is_none() {
        return;
    }

    let mut stack = vec![];
    expand_left(root.clone(), &mut stack);

    let mut first_to_swap = None;
    let mut previous_node = None;

    while let Some(node) = stack.pop() {
        let node_ref = node.borrow();

        match first_to_swap.as_ref() {
            None => match previous_node.as_ref() {
                None => previous_node = Some(node.clone()),
                Some(prev) => {
                    if node_ref.val < prev.borrow().val {
                        first_to_swap = previous_node.clone();
                    }

                    previous_node = Some(node.clone());
                }
            },
            Some(to_swap) => {
                if to_swap.borrow().val < node_ref.val {
                    break;
                }

                previous_node = Some(node.clone());
            }
        }

        expand_left(node_ref.right.clone(), &mut stack);
    }

    // Swapping it outside the loop lets us handle various corner cases, such as:
    //   0        1
    //  /        /
    // 1        3
    //           \
    //            2
    //--------------------
    let a = first_to_swap.unwrap();
    let b = previous_node.unwrap();
    std::mem::swap(&mut a.borrow_mut().val, &mut b.borrow_mut().val);
}

fn expand_left(root: Option<Rc<RefCell<TreeNode>>>, stack: &mut Vec<Rc<RefCell<TreeNode>>>) {
    let mut node = root;
    while let Some(n) = node {
        node = n.borrow().left.clone();
        stack.push(n);
    }
}
```
