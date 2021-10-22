# [380. Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/)

## Problem

Implement the `RandomizedSet` class:

* `RandomizedSet()` Initializes the `RandomizedSet` object.
* `bool insert(int val)` Inserts an item val into the set if not present.
  Returns `true` if the item was not present, `false` otherwise.
* `bool remove(int val)` Removes an item val from the set if present.
  Returns `true` if the item was present, `false` otherwise.
* `int getRandom()` Returns a random element from the current set of elements (
  it's guaranteed that at least one element exists when this method is called).
  Each element must have the same probability of being returned.

You must implement the functions of the class such that each function works in
average O(1) time complexity.

#### Constraints

* `-2^31 <= val <= 2^31 - 1`
* At most `2 * 10^5` calls will be made to `insert`, `remove`, and `getRandom`.
* There will be at least one element in the data structure when `getRandom` is
  called.

## Solution

```rust
use std::collections::hash_map::Entry;
use std::collections::HashMap;

struct RandomizedSet {
    rand: RandomState,
    map: HashMap<i32, usize>,
    val: Vec<i32>,
}

impl RandomizedSet {
    fn new() -> Self {
        RandomizedSet {
            rand: RandomState::default(),
            map: HashMap::with_capacity(1024),
            val: Vec::with_capacity(1024),
        }
    }

    fn insert(&mut self, val: i32) -> bool {
        match self.map.entry(val) {
            Entry::Occupied(_) => false,
            Entry::Vacant(e) => {
                self.val.push(val);
                e.insert(self.val.len() - 1);
                true
            }
        }
    }

    fn remove(&mut self, val: i32) -> bool {
        match self.map.remove(&val) {
            None => false,
            Some(idx) => {
                if idx == self.val.len() - 1 {
                    self.val.pop();
                } else {
                    self.val.swap_remove(idx);
                    let idx = idx.min(self.val.len() - 1);
                    let val = self.val[idx];
                    self.map.entry(val).and_modify(|e| *e = idx);
                }
                true
            }
        }
    }

    fn get_random(&mut self) -> i32 {
        let idx = self.rand.next() % self.val.len();
        self.val[idx]
    }
}

// Xoroshiro++
// https://prng.di.unimi.it/xoroshiro128plusplus.c
struct RandomState(u64, u64);

impl Default for RandomState {
    fn default() -> Self {
        RandomState(0x2bd7a6a6e99c2ddc, 0x0992ccaf6a6fca05)
    }
}

impl RandomState {
    fn next(&mut self) -> usize {
        let mut s0 = self.0;
        let mut s1 = self.1;

        let result = (s0.overflowing_add(s1).0)
            .rotate_left(17)
            .overflowing_add(s0)
            .0;

        s1 ^= s0;
        self.0 = s0.rotate_left(49) ^ s1 ^ (s1 << 21);
        self.1 = s1.rotate_left(28);

        result as usize
    }
}

```