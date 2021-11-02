# [68. Text Justification](https://leetcode.com/problems/text-justification/)

## Problem

Given an array of strings `words` and a width `maxWidth`, format the text such
that each line has exactly `maxWidth` characters and is fully (left and right)
justified.

You should pack your words in a greedy approach; that is, pack as many words as
you can in each line. Pad extra spaces `' '` when necessary so that each line
has exactly `maxWidth` characters.

Extra spaces between words should be distributed as evenly as possible. If the
number of spaces on a line does not divide evenly between words, the empty slots
on the left will be assigned more spaces than the slots on the right.

For the last line of text, it should be left-justified and no extra space is
inserted between words.

* A word is defined as a character sequence consisting of non-space characters
  only.
* Each word's length is guaranteed to be greater than 0 and not
  exceed `maxWidth`.
* The input array `words` contains at least one word.

#### Constraints

* `1 <= words.length <= 300`
* `1 <= words[i].length <= 20`
* `words[i]` consists of only English letters and symbols.
* `1 <= maxWidth <= 100`
* `words[i].length <= maxWidth`

#### Examples

```text
Input: words = ["This", "is", "an", "example", "of", "text", "justification."], maxWidth = 16
Output:
[
   "This    is    an",
   "example  of text",
   "justification.  "
]
```

```text
Input: words = ["What","must","be","acknowledgment","shall","be"], maxWidth = 16
Output:
[
  "What   must   be",
  "acknowledgment  ",
  "shall be        "
]
Explanation: Note that the last line is "shall be    " instead of "shall     be", because the last line must be left-justified instead of fully-justified.
Note that the second line is also left-justified becase it contains only one word.
```

```text
Input: words = ["Science","is","what","we","understand","well","enough","to","explain","to","a","computer.","Art","is","everything","else","we","do"], maxWidth = 20
Output:
[
  "Science  is  what we",
  "understand      well",
  "enough to explain to",
  "a  computer.  Art is",
  "everything  else  we",
  "do                  "
]
```

## Solution

```rust
pub fn full_justify(words: Vec<String>, max_len: usize) -> Vec<String> {
    let mut result = vec![];

    let mut line_len = 0;
    let mut from = 0;

    for idx in 0..words.len() {
        line_len += words[idx].len();

        if idx < words.len() - 1 {
            if line_len + words[idx + 1].len() + (idx - from) + 1 <= max_len {
                continue;
            }
        }

        let mut line = String::with_capacity(max_len);

        // do full justification if it's not the last line
        if idx < words.len() - 1 {
            let word_count = idx - from + 1;
            let all_spaces = max_len - line_len;

            let mut eq_spaces = 0;
            let mut additional = 0;
            if word_count > 1 {
                eq_spaces = all_spaces / (word_count - 1);
                additional = all_spaces % (word_count - 1);
            }

            for word in &words[from..=idx] {
                if !line.is_empty() {
                    let mut spaces = eq_spaces;
                    if additional > 0 {
                        spaces += 1;
                        additional -= 1;
                    }

                    (0..spaces).for_each(|_| line.push(' '));
                }

                line.push_str(word);
            }
        } else {
            // this is the last line, so we'll do only left justification
            for word in &words[from..] {
                if !line.is_empty() {
                    line.push(' ');
                }
                line.push_str(word);
            }
        }

        // in case of the last row, which should be left-justified
        // or in case of a single word on the line
        while line.len() < max_len {
            line.push(' ');
        }

        result.push(line);

        // reset the state for the next row
        from = idx + 1;
        line_len = 0;
    }

    result
}
```