# [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)

## Problem

Given head, the head of a linked list, determine if the linked list has a cycle
in it.

There is a cycle in a linked list if there is some node in the list that can be
reached again by continuously following the next pointer. Internally, `pos` is
used to denote the index of the node that tail's next pointer is connected to.
Note that `pos` is not passed as a parameter.

Return `true` if there is a cycle in the linked list. Otherwise, return `false`.

#### Examples

```text
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 1st node (0-indexed).
```

```text
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 0th node.
```

```text
Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.
```

## Solution

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */

func hasCycle(head *ListNode) bool {
    slow := head
    fast := head

    for fast != nil && fast.Next != nil {
        slow = slow.Next
        fast = fast.Next.Next

        if fast == slow {
            return true
        }
    }

    return false
}
```

## Related Problems

* [142. Linked List Cycle II](142%20-%20Linked%20List%20Cycle%20II.md)
* [287. Find the Duplicate Number](/leetcode/200%20-%20299/287%20-%20Find%20the%20Duplicate%20Number.md)