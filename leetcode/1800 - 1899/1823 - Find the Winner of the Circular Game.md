# [1823. Find the Winner of the Circular Game](https://leetcode.com/problems/find-the-winner-of-the-circular-game/)

## Problem

### Description

There are `n` friends that are playing a game. The friends are sitting in a
circle and are numbered from `1` to `n` in clockwise order. More formally,
moving clockwise from the `i`th friend brings you to the `(i+1)`th friend
for `1 <= i < n`, and moving clockwise from the `n`th friend brings you to
the `1`st friend.

The rules of the game are as follows:

* Start at the 1st friend.
* Count the next `k` friends in the clockwise direction including the friend you
  started at. The counting wraps around the circle and may count some friends
  more than once.
* The last friend you counted leaves the circle and loses the game.
* If there is still more than one friend in the circle, go back to step 2
  starting from the friend immediately clockwise of the friend who just lost and
  repeat.
* Else, the last friend in the circle wins the game.

Given the number of friends, `n`, and an integer `k`, return the winner of the
game.

### Constraints

* `1 <= k <= n <= 500`

### Examples

#### Example 1

![image](resources/1823/ex1.png)

```text
Input: n = 5, k = 2
Output: 3
Explanation: Here are the steps of the game:
1) Start at friend 1.
2) Count 2 friends clockwise, which are friends 1 and 2.
3) Friend 2 leaves the circle. Next start is friend 3.
4) Count 2 friends clockwise, which are friends 3 and 4.
5) Friend 4 leaves the circle. Next start is friend 5.
6) Count 2 friends clockwise, which are friends 5 and 1.
7) Friend 1 leaves the circle. Next start is friend 3.
8) Count 2 friends clockwise, which are friends 3 and 5.
9) Friend 5 leaves the circle. Only friend 3 is left, so they are the winner.
```

#### Example 2

```text
Input: n = 6, k = 5
Output: 1
Explanation: The friends leave in this order: 5, 4, 6, 2, 3. The winner is friend 1.
```

## Solutions

### Simulation #1

We can allocate a vector, where for each `i` `player[i]` points to the next
player: `player[i] = i + 1` (note: the last element wraps to the first one).

Once a player is removed, we simply update the value. Let's say
that `player[i] = j` and `j` is removed from the game, this means that we have
to update the array to point to the correct next player: `player[i] = player[j]`

I.e. we've built a circular singly linked list, by using indexes instead of
pointers

```rust
pub fn find_the_winner(n: i32, k: i32) -> i32 {
    assert!(1 <= k);
    assert!(k <= n);
    assert!(n <= 500);

    let mut alive = n as usize;
    let mut players = (0..=n).map(|x| x + 1).collect::<Vec<_>>();
    // the last one points to the 1st one (not the 0th!)
    players[n as usize] = 1;

    let mut previous = n as usize;
    let mut to_delete = players[previous] as usize;

    while alive > 1 {
        for _ in 0..k - 1 {
            previous = players[previous] as usize;
            to_delete = players[to_delete] as usize;
        }

        players[previous] = players[to_delete];
        to_delete = players[to_delete] as usize;

        alive -= 1;
    }

    previous as i32
}
```

### Simulation #2

We can simplify the above solution by keeping track only of the `previous`
player, realizing that `to_delete` is a function of `previous` (
i.e. `to_delete = player[previous]`, in other words, it's always one step
ahead):

```rust
pub fn find_the_winner(n: i32, k: i32) -> i32 {
    assert!(1 <= k);
    assert!(k <= n);
    assert!(n <= 500);

    let mut alive = n as usize;
    let mut players = (0..=n).map(|x| x + 1).collect::<Vec<_>>();
    // the last one points to the 1st one (not the 0th!)
    players[n as usize] = 1;

    let mut previous = n as usize;

    while alive > 1 {
        for _ in 0..k - 1 {
            previous = players[previous] as usize;
        }

        players[previous] = players[players[previous] as usize];
        alive -= 1;
    }

    previous as i32
}
```

## Resources

* [Josephus problem @ Wikipedia](https://en.wikipedia.org/wiki/Josephus_problem)
