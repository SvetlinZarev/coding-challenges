# [705. Design HashSet](https://leetcode.com/problems/design-hashset/)

## Problem

### Description

Design a `HashSet` without using any built-in hash table libraries.

Implement `MyHashSet` class:

* `void add(key)` Inserts the value key into the `HashSet`.
* `bool contains(key)` Returns whether the value key exists in the `HashSet` or
  not.
* `void remove(key)` Removes the value key in the HashSet. If key does not exist
  in the `HashSet`, do nothing.

### Constraints

* `0 <= key <= 10^6`
* At most `10^4` calls will be made to `add`, `remove`, and `contains`.

### Examples

```text
Input
["MyHashSet", "add", "add", "contains", "contains", "add", "contains", "remove", "contains"]
[[], [1], [2], [1], [3], [2], [2], [2], [2]]
Output
[null, null, null, true, false, null, true, null, false]

Explanation
MyHashSet myHashSet = new MyHashSet();
myHashSet.add(1);      // set = [1]
myHashSet.add(2);      // set = [1, 2]
myHashSet.contains(1); // return True
myHashSet.contains(3); // return False, (not found)
myHashSet.add(2);      // set = [1, 2]
myHashSet.contains(2); // return True
myHashSet.remove(2);   // set = [1]
myHashSet.contains(2); // return False, (already removed)
```

## Solutions

### Simple set with resizing

```rust
struct MyHashSet {
    data: Vec<Vec<i32>>,
    size: usize,
}

impl MyHashSet {
    fn new() -> Self {
        const INITIAL_SIZE: usize = 16;
        Self {
            data: vec![vec![]; INITIAL_SIZE],
            size: 0,
        }
    }

    fn add(&mut self, key: i32) {
        if insert(&mut self.data, key) {
            self.size += 1;
        }

        if self.size >= self.data.len() - self.data.len() / 4 {
            self.data = resize(&mut self.data, self.size * 2);
        }
    }

    fn remove(&mut self, key: i32) {
        if remove(&mut self.data, key) {
            self.size -= 1;
        }

        if self.size > 32 && self.data.len() > self.size * 3 {
            let new_size = self.size + self.size / 2;
            self.data = resize(&mut self.data, new_size);
        }
    }

    fn contains(&self, key: i32) -> bool {
        let idx = index(key, self.data.len());
        self.data[idx].iter().any(|x| *x == key)
    }
}

fn resize(src: &mut Vec<Vec<i32>>, size: usize) -> Vec<Vec<i32>> {
    let mut data = vec![vec![]; size];
    src.iter().flat_map(|x| x.iter()).for_each(|&key| {
        insert(&mut data, key);
    });
    data
}

fn insert(data: &mut Vec<Vec<i32>>, key: i32) -> bool {
    let idx = index(key, data.len());
    if !data[idx].contains(&key) {
        data[idx].push(key);
        return true;
    }

    false
}

fn remove(data: &mut Vec<Vec<i32>>, key: i32) -> bool {
    let idx = index(key, data.len());
    if let Some(pos) = data[idx].iter().position(|x| *x == key) {
        data[idx].swap_remove(pos);
        return true;
    }

    false
}

fn index(key: i32, len: usize) -> usize {
    const PRIME: usize = 2usize.pow(16) + 1;
    (key.abs() as usize).wrapping_mul(PRIME) % len
}
```

## Related Problems

* [706. Design HashMap](706%20-%20Design%20HashMap.md)
