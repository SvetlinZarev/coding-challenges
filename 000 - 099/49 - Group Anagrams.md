# [49. Group Anagrams](https://leetcode.com/problems/group-anagrams/)

## Problem

Given an array of strings `strs`, group the anagrams together. You can return
the answer in any order.

An Anagram is a word or phrase formed by rearranging the letters of a different
word or phrase, typically using all the original letters exactly once.

Input:

```text
["eat","tea","tan","ate","nat","bat"]
```

Output:

```text
[["bat"],["nat","tan"],["ate","eat","tea"]]
```

#### Constraints:

```text
1 <= strs.length <= 104
0 <= strs[i].length <= 100
strs[i] consists of lowercase English letters.
```

## Solutions

### Using the sorted words as map keys

* Time: 8 ms
* Memory: 4.7 MB

```rust
use std::collections::HashMap;

pub fn group_anagrams(strs: Vec<String>) -> Vec<Vec<String>> {
    let mut groups = HashMap::with_capacity(strs.len());

    for string in strs.into_iter() {
        let mut key = string.as_bytes().to_vec();
        key.sort_unstable();

        groups
            .entry(key)
            .or_insert_with(|| Vec::with_capacity(4))
            .push(string);
    }

    groups.into_iter().map(|(_, v)| v).collect()
}
```

### Using the character frequency as a map key

* Time: 4 ms
* Memory: 6.4 MB

```rust
use std::collections::HashMap;

pub fn group_anagrams(strs: Vec<String>) -> Vec<Vec<String>> {
    let mut map = HashMap::new();

    for string in strs.into_iter() {
        let mut buf = [0; (b'z' - b'a' + 1) as usize];

        let s = string.as_bytes();
        for ch in s.iter().copied().map(|ch| ch - b'a') {
            buf[ch as usize] += 1;
        }

        map.entry(buf).or_insert(vec![]).push(string);
    }

    map.drain().map(|(_, v)| v).collect()
}
```

### Using a 'prime hash'

We can assign a [prime number] to each letter:

```text
a -> 1,
b -> 3,
c -> 5,
...
z -> 101
```

Then we can calculate the "hash" of each string by mapping the character to its
prime number and multiplying the result:

```
let mut result = 1;
for ch in characters.iter().copied(){
    result *= ch;
}
```

Thus, each anagram will have the same hash, because the multiplication is
commutative (
i.e. `a*b == b*a`).

Unfortunately this solution is not applicable to the current problem, because
the hashes quickly overflow the available integer types:

```text
log101(2^64) ~= 9.6
```

Thus, if a string has a length greater than 9 character the hash might not fit
into `u64`

## Links and resources

* Wikipedia article on [multiplication properties]
* A list of [prime number]s

[multiplication properties]: https://en.wikipedia.org/wiki/Multiplication#Properties

[prime number]: https://en.wikipedia.org/wiki/List_of_prime_numbers