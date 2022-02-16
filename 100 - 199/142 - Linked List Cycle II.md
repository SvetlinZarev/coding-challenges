# [142. Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)

## Problem

Given the `head` of a linked list, return the node where the cycle begins. If
there is no cycle, return `null`.

There is a cycle in a linked list if there is some node in the list that can be
reached again by continuously following the `next` pointer. Internally, `pos` is
used to denote the index of the node that tail's next pointer is connected to (
0-indexed). It is `-1` if there is no cycle. Note that `pos` is not passed as a
parameter.

**Do not modify** the linked list.

#### Constraints

* The number of the nodes in the list is in the range `[0, 10^4]`.
* `-10^5 <= Node.val <= 10^5`
* `pos` is `-1` or a valid index in the linked-list.

#### Examples

![image](resources/142/circularlinkedlist.png)

```text
Input: head = [3,2,0,-4], pos = 1
Output: tail connects to node index 1
Explanation: There is a cycle in the linked list, where tail connects to the second node.
```

![image](resources/142/circularlinkedlist_test2.png)

```text
Input: head = [1,2], pos = 0
Output: tail connects to node index 0
Explanation: There is a cycle in the linked list, where tail connects to the first node.
```

![image](resources/142/circularlinkedlist_test3.png)

```text
Input: head = [1], pos = -1
Output: no cycle
Explanation: There is no cycle in the linked list.
```

## Solutions

### With additional memory

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */


class Solution {
    public ListNode detectCycle(ListNode head) {
        if (null == head) {
            return null;
        }

        final var visited = new HashSet<ListNode>();
        visited.add(head);

        var node = head;
        while (node.next != null) {
            if (!visited.add(node.next)) {
                return node.next;
            }

            node = node.next;
        }

        return null;
    }
}
```

### Without additional memory

[From the leetcode discussion forums:](https://leetcode.com/problems/linked-list-cycle-ii/discuss/1701128/C%2B%2BJavaPython-Slow-and-Fast-oror-Image-Explanation-oror-Beginner-Friendly)

* `slow` moves 1 step at a time, `fast` moves 2 steps at a time.
* therefore, `slow` and `fast` can meet only if there is a cycle, then
    * `x` is the length of the list before starting the circle
    * `y` is the distance from the start of the cycle to where `slow` and `fast`
      met
    * `C` is the length of the cycle
    * when they meet, `slow` traveled `x + y` steps while fast
      traveled `2 * (x + y)` steps, thus the extra distance `x + y` must be a
      multiple of the circle length `C`
        * `head` moves `x` steps and arrives at the start of the cycle.
    * so we have `x + y = N * C`, let `slow` continue to travel from `y` and
      after `x` more steps, `slow` will return to the start of the cycle.
    * At the same time, according to the definition of `x`, `head` will also
      reach the start of the cycle after moving `x` steps.
    * so if `head` and `slow` start to move at the same time, they will meet at
      the start of the cycle, that is the answer.

```java
/**
 * Definition for singly-linked list.
 * class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) {
 *         val = x;
 *         next = null;
 *     }
 * }
 */

class Solution {
    public ListNode detectCycle(ListNode head) {
        var fast = head;
        var slow = head;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;

            // Move "fast" by 2 nodes and "slow" by 1 node. If they meet, 
            // then there is a cycle
            if (fast == slow) {
                // If there is a cycle, reset "slow" back to "head"
                // and move both "fast" and "slow" by one node.
                // The node they meet is the target node
                slow = head;
                while (fast != slow) {
                    fast = fast.next;
                    slow = slow.next;
                }
                return slow;
            }
        }

        return null;
    }
}
```

## Related Problems

* [141. Linked List Cycle](/100%20-%20199/141%20-%20Linked%20List%20Cycle.md)