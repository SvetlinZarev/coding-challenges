# [138. Copy List with Random Pointer](https://leetcode.com/problems/copy-list-with-random-pointer/)

## Problem

### Description

A linked list of length `n` is given such that each node contains an
additional `random` pointer, which could point to any node in the list,
or `null`.

Construct a deep copy of the list. The deep copy should consist of exactly `n`
brand new nodes, where each new node has its value set to the value of its
corresponding original node. Both the next and random pointer of the new nodes
should point to new nodes in the copied list such that the pointers in the
original list and copied list represent the same list state. None of the
pointers in the new list should point to nodes in the original list.

For example, if there are two nodes `X` and `Y` in the original list,
where `X.random --> Y`, then for the corresponding two nodes `x` and `y` in the
copied list, `x.random --> y`.

Return the `head` of the copied linked list.

The linked list is represented in the input/output as a list of n nodes. Each
node is represented as a pair of `[val, random_index]` where:

* `val`: an integer representing `Node.val`
* `random_index`: the index of the node (range from `0` to `n-1`) that the
  random pointer points to, or `null` if it does not point to any node.

Your code will only be given the head of the original linked list.

### Constraints

* `0 <= n <= 1000`
* `-10^4 <= Node.val <= 10^4`
* `Node.random` is `null` or is pointing to some node in the linked list.

### Examples

```text
Input: head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
Output: [[7,null],[13,0],[11,4],[10,2],[1,0]]
```

```text
Input: head = [[1,1],[2,1]]
Output: [[1,1],[2,1]]
```

```text
Input: head = [[3,null],[3,0],[3,null]]
Output: [[3,null],[3,0],[3,null]]
```

## Solutions

### With O(n) additional memory

```java
import java.util.IdentityHashMap;

// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}

class Solution {
    public Node copyRandomList(Node head) {
        if (null == head) {
            return null;
        }

        var map = new IdentityHashMap<Node, Node>();

        // Step 1: Copy all the nodes
        var tail = head;
        while (null != tail) {
            map.put(tail, new Node(tail.val));
            tail = tail.next;
        }

        // Step 2: Set the next/random pointers
        tail = head;
        while (null != tail) {
            var node = map.get(tail);
            node.next = map.get(tail.next);
            node.random = map.get(tail.random);
            tail = tail.next;
        }

        return map.get(head);
    }
}
```

### Without additional memory

The main idea is to interleave the original and the cloned
lists `N1 -> C1 -> N2 -> C2..-> Nn -> Cn`, then fix the `random` pointer in the
cloned nodes and then split the two lists:

```java

// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}

class Solution {
    public Node copyRandomList(Node head) {
        if (null == head) {
            return null;
        }

        // Step 1: Clone the nodes and interleave them with the original lists: `original -> clone -> original -> ...`
        var tail = head;
        while (null != tail) {
            var cloned = new Node(tail.val);
            cloned.next = tail.next;
            tail.next = cloned;
            tail = cloned.next;
        }

        // Step 2: Fix the "random" pointer in the clone
        tail = head;
        while (null != tail) {
            if (null != tail.random) {
                // tail is the original node
                // tail.next is the cloned node
                // tail.next.random is the pointer in the cloned node
                // tail.random.next is the cloned equivalent of "random"
                tail.next.random = tail.random.next;
            }

            tail = tail.next.next;
        }

        // Step 3: Split the two lists
        var cloned = head.next;
        var a = head;
        var b = cloned;

        while (null != a) {
            // There is always a next node, because it's the clone of the current one
            a.next = a.next.next;
            a = a.next;

            // because B is the clone,there might not be a next node, so check for NULL
            if (null != b.next) {
                b.next = b.next.next;
                b = b.next;
            }
        }

        return cloned;
    }
}
```