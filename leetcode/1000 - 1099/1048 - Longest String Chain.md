# [1048. Longest String Chain](https://leetcode.com/problems/longest-string-chain/)

## Problem

### Description

You are given an array of words where each word consists of lowercase English
letters.

`wordA` is a predecessor of `wordB` if and only if we can insert exactly one
letter anywhere in `wordA` without changing the order of the other characters to
make it equal to `wordB`.

* For example, `abc` is a predecessor of `abac`, while `cba` is not a
  predecessor of `bcad`.

A word chain is a sequence of words `[word1, word2, ..., wordk]` with `k >= 1`,
where `word1` is a predecessor of `word2`, `word2` is a predecessor of `word3`,
and so on. A single word is trivially a word chain with `k == 1`.

Return the length of the longest possible word chain with words chosen from the
given list of words.

### Constraints

* `1 <= words.length <= 1000`
* `1 <= words[i].length <= 16`
* `words[i]` only consists of lowercase English letters.

### Examples

```text
Input: words = ["a","b","ba","bca","bda","bdca"]
Output: 4
Explanation: One of the longest word chains is ["a","ba","bda","bdca"].
```

```text
Input: words = ["xbc","pcxbcf","xb","cxbc","pcxbc"]
Output: 5
Explanation: All the words can be put in a word chain ["xb", "xbc", "cxbc", "pcxbc", "pcxbcf"].
```

```text
Input: words = ["abcd","dbqca"]
Output: 1
Explanation: The trivial word chain ["abcd"] is one of the longest word chains.
["abcd","dbqca"] is not a valid word chain because the ordering of the letters is changed.
```

## Solutions

## Recursive DP/Memoization

```rust
pub fn longest_str_chain(mut words: Vec<String>) -> i32 {
    // Sort by length in reverse order - i.e. the longer words should come first
    words.sort_unstable_by(|a, b| a.len().cmp(&b.len()).reverse());

    // Track the chain length for each word.
    let mut cache = vec![1; words.len()];

    let mut answer = 0;
    for idx in 0..words.len() {
        // Recursively calculate the chain length for each word
        answer = answer.max(chain_length(&words, &mut cache, idx));
    }
    answer
}

fn chain_length(words: &[String], cache: &mut [i32], idx: usize) -> i32 {
    // IF the chain length is already known (i.e. already computed 
    // or a word with length of 1), then return it immediately
    if cache[idx] != 1 || words[idx].len() == 1 {
        return cache[idx];
    }

    let mut max_chain = 0;
    for pos in idx + 1..words.len() {
        let len_diff = words[idx].len() - words[pos].len();
        // Because the words are sorted by length, we have to skip over the 
        // words with the same length as they cannot be predecessors
        if len_diff == 0 {
            continue;
        }

        // Because the words are sorted by length, we can break early once we 
        // reach a length difference greater than 1, because all the other 
        // words will be shorter and shorter and cannot be predecessors
        if len_diff > 1 {
            break;
        }

        // Check if the word is predecessor and update the max chain length accordingly
        if is_predecessor(words[pos].as_bytes(), words[idx].as_bytes()) {
            max_chain = max_chain.max(chain_length(words, cache, pos));
        }
    }

    // Remember the max chain length for the current word, 
    // so that we do not need to recompute it again
    cache[idx] = 1 + max_chain;
    cache[idx]
}

// We need a Longest-Common-Subsequence (see LC-1143) with length equal 
// to the shorter string AND a difference in lengths equal of 1 character
//
// But the LCS algorithm is O(m*n) and requires additional memory. Here we
// can exploit the fact that the string lengths must be different by exactly
// one character, so we can check if A is predecessor of B in O(m+n) time
// without any allocations
fn is_predecessor(a: &[u8], b: &[u8]) -> bool {
    if a.len() >= b.len() || b.len() - a.len() != 1 {
        return false;
    }

    let mut diff = 0;
    let mut i = 0;
    let mut j = 0;

    while diff <= 1 && (i < a.len() || j < b.len()) {
        if i == a.len() || j == b.len() {
            diff += 1;

            // we know already checked that the length difference 
            // is 1, so we can BREAK instead of CONTINUE
            break;
        }

        if a[i] != b[j] {
            diff += 1;
            j += 1;
            continue;
        }

        i += 1;
        j += 1;
    }

    diff == 1
}
```

## Related Problems

* [1143. Longest Common Subsequence](/leetcode/1100%20-%201199/1143%20-%20Longest%20Common%20Subsequence.md)
