# [160. Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)

## Problem

### Description

Given the heads of two singly linked-lists `headA` and `headB`, return the node
at which the two lists intersect. If the two linked lists have no intersection
at all, return `null`.

For example, the following two linked lists begin to intersect at node `c1`:

![image](resources/160/statement.png)

The test cases are generated such that there are no cycles anywhere in the
entire linked structure.

**Note** that the linked lists must retain their original structure after the
function returns.

#### Custom Judge

The inputs to the judge are given as follows (your program is not given these
inputs):

* `intersectVal` - The value of the node where the intersection occurs. This
  is `0` if there is no intersected node.
* `listA` - The first linked list.
* `listB` - The second linked list.
* `skipA` - The number of nodes to skip ahead in `listA` (starting from the
  head) to get to the intersected node.
* `skipB` - The number of nodes to skip ahead in `listB` (starting from the
  head) to get to the intersected node.

The judge will then create the linked structure based on these inputs and pass
the two heads, `headA` and `headB` to your program. If you correctly return the
intersected node, then your solution will be accepted.

### Constraints

* The number of nodes of `listA` is in the `m`.
* The number of nodes of `listB` is in the `n`.
* `1 <= m, n <= 3 * 10^4`
* `1 <= Node.val <= 10^5`
* `0 <= skipA < m`
* `0 <= skipB < n`
* `intersectVal` is `0` if `listA` and `listB` do not intersect.
* `intersectVal == listA[skipA] == listB[skipB]` if `listA` and `listB`
  intersect.

### Examples

#### Example 1

![image](resources/160/ex1.png)

```text
Input: intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
Output: Intersected at '8'
```

> Explanation: The intersected node's value is 8 (note that this must not be 0
> if the two lists intersect).
> From the head of A, it reads as [4,1,8,4,5]. From the head of B, it reads
> as [5,6,1,8,4,5]. There are 2 nodes before the intersected node in A; There
> are
> 3 nodes before the intersected node in B.

#### Example 2

![image](resources/160/ex2.png)

```text
Input: intersectVal = 2, listA = [1,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
Output: Intersected at '2'
```

> Explanation: The intersected node's value is 2 (note that this must not be 0
> if the two lists intersect).
> From the head of A, it reads as [1,9,1,2,4]. From the head of B, it reads
> as [3,2,4]. There are 3 nodes before the intersected node in A; There are 1
> node
> before the intersected node in B.

#### Example 3

![image](resources/160/ex3.png)

```text
Input: intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
Output: No intersection
```

> Explanation: From the head of A, it reads as [2,6,4]. From the head of B, it
> reads as [1,5]. Since the two lists do not intersect, intersectVal must be 0,
> while skipA and skipB can be arbitrary values.
> Explanation: The two lists do not intersect, so return null.

## Solutions

#### Structure definitions from leetcode

```java
class ListNode {
    int val;
    ListNode next;

    ListNode(int x) {
        val = x;
        next = null;
    }
}
```

### Exploiting the difference in length

Because the common list segment has the same length for both lists, we can
compare them node by node, starting from the head node of each list until we
reach a common node. But in order to do that we must first make the lists have
the same length - i.e. skip some nodes from the longer list, as they cannot
possibly be in the common segment.

```java

class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        int lenA = length(headA);
        int lenB = length(headB);

        int skipA = lenA > lenB ? lenA - lenB : 0;
        int skipB = lenB > lenA ? lenB - lenA : 0;

        var ha = headA;
        for (int i = 0; i < skipA; i++) {
            ha = ha.next;
        }

        var hb = headB;
        for (int i = 0; i < skipB; i++) {
            hb = hb.next;
        }

        while (ha != null && hb != null) {
            if (ha == hb) {
                return ha;
            }

            ha = ha.next;
            hb = hb.next;
        }

        return null;
    }

    static int length(ListNode head) {
        int len = 0;
        var node = head;
        while (node != null) {
            node = node.next;
            len += 1;
        }

        return len;
    }
}
```
