# [1286. Iterator for Combination](https://leetcode.com/problems/iterator-for-combination/)

## Problem

Design the `CombinationIterator` class:
* `CombinationIterator(string characters, int combinationLength)`: Initializes the object 
  with a string `characters` of sorted distinct lowercase English letters and a number 
  `combinationLength` as arguments.
* `next()`: Returns the next combination of length `combinationLength` in lexicographical order.
* `hasNext()`: Returns `true` if and only if there exists a next combination.


#### Constraints

* `1 <= combinationLength <= characters.length <= 15`
* All the characters of `characters` are unique.
* At most 10^4 calls will be made to `next()` and `hasNext()`.
* It's guaranteed that all calls of the function `next()` are valid.


#### Examples

```text
Input
["CombinationIterator", "next", "hasNext", "next", "hasNext", "next", "hasNext"]
[["abc", 2], [], [], [], [], [], []]
Output
[null, "ab", true, "ac", true, "bc", false]

Explanation
CombinationIterator itr = new CombinationIterator("abc", 2);
itr.next();    // return "ab"
itr.hasNext(); // return True
itr.next();    // return "ac"
itr.hasNext(); // return True
itr.next();    // return "bc"
itr.hasNext(); // return False
```


## Solutions

### Using bitmasks

Given that we will have at most 15 distinct characters, we can use a bitmask
to enumerate all possible combinations. We can increment or decremennt a
simple counter and then just check the number of bits set to 1. 

```rust
struct CombinationIterator {
    chars: Vec<char>,
    len: u32,
    mask: u32,
}

impl CombinationIterator {
    fn new(characters: String, combination_length: i32) -> Self {
        assert!(combination_length as usize <= characters.len());

        let chars: Vec<_> = characters.chars().collect();
        let chars_len = chars.len();

        Self {
            chars,
            len: combination_length as u32,
            mask: 1 << chars_len,
        }
    }

    fn next(&mut self) -> String {
        // find through bruteforce the next "valid" masak - i.e. a mask that has "len" bits set to 1
        loop {
            self.mask -= 1;
            if self.mask == 0 || self.mask.count_ones() == self.len {
                break;
            }
        }

        let mut word = Vec::with_capacity(self.len as usize);
        let mut mask = self.mask;

        while mask > 0 {
            let idx = mask.trailing_zeros();
            mask ^= 1 << idx;

            let pos = self.chars.len() - idx as usize - 1;
            let ch = self.chars[pos];
            word.push(ch);
        }

        // Because we pushed the characters in reverse order, we have to reverse the string
        // Alternatively we could have used direct indexing in order to avoid that operation
        word.iter().rev().collect()
    }

    fn has_next(&self) -> bool {
        let mut mask = self.mask;

        loop {
            mask -= 1;
            if mask == 0 || mask.count_ones() == self.len {
                break;
            }
        }

        mask != 0
    }
}
```
