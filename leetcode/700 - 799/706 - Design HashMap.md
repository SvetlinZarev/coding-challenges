# [706. Design HashMap](https://leetcode.com/problems/design-hashmap/)

## Problem

Design a `HashMap` without using any built-in hash table libraries.

Implement the `MyHashMap` class:

* `MyHashMap()` initializes the object with an empty map.
* `void put(int key, int value)` inserts a `(key, value)` pair into the HashMap.
  If the key already exists in the map, update the corresponding value.
* `int get(int key)` returns the value to which the specified key is mapped,
  or `-1` if this map contains no mapping for the key.
* `void remove(key)` removes the `key` and its corresponding `value` if the map
  contains the mapping for the key.

#### Constraints

* `0 <= key, value <= 10^6`
* At most 10^4 calls will be made to `put`, `get`, and `remove`.

## Solution

```rust
#[derive(Debug, Clone)]
enum Entry {
    Single((i32, i32)),
    Multi(Vec<(i32, i32)>),
}

struct MyHashMap {
    data: Vec<Option<Entry>>,
}

impl MyHashMap {
    fn new() -> Self {
        MyHashMap {
            data: vec![None; 257],
        }
    }

    fn put(&mut self, key: i32, value: i32) {
        let idx = key as usize % self.data.len();
        let entry = &mut self.data[idx];
        match entry.as_mut() {
            None => {
                *entry = Some(Entry::Single((key, value)));
            }

            Some(entry) => match entry {
                Entry::Single((k, v)) => {
                    if *k == key {
                        *v = value;
                    } else {
                        let mut multi = if *k < key {
                            vec![(*k, *v), (key, value)]
                        } else {
                            vec![(key, value), (*k, *v)]
                        };

                        *entry = Entry::Multi(multi);
                    }
                }
                Entry::Multi(multi) => {
                    match multi.binary_search_by(|(k, _)| key.cmp(k).reverse()) {
                        Ok(idx) => multi[idx] = (key, value),
                        Err(idx) => multi.insert(idx, (key, value)),
                    }
                }
            },
        }
    }

    fn get(&self, key: i32) -> i32 {
        let idx = key as usize % self.data.len();
        match &self.data[idx] {
            None => -1,

            Some(Entry::Single((k, v))) => {
                if *k != key {
                    return -1;
                }

                *v
            }

            Some(Entry::Multi(multi)) => {
                match multi.binary_search_by(|(k, _)| key.cmp(k).reverse()) {
                    Err(_) => -1,
                    Ok(idx) => multi[idx].1,
                }
            }
        }
    }

    fn remove(&mut self, key: i32) {
        let idx = key as usize % self.data.len();

        match &mut self.data[idx] {
            None => {
                // do nothing
            }

            Some(Entry::Single((k, v))) => {
                if *k == key {
                    self.data[idx] = None;
                }
            }

            Some(Entry::Multi(multi)) => {
                match multi.binary_search_by(|(k, _)| key.cmp(k).reverse()) {
                    Err(_) => {
                        // do nothing
                    }
                    Ok(pos) => {
                        multi.remove(pos);
                        if multi.is_empty() {
                            self.data[idx] = None;
                        } else if multi.len() == 1 {
                            self.data[idx] = Some(Entry::Single((multi[0].0, multi[0].1)));
                        }
                    }
                }
            }
        }
    }
}

/**
 * Your MyHashMap object will be instantiated and called as such:
 * let obj = MyHashMap::new();
 * obj.put(key, value);
 * let ret_2: i32 = obj.get(key);
 * obj.remove(key);
 */
```

## Related Problems

* [705. Design HashSet](705%20-%20Design%20HashSet.md)
