# [187. Repeated DNA Sequences](https://leetcode.com/problems/repeated-dna-sequences/)

## Problem

### Description

The DNA sequence is composed of a series of nucleotides abbreviated as `A`, `C`
, `G`, and `T`.

* For example, `ACGAATTCCG` is a DNA sequence.

When studying DNA, it is useful to identify repeated sequences within the DNA.

Given a string `s` that represents a DNA sequence, return all the 10-letter-long
sequences (substrings) that occur more than once in a DNA molecule. You may
return the answer in any order.

### Constraints

* `1 <= s.length <= 10^5`
* `s[i]` is either `A`, `C`, `G`, or `T`.

### Examples

```text
Input: s = "AAAAACCCCCAAAAACCCCCCAAAAAGGGTTT"
Output: ["AAAAACCCCC","CCCCCAAAAA"]
```

```text
Input: s = "AAAAAAAAAAAAA"
Output: ["AAAAAAAAAA"]
```

## Solutions

### Counting substrings in a `HashMap`

```rust
use std::collections::HashMap;

pub fn find_repeated_dna_sequences(s: String) -> Vec<String> {
    let mut sequences = HashMap::new();
    s.as_bytes().windows(10).for_each(|seq| {
        sequences.entry(seq).and_modify(|c| *c += 1).or_insert(1u32);
    });

    sequences
        .into_iter()
        .filter(|&(_seq, count)| count > 1)
        .map(|(seq, _count)| seq)
        .map(|seq| std::str::from_utf8(seq).unwrap())
        .map(|seq| seq.to_owned())
        .collect()
}
```

### Rolling Hash

```rust
pub fn find_repeated_dna_sequences(s: String) -> Vec<String> {
    let s = s.as_bytes();
    if s.len() < 10 {
        return vec![];
    }

    // there are only `4.pow(10)` possible 10-letter sequences,
    // which is equal to `(2.pow(2)).pow(10)` which is equal to
    // `2.pow(20)` which in turn can be computed as `1 << 20`
    let mut visited = vec![0u8; 1 << 20];
    let mut answer = vec![];

    let mut hash = 0usize;
    for idx in 0..10 {
        hash = hash << 2;
        hash = hash | gene_to_two_bit_number(s[idx]);
    }
    visited[hash] = 1;

    for idx in 10..s.len() {
        hash = hash << 2;
        hash = hash | gene_to_two_bit_number(s[idx]);
        // clear the 2 MSB bits from the **20 bit** number
        hash = hash & ((1 << 20) - 1);

        visited[hash] = match visited[hash] {
            0 => 1, // we've seen the substring for thr first time
            1 => {
                // we've seen it for a secondtime, so add it to the answer
                answer.push(std::str::from_utf8(&s[idx - 9..=idx]).unwrap().to_owned());
                2
            }
            _ => 2, // already added to the answer, so do nothing
        }
    }

    answer
}
```

## Related Problems

* [1461. Check If a String Contains All Binary Codes of Size K](/leetcode/1400%20-%201499/1461%20-%20Check%20If%20a%20String%20Contains%20All%20Binary%20Codes%20of%20Size%20K.md)
