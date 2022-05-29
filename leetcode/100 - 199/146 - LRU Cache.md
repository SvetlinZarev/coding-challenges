# [146. LRU Cache](https://leetcode.com/problems/lru-cache/)

## Problem

### Description

Design a data structure that follows the constraints of a Least Recently Used (
LRU) cache.

Implement the `LRUCache` class:

* `LRUCache(int capacity)` Initialize the LRU cache with positive size capacity.
* `int get(int key)` Return the value of the key if the key exists, otherwise
  return `-1`.
* `void put(int key, int value)` Update the value of the key if the key exists.
  Otherwise, add the key-value pair to the cache. If the number of keys exceeds
  the capacity from this operation, evict the least recently used key.

The functions `get` and `put` must each run in `O(1)` average time complexity.

### Constraints

* `1 <= capacity <= 3000`
* `0 <= key <= 10^4`
* `0 <= value <= 10^5`
* At most `2 * 10^5` calls will be made to `get` and `put`.

### Examples

```text
Input
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
Output
[null, null, null, 1, null, -1, null, -1, 3, 4]


Explanation
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // cache is {1=1}
lRUCache.put(2, 2); // cache is {1=1, 2=2}
lRUCache.get(1);    // return 1
lRUCache.put(3, 3); // LRU key was 2, evicts key 2, cache is {1=1, 3=3}
lRUCache.get(2);    // returns -1 (not found)
lRUCache.put(4, 4); // LRU key was 1, evicts key 1, cache is {4=4, 3=3}
lRUCache.get(1);    // return -1 (not found)
lRUCache.get(3);    // return 3
lRUCache.get(4);    // return 4
```

## Solutions

### HashMap + Doubly-Linked List

* The `HashMap` is used to guarantee the `O(1)` requirements for `get()`
  and `put()`. But instead of holding the actual value it points to a linked
  list node that holds it.
* The linked list is used to maintain LRU order. Having a pointer (obtained via
  the hash map) to a node allows us to remove it on `O(1)` time and/or insert it
  at the head of the list again in `O(1)` time

```java
import java.util.HashMap;

class Node {
    Node prev;
    Node next;
    int key;
    int value;

    public Node(int key, int value) {
        this.key = key;
        this.value = value;
    }
}

class LRUCache {
    private final int capacity;
    private final HashMap<Integer, Node> cache;
    private Node head;
    private Node tail;

    public LRUCache(int capacity) {
        if (capacity < 1) {
            throw new IllegalArgumentException("The capacity must be at least 1: " + capacity);
        }

        this.capacity = capacity;
        this.cache = new HashMap<>();
    }

    public int get(int key) {
        final var node = this.cache.get(key);
        if (null == node) {
            return -1;
        }

        move_to_head(node);
        return node.value;
    }

    public void put(int key, int value) {
        final var n = this.cache.compute(key, (k, v) -> {
            var node = v;

            if (null == node) {
                node = new Node(k, value);
            } else {
                node.value = value;
            }

            return node;
        });

        move_to_head(n);
        if (this.capacity < this.cache.size()) {
            remove_tail();
        }
    }

    private void move_to_head(Node node) {
        if (node == head) {
            return;
        }

        final var next = node.next;
        final var prev = node.prev;

        // if PREV is null, this means that this is a new node,
        // because we have already checked for HEAD
        if (null != prev) {
            prev.next = next;
            if (null != next) {
                next.prev = prev;
            }
        }

        node.prev = null;
        node.next = this.head;

        if (null != this.head) {
            this.head.prev = node;
        }

        if (null == this.tail) {
            this.tail = node;
        } else if (node == this.tail) {
            this.tail = prev;
        }

        this.head = node;
    }

    private void remove_tail() {
        final var node = this.tail;
        this.cache.remove(node.key);

        tail = node.prev;
        if (null == this.tail) {
            this.head = null;
        } else {
            this.tail.next = null;
        }
    }
}
```
