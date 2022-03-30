# [1845. Seat Reservation Manager](https://leetcode.com/problems/seat-reservation-manager/)

## Problem

### Description

Design a system that manages the reservation state of `n` seats that are
numbered from `1` to `n`.

Implement the `SeatManager` class:

* `SeatManager(int n)` Initializes a `SeatManager` object that will manage `n`
  seats numbered from `1` to `n`. All seats are initially available.
* `int reserve()` Fetches the smallest-numbered unreserved seat, reserves it,
  and returns its number.
* `void unreserve(int seatNumber)` Unreserves the seat with the
  given `seatNumber`.

### Constraints

* `1 <= n <= 10^5`
* `1 <= seatNumber <= n`
* For each call to `reserve`, it is guaranteed that there will be at least one
  unreserved seat.
* For each call to `unreserve`, it is guaranteed that `seatNumber` will be
  reserved.
* At most `10^5` calls in total will be made to `reserve` and `unreserve`.

### Examples

```text
Input
["SeatManager", "reserve", "reserve", "unreserve", "reserve", "reserve", "reserve", "reserve", "unreserve"]
[[5], [], [], [2], [], [], [], [], [5]]
Output
[null, 1, 2, null, 2, 3, 4, 5, null]

Explanation
SeatManager seatManager = new SeatManager(5); // Initializes a SeatManager with 5 seats.
seatManager.reserve();    // All seats are available, so return the lowest numbered seat, which is 1.
seatManager.reserve();    // The available seats are [2,3,4,5], so return the lowest of them, which is 2.
seatManager.unreserve(2); // Unreserve seat 2, so now the available seats are [2,3,4,5].
seatManager.reserve();    // The available seats are [2,3,4,5], so return the lowest of them, which is 2.
seatManager.reserve();    // The available seats are [3,4,5], so return the lowest of them, which is 3.
seatManager.reserve();    // The available seats are [4,5], so return the lowest of them, which is 4.
seatManager.reserve();    // The only available seat is seat 5, so return 5.
seatManager.unreserve(5); // Unreserve seat 5, so now the available seats are [5].
```

## Solutions

#### Observations

* We must always `reserve` the smallest number, which means that the data must
  be somehow stored in sorted order
* If we store each number individually for instance in a vector then:
    * It will take a lot of memory
    * The `reserve()` method will take `O(n)` time to find the smallest
      unreserved number and the `unreserve()` can be implemented in `O(1)` time
      via direct indexing
    * Another option is to implement `reserve()` via `vec.pop()` which would
      give us `O(1)` time complexity, but then the `unreserve()` method would
      take `O(n)` time
* We can store the numbers in a tree
    * It will still consume a lot of memory
    * But it will give us `O(log n)` implementations of `reserve()`
      and `unreserve()`
* A better option is to store ranges instead of individual seats
    * It will use less memory
    * We can split & merge the ranges in order to avoid fragmentation and thus
      reduce the memory usage

### Using a BTreeSet storing ranges

```rust
use std::collections::BTreeSet;

struct SeatManager {
    free: BTreeSet<(i32, i32)>,
}

impl SeatManager {
    fn new(n: i32) -> Self {
        assert!(n > 0);

        let mut free = BTreeSet::new();
        free.insert((1, n));

        Self { free }
    }

    fn reserve(&mut self) -> i32 {
        // This can be achieved more elegantly and efficiently with the unstable `.first()`/`.pop_first()` methods
        let &min_range @ (from, to) = self.free.iter().next().unwrap();
        self.free.remove(&min_range);

        let reserved = from;
        if to - from > 0 {
            self.free.insert((from + 1, to));
        }

        reserved
    }

    fn unreserve(&mut self, seat_number: i32) {
        let mut range = (seat_number, seat_number);

        // find the range smaller than the key if it exists
        if let Some(&entry @ (from, to)) = self.free.range(..range).next_back() {
            // If this is the next value, then merge the two ranges
            if seat_number == to + 1 {
                self.free.remove(&entry);
                range.0 = from;
            }
        }

        // find the range greater than the key if it exists
        if let Some(&entry @ (from, to)) = self.free.range(range..).next() {
            // If this is the next value, then merge the two ranges
            if seat_number + 1 == from {
                self.free.remove(&entry);
                range.1 = to;
            }
        }

        self.free.insert(range);
    }
}
```

## Related Problems

* [56. Merge Intervals](/leetcode/000%20-%20099/56%20-%20Merge%20Intervals.md)