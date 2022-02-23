# [430. Flatten a Multilevel Doubly Linked List](https://leetcode.com/problems/flatten-a-multilevel-doubly-linked-list/)

## Problem

You are given a doubly linked list which in addition to the next and previous pointers, it could have a child pointer,
which may or may not point to a separate doubly linked list. These child lists may have one or more children of their
own, and so on, to produce a multilevel data structure, as shown in the example below.

Flatten the list so that all the nodes appear in a single-level, doubly linked list. You are given the head of the first
level of the list.

#### Constraints

* The number of Nodes will not exceed 1000.
* `1 <= Node.val <= 10^5`

#### Examples

```text
Input: head = [1,2,3,4,5,6,null,null,null,7,8,9,10,null,null,11,12]
```

![multilevel list](resources/430/multilevellinkedlist.png)

```text
Output: [1,2,3,7,8,11,12,9,10,4,5,6]
```

![flattened list](resources/430/multilevellinkedlistflattened.png)

````text
Input: head = [1,2,null,3]
Output: [1,3,2]
Explanation:

The input multilevel linked list is as follows:

  1---2---NULL
  |
  3---NULL
````

```text
Input: head = []
Output: []
```

## Solution

```java

// The list definition from leetcode
class Node {
    public int val;
    public Node prev;
    public Node next;
    public Node child;
};


class Solution {
    public Node flatten(Node head) {
        return flatten(head, null);
    }

    Node flatten(Node head, Node next) {
        if (null == head) {
            return head;
        }

        var node = head;
        while (node != null) {
            if (null != node.child) {
                var subList = flatten(node.child, node.next);
                subList.prev = node;
                node.next = subList;
                node.child = null;
            }

            if (null == node.next && null != next) {
                node.next = next;
                next.prev = node;
                break;
            }

            node = node.next;
        }

        return head;
    }
}
```