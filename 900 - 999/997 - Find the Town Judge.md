# [997. Find the Town Judge](https://leetcode.com/problems/find-the-town-judge/)

## Problem

In a town, there are `n` people labeled from `1` to `n`. There is a rumor that one of these people is secretly the town
judge.

If the town judge exists, then:

1. The town judge trusts nobody.
2. Everybody (except for the town judge) trusts the town judge.
3. There is exactly one person that satisfies properties `1` and `2`.

You are given an array trust where `trust[i] = [ai, bi]` representing that the person labeled `ai` trusts the person
labeled `bi`.

Return the label of the town judge if the town judge exists and can be identified, or return `-1` otherwise.

#### Constraints

* `1 <= n <= 1000`
* `0 <= trust.length <= 10^4`
* `trust[i].length == 2`
* All the pairs of `trust` are unique.
* `ai != bi`
* `1 <= ai, bi <= n`

#### Examples

```text
Input: n = 2, trust = [[1,2]]
Output: 2
```

```text
Input: n = 3, trust = [[1,3],[2,3]]
Output: 3
```

```text
Input: n = 3, trust = [[1,3],[2,3],[3,1]]
Output: -1
```

## Solutions

### Using a HashMap

```rust
use std::collections::HashSet;

pub fn find_judge(n: i32, trust: Vec<Vec<i32>>) -> i32 {
    let mut trusting = HashSet::new();

    let mut judge_id = 0;
    for x in trust.iter() {
        if trusting.insert(x[0]) {
            judge_id ^= x[0];
        }
    }

    if trusting.len() != n as usize - 1 {
        // Everybody must trust the town judge, except for the judge himself, thus
        // we need to have "N-1" people who trust him. If there are more than N-1,
        // then there is no judge;
        //
        // If there are less people who exhibit "trust", then there i sno judge,
        // because everyone must trust him
        return -1;
    }

    // There is only one person ID, so we can apply the XOR trick to find its value
    for x in 1..=n {
        judge_id ^= x;
    }

    // remove all people from the list, who trust the judge. If the list is empty -> then everyone trusts him
    for x in trust.iter() {
        if x[1] == judge_id {
            trusting.remove(&x[0]);
        }
    }

    if !trusting.is_empty() {
        return -1;
    }

    judge_id
}
```

### Using an array/vector

```rust
pub fn find_judge(n: i32, trust: Vec<Vec<i32>>) -> i32 {
    let mut people = vec![0; n as usize];

    for x in trust.iter() {
        // The judge does not trust anyone, so if someone is
        // trusting someone else, then he can never reach N-1
        people[(x[0] - 1) as usize] -= 1;

        // Because all pairs are unique, if someone reaches N-1,
        // then he is the judge
        people[(x[1] - 1) as usize] += 1;
    }

    let judges = people
        .iter()
        .copied()
        .enumerate()
        .filter(|&(_, v)| v == n - 1)
        .map(|(idx, _)| idx + 1)
        .collect::<Vec<_>>();

    if judges.len() != 1 {
        return -1;
    }

    judges[0] as i32
}
```