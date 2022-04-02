# [707. Design Linked List](https://leetcode.com/problems/design-linked-list/)

## Problem

### Description

Design your implementation of the linked list. You can choose to use a singly or
doubly linked list. A node in a singly linked list should have two
attributes: `val` and `next`. `val` is the value of the current node, and `next`
is a pointer/reference to the next node. If you want to use the doubly linked
list, you will need one more attribute `prev` to indicate the previous node in
the linked list. Assume all nodes in the linked list are 0-indexed.

Implement the `MyLinkedList` class:

* `MyLinkedList()` Initializes the `MyLinkedList` object.
* `int get(int index)` Get the value of the `index`th node in the linked list.
  If the index is invalid, return `-1`.
* `void addAtHead(int val)` Add a node of value val before the first element of
  the linked list. After the insertion, the new node will be the first node of
  the linked list.
* `void addAtTail(int val)` Append a node of value val as the last element of
  the linked list.
* `void addAtIndex(int index, int val)` Add a node of value val before
  the `index`th node in the linked list. If index equals the length of the
  linked list, the node will be appended to the end of the linked list. If index
  is greater than the length, the node will not be inserted.
* `void deleteAtIndex(int index)` Delete the `index`th node in the linked list,
  if the index is valid.

### Constraints

* `0 <= index, val <= 1000`
* Please do not use the built-in `LinkedList` library.
* At most 2000 calls will be made to get, `addAtHead`, `addAtTail`, `addAtIndex`
  and `deleteAtIndex`.

### Examples

```text
Input
["MyLinkedList", "addAtHead", "addAtTail", "addAtIndex", "get", "deleteAtIndex", "get"]
[[], [1], [3], [1, 2], [1], [1], [1]]
Output
[null, null, null, null, 2, null, 3]

Explanation
MyLinkedList myLinkedList = new MyLinkedList();
myLinkedList.addAtHead(1);
myLinkedList.addAtTail(3);
myLinkedList.addAtIndex(1, 2);    // linked list becomes 1->2->3
myLinkedList.get(1);              // return 2
myLinkedList.deleteAtIndex(1);    // now the linked list is 1->3
myLinkedList.get(1);              // return 3
```

## Solutions

### Singly-linked list

```rust
use std::cell::RefCell;
use std::rc::Rc;

type Link = Rc<RefCell<Node>>;

#[derive(Debug)]
struct Node {
    next: Option<Link>,
    value: i32,
}

#[derive(Default, Debug)]
struct MyLinkedList {
    head: Option<Link>,
    tail: Option<Link>,
    length: usize,
}

impl MyLinkedList {
    fn new() -> Self {
        Default::default()
    }

    fn get(&self, index: i32) -> i32 {
        if index as usize >= self.length {
            return -1;
        }

        let mut node = self.head.clone();
        for _ in 0..index {
            node = node.unwrap().borrow().next.clone();
        }

        node.unwrap().borrow().value
    }

    fn add_at_head(&mut self, val: i32) {
        let node = Rc::new(RefCell::new(Node {
            next: self.head.take(),
            value: val,
        }));

        self.head = Some(node);

        if self.tail.is_none() {
            self.tail = self.head.clone();
        }

        self.length += 1;
    }

    fn add_at_tail(&mut self, val: i32) {
        let node = Rc::new(RefCell::new(Node {
            next: None,
            value: val,
        }));

        match self.tail.take() {
            None => self.head = Some(node.clone()),
            Some(tail) => tail.borrow_mut().next = Some(node.clone()),
        }

        self.tail = Some(node);
        self.length += 1;
    }

    fn add_at_index(&mut self, index: i32, val: i32) {
        if index as usize > self.length {
            return;
        }

        if index == 0 {
            self.add_at_head(val);
        } else if index as usize == self.length {
            self.add_at_tail(val)
        } else {
            self.length += 1;

            let mut node = self.head.clone();
            for _ in 0..index - 1 {
                node = node.unwrap().borrow().next.clone();
            }

            let mut n = node.as_mut().unwrap().borrow_mut();
            let mut x = Rc::new(RefCell::new(Node {
                next: n.next.take(),
                value: val,
            }));
            n.next = Some(x);
        }
    }

    fn delete_at_index(&mut self, index: i32) {
        if index as usize >= self.length {
            return;
        }
        self.length -= 1;

        if index == 0 {
            let mut head = self.head.take().unwrap();
            self.head = head.borrow_mut().next.take();

            if self.length == 0 {
                self.tail = None;
            }

            return;
        }

        let mut node = self.head.clone();
        for _ in 0..index - 1 {
            node = node.take().unwrap().borrow().next.clone();
        }

        let mut node_ref = node.as_mut().unwrap().borrow_mut();
        let to_remove = node_ref.next.take().unwrap();
        node_ref.next = to_remove.borrow_mut().next.take();

        if node_ref.next.is_none() {
            drop(node_ref);
            self.tail = node;
        }
    }
}
```