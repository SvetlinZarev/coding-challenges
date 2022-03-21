# [763. Partition Labels](https://leetcode.com/problems/partition-labels/)

## Problem

### Description

You are given a string `s`. We want to partition the string into as many parts
as possible so that each letter appears in at most one part.

Note that the partition is done so that after concatenating all the parts in
order, the resultant string should be `s`.

Return a list of integers representing the size of these parts.

### Constraints

* `1 <= s.length <= 500`
* `s` consists of lowercase English letters.

### Examples

```text
Input: s = "ababcbacadefegdehijhklij"
Output: [9,7,8]
Explanation:
The partition is "ababcbaca", "defegde", "hijhklij".
This is a partition so that each letter appears in at most one part.
A partition like "ababcbacadefegde", "hijhklij" is incorrect, because it splits s into less parts.
```

```text
Input: s = "eccbbbbdec"
Output: [10]
```

## Solutions

### Keep track of the last occurrence of each character

```rust
const ASCII_LETTERS: usize = (b'z' - b'a' + 1) as usize;

pub fn partition_labels<S: AsRef<[u8]>>(s: S) -> Vec<i32> {
    let string = s.as_ref();

    let mut last = [0; ASCII_LETTERS];
    for (idx, ch) in string.iter().map(|&ch| (ch - b'a') as usize).enumerate() {
        last[ch] = idx; // the last position where `ch` is encountered
    }

    let mut start = 0;
    let mut end = 0;
    let mut answer = vec![];

    for (idx, ch) in string.iter().map(|&ch| (ch - b'a') as usize).enumerate() {
        end = end.max(last[ch]);

        // push the interval only if the characters between [start;end] are 
        // encountered only within that interval
        if idx == end {
            answer.push((end - start + 1) as i32);
            start = end + 1;
        }
    }

    answer
}
```

### Merging of intervals

We can reduce this problem to the one of merging overlapping intervals. We just
need to find the first & last occurrence of each letter.

```rust
const ASCII_LETTERS: usize = (b'z' - b'a' + 1) as usize;

pub fn partition_labels<S: AsRef<[u8]>>(s: S) -> Vec<i32> {
    let string = s.as_ref();

    let mut range = [None; ASCII_LETTERS];
    for (idx, ch) in string.iter().map(|&ch| (ch - b'a') as usize).enumerate() {
        range[ch] = Some(match range[ch] {
            Some((start, _end)) => (start, idx),
            None => (idx, idx),
        });
    }

    range.sort_unstable_by(|a, b| {
        // put the `None` values at the end of the array
        if a.is_none() || b.is_none() {
            return a.cmp(b).reverse();
        }

        // put the smaller `start` indexes at the front of th array
        a.cmp(b)
    });

    let mut answer = vec![];
    let mut interval = None;

    range
        .into_iter()
        .take_while(|x| x.is_some())
        .map(|x| x.unwrap())
        .for_each(|(from, to)| {
            interval = Some(match interval.take() {
                None => (from, to), // This is the first interval, so just set it
                Some((start, end)) => {
                    if from <= end {
                        // the intervals are overlapping. Extend the "end" 
                        // of the interval
                        (start, end.max(to))
                    } else {
                        // The intervals do not overlap - push the old one to 
                        // the result and replace it with the new one
                        answer.push((end - start + 1) as i32);
                        (from, to)
                    }
                }
            });
        });

    // Handle the last interval
    if let Some((start, end)) = interval.take() {
        answer.push((end - start + 1) as i32);
    }

    answer
}
```

## Related Problems

* [56. Merge Intervals](/leetcode/000%20-%20099/56%20-%20Merge%20Intervals.md)