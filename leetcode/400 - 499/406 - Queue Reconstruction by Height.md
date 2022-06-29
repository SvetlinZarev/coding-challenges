# [406. Queue Reconstruction by Height](https://leetcode.com/problems/queue-reconstruction-by-height/)

## Problem

### Description

You are given an array of people, `people`, which are the attributes of some
people in a queue (not necessarily in order). Each `people[i] = [hi, ki]`
represents the ith person of height `hi` with exactly `ki` other people in front
who have a height greater than or equal to `hi`.

Reconstruct and return the queue that is represented by the input array `people`
. The returned queue should be formatted as an array `queue`,
where `queue[j] = [hj, kj]` is the attributes of the `j`th person in the
queue (`queue[0]` is the person at the front of the queue).

### Constraints

* `1 <= people.length <= 2000`
* `0 <= hi <= 10^6`
* `0 <= ki < people.length`
* It is guaranteed that the queue can be reconstructed.

### Examples

```text
Input: people = [[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]
Output: [[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]]
Explanation:
Person 0 has height 5 with no other people taller or the same height in front.
Person 1 has height 7 with no other people taller or the same height in front.
Person 2 has height 5 with two persons taller or the same height in front, which is person 0 and 1.
Person 3 has height 6 with one person taller or the same height in front, which is person 1.
Person 4 has height 4 with four people taller or the same height in front, which are people 0, 1, 2, and 3.
Person 5 has height 7 with one person taller or the same height in front, which is person 1.
Hence [[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]] is the reconstructed queue.
```

```text
Input: people = [[6,0],[5,0],[4,0],[3,2],[2,2],[1,4]]
Output: [[4,0],[5,0],[2,2],[3,2],[1,4],[6,0]]
```

### Hints

> What can you say about the position of the shortest person? If the position of
> the shortest person is `i`, how many people would be in front of the shortest
> person?


> Once you fix the position of the shortest person, what can you say about the
> position of the second shortest person?

## Solutions

### Sorting #1

```rust
use std::cmp::Ordering;


pub fn reconstruct_queue(mut people: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    // put the shortest people first, and two persons have the same 
    // height, put the one with less taller people in front of the other
    //
    // The long and explicit comparison can be simplified to:
    // ```rust people.sort_unstable_by_key(|x| (x[0], x[1]));```
    people.sort_unstable_by(|a, b| match a[0].cmp(&b[0]) {
        Ordering::Less => Ordering::Less,
        Ordering::Greater => Ordering::Greater,
        Ordering::Equal => a[1].cmp(&b[1]),
    });


    let mut answer = vec![vec![]; people.len()];
    for person in people {
        let h = person[0];
        let mut k = person[1];

        for idx in 0..answer.len() {
            // If k == 0, then we must put the person in the first
            // available (i.e. empty) position
            if k == 0 && answer[idx].is_empty() {
                answer[idx] = person;
                break;
            }

            // If K>0, we have to skip over K either empty positions,
            // or ones with taller persons. The ones with shorter 
            // persons are ignored
            if answer[idx].is_empty() || answer[idx][0] >= h {
                k -= 1;
            }
        }
    }

    answer
}
```

### Sorting #2

Here we take the opposite approach and star with the tallest person.

Although both solutions are quadratic, this one runs much faster in the online
judge.

```rust
pub fn reconstruct_queue(mut people: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
    // Put taller persons first. If two people have the same height,
    // then put the one with smaller K in front of the other
    people.sort_unstable_by_key(|x| (-x[0], x[1]));

    let mut answer = Vec::with_capacity(people.len());
    for person in people {
        answer.insert(person[1] as usize, person);
    }

    answer
}
```
