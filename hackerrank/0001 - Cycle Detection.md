# [Cycle Detection](https://www.hackerrank.com/challenges/detect-whether-a-linked-list-contains-a-cycle/problem)

## Problem

### Description

A linked list is said to contain a cycle if any node is visited more than once
while traversing the list. Given a pointer to the head of a linked list,
determine if it contains a cycle. If it does, return 1. Otherwise, return 0.

**Note:** If the list is empty, `head` will be `null`.

### Constraints

* `0 <= list length <= 1000`

### Examples

```text
Input: 1 -> 2 -> 3 -> NULL
Output: 0 (no cycle)
```

```text
Input: 1 -> 2 -> 3 -> 1 ->...
Output: 1 (cycle with node 3 pointing back to node 1)
```

## Solutions

### Slow/Fast pointers

```java
class Solution {

    // Complete the hasCycle function below.

    /*
     * For your reference:
     *
     * SinglyLinkedListNode {
     *     int data;
     *     SinglyLinkedListNode next;
     * }
     *
     */
    static boolean hasCycle(SinglyLinkedListNode head) {
        SinglyLinkedListNode slow = head;
        SinglyLinkedListNode fast = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next;
            fast = fast.next;

            if (fast == slow) {
                return true;
            }
        }

        return false;
    }
}
```