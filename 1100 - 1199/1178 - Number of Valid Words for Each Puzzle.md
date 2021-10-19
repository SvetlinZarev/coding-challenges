# [1178. Number of Valid Words for Each Puzzle](https://leetcode.com/problems/number-of-valid-words-for-each-puzzle/)

## Problem

With respect to a given puzzle string, a word is valid if both the following
conditions are satisfied:

* word contains the first letter of puzzle.
* For each letter in word, that letter is in puzzle.
    * For example, if the puzzle is `abcdefg`, then valid words are `faced`,
      `cabbage`, and `baggage`, while invalid words are `beefed` (does not
      include `a`) and `based` (includes `s` which is not in the puzzle).

Return an array answer, where `answer[i]` is the number of words in the given
word list words that is valid with respect to the puzzle `puzzles[i]`.

#### Constraints

* `words[i]` an`d puzzles[i]` consist of lowercase English letters.
* Each `puzzles[i]` does not contain repeated characters.
* `puzzles[i].length == 7`
* `1 <= puzzles.length <= 10^4`
* `4 <= words[i].length <= 50`
* `1 <= words.length <= 10^5`

## Solution

### Observations

* The puzzle length is only 7, and each puzzle does not contain duplicate
  characters
* The words & puzzles contain only lowercase ASCII, thus the words may contain
  duplicates.
* If the number of unique characters in a word is more than 7, then it's
  definitely not a valid word
* We are not interested in the order of the characters in the words, only if
  they are present in the puzzles or not

### Using a Trie

#### Ideas

* Sort the characters in the words and then deduplicate the characters in the
  strings
* After the deduplication, if a word is longer than a puzzle, then it definitely
  is not a valid word, because it contains characters that are not present in
  the puzzle
* We can use a trie to store the pre-processed words. Instead of "word_end"
  marker, we'll use a counter which will track how many words end there (because
  of the dedup+sorting!)
* Then for each puzzle will do a search in the trie and just sum those counters

#### Implementation

* Time: 80 ms
* Memory: 25.3 MB

```rust
use std::collections::HashMap;

pub fn find_num_of_valid_words<W: AsRef<str>>(words: Vec<W>, puzzles: Vec<W>) -> Vec<i32> {
    let mut trie = Node::default();

    // Use a single, pre-allocated buffer in order to avoid wasting time in
    // (de)allocating temporary buffers
    let mut buffer = Vec::with_capacity(50);

    // Sort and deduplicate each word, then add it to the trie
    for word in words.iter().map(|w| w.as_ref()) {
        buffer.clear();
        buffer.extend_from_slice(word.as_bytes());
        buffer.sort_unstable();
        buffer.dedup();

        // Given that the max puzzle length is 7, if the word contains more
        // than 7 unique characters, then it cannot be a valid puzzle word
        if buffer.len() <= 7 {
            trie.insert(buffer.as_slice());
        }
    }

    // We can preallocate the result vector, because we know exactly
    // how many elements we need
    let mut result = Vec::with_capacity(puzzles.len());

    // We need to sort the puzzle input in order to reduce the number of
    // searches later
    for puzzle in puzzles.iter().map(|w| w.as_ref()) {
        buffer.clear();
        buffer.extend_from_slice(puzzle.as_bytes());
        buffer.sort_unstable();

        let first_char = puzzle.as_bytes()[0];
        let num_words = trie.matches(&buffer, first_char);
        result.push(num_words)
    }

    result
}

#[derive(Default)]
struct Node {
    nodes: HashMap<u8, Node>,

    // Track how many words end here. For instance after
    // sorting & deduplication any of "abc", "cba" and
    // "acb".. etc will end here, and we must count all
    // of them
    words: i32,
}

impl Node {
    pub fn insert<W: AsRef<[u8]>>(&mut self, word: W) {
        let word = word.as_ref();

        let mut node = self;
        for &ch in word.iter() {
            node = node.nodes.entry(ch).or_default();
        }
        node.words += 1;
    }

    pub fn matches<W: AsRef<[u8]>>(&self, word: W, mark: u8) -> i32 {
        let mut word = word.as_ref();
        dfs(self, mark, word)
    }
}

fn dfs(mut node: &Node, first: u8, word: &[u8]) -> i32 {
    let mut count = 0;

    // The current word contains the first character of the puzzle word, so we
    // need to add its word counter to the total count. If `first` != 0, then
    // the current word does not contain the first character of the puzzle, thus
    // we must ignore its word counter
    if first == 0 {
        count += node.words;
    }

    for (idx, ch) in word.iter().enumerate() {
        // Because the puzzle word is sorted, if the current character (i.e. `ch`)
        // is larger than the required first character, then we can `break` out of
        // the loop because it will not appear anywhere in the trie
        if first != 0 && *ch > first {
            break;
        }

        match node.nodes.get(ch) {
            None => continue,
            Some(n) => {
                // We need to use a local variable, because if `*ch == first`
                // for the current iteration of the loop, that will not be
                // true for the next iteration!
                let mut contains_first = first;
                if *ch == contains_first {
                    contains_first = 0;
                }

                // Given that we use sorted puzzles as input, we can safely
                // skip all processed characters (idx + 1), as they will be
                // "less" than the current character,thus they cannot appear
                // in the trie at that point, and thus we can greatly reduce
                // the number of search operations (`hashmap.get()`)
                count += dfs(n, contains_first, &word[idx + 1..]);
            }
        }
    }

    count
}
```

### Using bitmasks

#### Ideas

Given that the lowercase ASCII is only 26 characters, we can fit each
word/puzzle in a `u32`. Then checking if the word starts with the first letter
of the puzzle ot if the puzzle contains all letters of the words is as trivial
as a bitwise AND

#### Implementation

* Time: 214 ms
* Memory: 8.7 MB

```rust
pub fn find_num_of_valid_words<W: AsRef<str>>(words: Vec<W>, puzzles: Vec<W>) -> Vec<i32> {
    let mut words = words.iter().map(|w| word_to_int(w)).collect::<Vec<_>>();
    let mut puzzles = puzzles
        .iter()
        .map(|w| {
            let first_ch = first_char_to_mask(w);
            let as_int = word_to_int(w);
            (as_int, first_ch)
        })
        .collect::<Vec<_>>();

    let mut result = Vec::with_capacity(puzzles.len());
    for &(puzzle, first_ch) in puzzles.iter() {
        let mut counter = 0;
        for &word in words.iter() {
            if (word & puzzle == word) & (word & first_ch == first_ch) {
                counter += 1;
            }
        }
        result.push(counter);
    }
    result
}

fn word_to_int<W: AsRef<str>>(word: W) -> u32 {
    let word = word.as_ref().as_bytes();
    let mut bits = 0;

    for &ch in word {
        bits |= 1 << ch - b'a';
    }

    bits
}

fn first_char_to_mask<W: AsRef<str>>(word: W) -> u32 {
    let word = word.as_ref().as_bytes();
    1 << word[0] - b'a'
}
```