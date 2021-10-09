# [212. Word Search II](https://leetcode.com/problems/word-search-ii/)

## Problem

Given an `m x n` board of characters and a list of strings words, return all
words on the board.

Each word must be constructed from letters of sequentially adjacent cells, where
adjacent cells are horizontally or vertically neighboring. The same letter cell
may not be used more than once in a word.

Input:

```text
board = [
 ["o","a","a","n"],
 ["e","t","a","e"],
 ["i","h","k","r"],
 ["i","f","l","v"]
]
words = ["oath","pea","eat","rain"]
```

Output:

```text
["eat","oath"]
```

#### Constraints:

* `m == board.length`
* `n == board[i].length`
* `1 <= m, n <= 12`
* `board[i][j]` is a lowercase English letter.
* `1 <= words.length <= 3 * 10^4`
* `1 <= words[i].length <= 10`
* `words[i]` consists of lowercase English letters.
* All the strings of words are unique.


## Solutions

### Using a Trie, storing all possible word combinations from the board

My first attempt at solving the problem was to build a [trie] by doing a DFS on
the board. This is the easiest approach to implement, but also the slowest and
the most memory-demanding one

* Time: 224 ms
* Memory: 57.6 MB

```rust
use std::collections::HashMap;

// For that problem we do not need to track the end of the word, because all
// substrings are valid solutions
#[derive(Default, Debug)]
struct Node {
    ptr: HashMap<u8, Node>,
}

impl Node {
    pub fn new() -> Self {
        Default::default()
    }

    // We'l use that to filter out the found words at the end
    pub fn starts_with<W: AsRef<[u8]>>(&self, prefix: W) -> bool {
        self.find(prefix).is_some()
    }

    fn find<W: AsRef<[u8]>>(&self, word: W) -> Option<&Node> {
        let word = word.as_ref();

        let mut node = self;
        for ch in word.iter() {
            match node.ptr.get(ch) {
                Some(link) => node = link,
                None => return None,
            }
        }

        Some(node)
    }

    // Insert a character into the trie and return the child node
    // In that way we do not need to start from the root every time 
    // we add a new character generated from the DFS
    pub fn insert_next(&mut self, ch: u8) -> &mut Node {
        self.ptr.entry(ch).or_default()
    }
}

pub fn find_words(mut board: Vec<Vec<char>>, mut words: Vec<String>) -> Vec<String> {
    // It's meaningless to compute and store words in the trie, which are longer
    // than the longest word we are looking for
    let max_depth = match words.iter().map(|w| w.len()).max() {
        None => return words,
        Some(len) => len,
    };

    let mut trie = Node::new();

    // generate all possible unique words with `max_depth` length by 
    // doing DFS on the board
    for r in 0..board.len() {
        for c in 0..board[r].len() {
            dfs(&mut board, &mut trie, max_depth, 0, r, c)
        }
    }

    // remove the prefixes not found in the trie
    words.retain(|w| trie.starts_with(w));
    words
}

fn dfs(
    board: &mut Vec<Vec<char>>,
    trie: &mut Node,
    max_depth: usize,
    depth: usize,
    r: usize,
    c: usize,
) {
    let ch = board[r][c] as u8;

    // check if we have already visited that cell and skip it in that case
    if ch == 0 {
        return;
    }

    let trie = trie.insert_next(ch);
    if depth + 1 == max_depth {
        // if we have reached the max depth we need to stop, because
        // we do not get anything by generating words longer than that
        return;
    }

    // mark the current cell as visited
    board[r][c] = 0 as char;

    // check the neighbouring cells. Doing the bound checks before 
    // making the call is faster
    if r > 0 {
        dfs(board, trie, max_depth, depth + 1, r - 1, c);
    }
    if r < board.len() - 1 {
        dfs(board, trie, max_depth, depth + 1, r + 1, c);
    }
    if c > 0 {
        dfs(board, trie, max_depth, depth + 1, r, c - 1);
    }
    if c < board[r].len() - 1 {
        dfs(board, trie, max_depth, depth + 1, r, c + 1);
    }

    // restore the previous value, so that we can continue with the DFS
    board[r][c] = ch as char;
}
```

### Using a Trie, storing only the words we are looking for

Although the previous solution passes the tests and is accepted by the judge, it
can be greatly improved. If we build our [trie] by using only the input words,
instead of all DFS generated words, then the trie will be much smaller and will
require much less memory.

This will also allow us to check fewer neighbouring cells on the board, thus
speeding the execution. The trick here is how to know when to terminate the DFS
and how to retrieve the words we found.

For the first problem - we'll just track in a variable how many words we've
found by decreasing a counter. When it hits 0, then we need to stop searching.

For the second problem, we'll just attach the words to the trie node, instead of
marking it as a '
word end'. That would allow us to quickly retrieve the word itself and also help
us avoid dealing with duplicate words

* Time: 112 ms
* Memory: 2.1 MB

```rust
use std::collections::HashMap;

#[derive(Default, Debug)]
struct Node {
    ptr: HashMap<u8, Node>,

    // instead of boolean `is_word`, we just attach the word itself
    word: Option<String>,
}

impl Node {
    pub fn new() -> Self {
        Default::default()
    }

    pub fn with_prefix(&mut self, ch: u8) -> Option<&mut Node> {
        self.ptr.get_mut(&ch)
    }

    pub fn insert(&mut self, word: String) {
        let w = word.as_bytes();

        let mut node = self;
        for ch in w.iter().copied() {
            node = node.ptr.entry(ch).or_default()
        }

        // attach the word
        node.word = Some(word);
    }

    pub fn take_word(&mut self) -> Option<String> {
        self.word.take()
    }
}

pub fn find_words(mut board: Vec<Vec<char>>, mut words: Vec<String>) -> Vec<String> {
    let mut trie = Node::new();
    let mut remaining = words.len();

    // we build our trie before doing DFS
    words.drain(..).for_each(|w| trie.insert(w));

    'all: for r in (0..board.len()).rev() {
        for c in (0..board[r].len()).rev() {
            // if we've found all the words we are looking for, then we can stop
            if remaining == 0 {
                break 'all;
            }

            // we'll reuse the `words` vector, because it's empty due 
            // to the `.drain()` call above
            dfs(&mut board, &mut trie, &mut words, &mut remaining, r, c)
        }
    }

    words
}

fn dfs(
    board: &mut Vec<Vec<char>>,
    trie: &mut Node,
    result: &mut Vec<String>,
    remaining: &mut usize,
    r: usize,
    c: usize,
) {
    let ch = board[r][c] as u8;

    // If we've already visited that cell or if we've found all the words,
    // then we have to stop 
    if ch == 0 || *remaining == 0 {
        return;
    }

    // If there is no node in the trie corresponding to the current character,
    // then none of our words contain it, so we can avoid going deeper on that 
    // DFS sub-problem.
    // Otherwise return the trie node for that character
    let trie = match trie.with_prefix(ch) {
        None => return,
        Some(trie) => trie,
    };

    // If this trie node is a terminal node, i.e. one containing a word,
    // then we have to take it from the tree and push it to our `result`
    if let Some(word) = trie.take_word() {
        result.push(word);

        // Each time we find a word we have to decrease the counter. because we 
        // are working with unsigned ints, we have to check for 0 in order to 
        // avoid integer underflow
        *remaining -= 1;
        if *remaining == 0 {
            // we've found all the words - terminate the DFS
            return;
        }
    }

    // mark the current cell as visited
    board[r][c] = 0 as char;

    // visit the neighbouring cells
    if r > 0 {
        dfs(board, trie, result, remaining, r - 1, c);
    }
    if r < board.len() - 1 {
        dfs(board, trie, result, remaining, r + 1, c);
    }
    if c > 0 {
        dfs(board, trie, result, remaining, r, c - 1);
    }
    if c < board[r].len() - 1 {
        dfs(board, trie, result, remaining, r, c + 1);
    }

    // un-mark the current cell
    board[r][c] = ch as char;
}
```

#### Further optimisations

##### Reducing the number of words to search for

We can calculate the frequency (how many times a character is encountered) of
the characters in the board. Then we can do the same for each input word. If the
frequency of some word characters is greater than the frequency of the board
characters, then this word cannot possibly be contained in the board.

In that way we can reduce the number of nodes in the trie and the number of
expected words, thus we might be able to stop doing DFS earlier.

* Time: 28 ms
* Memory: 2.1 MB

```rust
pub fn find_words(mut board: Vec<Vec<char>>, mut words: Vec<String>) -> Vec<String> {
    //find the frequency of each character in the board
    let mut board_freq = [0; (b'z' - b'a' + 1) as usize];
    for r in 0..board.len() {
        for c in 0..board[r].len() {
            board_freq[(board[r][c] as u8 - b'a') as usize] += 1;
        }
    }

    // filter out the words that cannot be found doe to missing characters
    words.retain(|w| {
        let mut word_freq = [0; (b'z' - b'a' + 1) as usize];
        for ch in w.as_bytes().iter().copied().map(|ch| (ch - b'a') as usize) {
            word_freq[ch] += 1;
        }

        board_freq
            .iter()
            .zip(word_freq.iter())
            .any(|(a, b)| *a < *b)
            .not()
    });

    let mut trie = Node::new();
    let mut remaining = words.len();

    words.drain(..).for_each(|w| trie.insert(w));

    'all: for r in (0..board.len()).rev() {
        for c in (0..board[r].len()).rev() {
            if remaining == 0 {
                break 'all;
            }
            dfs(&mut board, &mut trie, &mut words, &mut remaining, r, c)
        }
    }

    words
}

// the rest of the source is the same
```

##### Choosing a faster hash function

Our trie uses a `HashMap` to store the child nodes. But Rust's hash map is known
for its slow default hash function. If we use a faster one we can speed our
solution even more:

* Time: 16 ms
* Memory 2.1 MB

```rust
pub struct FnvHasher(u64);

impl Default for FnvHasher {
    fn default() -> FnvHasher {
        FnvHasher(0xcbf29ce484222325)
    }
}

impl Hasher for FnvHasher {
    fn finish(&self) -> u64 {
        self.0
    }

    fn write(&mut self, bytes: &[u8]) {
        let FnvHasher(mut hash) = *self;
        for byte in bytes {
            hash = hash ^ (*byte as u64);
            hash = hash * 0x100000001b3;
        }
        *self = FnvHasher(hash);
    }
}

pub struct HashBuilder<H> {
    _phantom: PhantomData<H>,
}

impl<H: Hasher + Default> BuildHasher for HashBuilder<H> {
    type Hasher = H;

    fn build_hasher(&self) -> Self::Hasher {
        H::default()
    }
}

impl<H: Hasher + Default> Default for HashBuilder<H> {
    fn default() -> Self {
        HashBuilder {
            _phantom: PhantomData,
        }
    }
}
```

Then we have to modify the trie to use our new hasher:

```rust
#[derive(Default, Debug)]
struct Node {
    ptr: HashMap<u8, Node, HashBuilder<FnvHasher>>,
    word: Option<String>,
}
```

#### Reducing the number of searches by soft-removing the found words from the trie

If we've found a word, then we can remove it from the trie in order to reduce the 
number of DFSes - in othre words - if we know that the current trie branch will not 
yield a result, then it's meaningless to traverse it.

In order to accomplish that, we can introduce a `reference count` in each trie node.
We have to increment that counter each time we add a word that contains the 
corresponding leter at that position and decrement it when we reomve a word containing
that character.

If the reference count is zero, then we treat that node as if it didn't exist.

* Time: 8ms
* Memory: 2.1MB


### Final Source Code

```rust
#[derive(Default, Debug)]
struct Node {
    ptr: HashMap<u8, Node, HashBuilder<FnvHasher>>,
    word: Option<String>,
    refs: usize,
}

impl Node {
    pub fn new() -> Self {
        Default::default()
    }

    pub fn with_prefix(&mut self, ch: u8) -> Option<&mut Node> {
        if self.refs == 0 {
            return None;
        }

        self.ptr.get_mut(&ch).filter(|n| n.refs > 0)
    }

    pub fn insert(&mut self, word: String) {
        let w = word.as_bytes();

        let mut node = self;
        for ch in w.iter().copied() {
            node.refs += 1;
            node = node.ptr.entry(ch).or_default();
        }

        node.refs += 1;
        node.word = Some(word);
    }

    pub fn take_word(&mut self) -> Option<String> {
        self.word.take()
    }

    pub fn decrement_refs(&mut self, times: usize) {
        self.refs = self.refs.saturating_sub(times);
    }

    fn refs(&self) -> usize {
        self.refs
    }
}

pub fn find_words(mut board: Vec<Vec<char>>, mut words: Vec<String>) -> Vec<String> {
    let mut board_freq = [0; (b'z' - b'a' + 1) as usize];
    for r in 0..board.len() {
        for c in 0..board[r].len() {
            board_freq[(board[r][c] as u8 - b'a') as usize] += 1;
        }
    }

    words.retain(|w| {
        let mut word_freq = [0; (b'z' - b'a' + 1) as usize];
        for ch in w.as_bytes().iter().copied().map(|ch| (ch - b'a') as usize) {
            word_freq[ch] += 1;
        }

        board_freq
            .iter()
            .zip(word_freq.iter())
            .any(|(a, b)| *a < *b)
            .not()
    });

    let mut trie = Node::new();
    let mut remaining = words.len();

    words.drain(..).for_each(|w| trie.insert(w));

    'all: for r in (0..board.len()).rev() {
        for c in (0..board[r].len()).rev() {
            if remaining == 0 {
                break 'all;
            }
            dfs(&mut board, &mut trie, &mut words, &mut remaining, r, c);
        }
    }

    words
}

fn dfs(
    board: &mut Vec<Vec<char>>,
    trie: &mut Node,
    result: &mut Vec<String>,
    remaining: &mut usize,
    r: usize,
    c: usize,
) -> usize {
    let ch = board[r][c] as u8;
    if ch == 0 || *remaining == 0 {
        return 0;
    }

    let trie = match trie.with_prefix(ch) {
        None => return 0,
        Some(trie) => trie,
    };

    let mut found = 0;
    if let Some(word) = trie.take_word() {
        result.push(word);
        found += 1;

        *remaining -= 1;
        if *remaining == 0 || trie.refs() == 0 {
            trie.decrement_refs(found);
            return found;
        }
    }

    board[r][c] = 0 as char;
    if r > 0 {
        found += dfs(board, trie, result, remaining, r - 1, c);
    }
    if r < board.len() - 1 {
        found += dfs(board, trie, result, remaining, r + 1, c);
    }
    if c > 0 {
        found += dfs(board, trie, result, remaining, r, c - 1);
    }
    if c < board[r].len() - 1 {
        found += dfs(board, trie, result, remaining, r, c + 1);
    }
    board[r][c] = ch as char;

    if found > 0 {
        trie.decrement_refs(found);
    }

    found
}
```

## Links and resources

* [Trie] on Wikipedia
* [LeetCode] page

[leetcode]: https://leetcode.com/problems/word-search-ii/

[trie]: https://en.wikipedia.org/wiki/Trie
