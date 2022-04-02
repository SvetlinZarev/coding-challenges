# [729. My Calendar I](https://leetcode.com/problems/my-calendar-i/)

## Problem

### Description

You are implementing a program to use as your calendar. We can add a new event
if adding the event will not cause a double booking.

A double booking happens when two events have some non-empty intersection (i.e.,
some moment is common to both events.).

The event can be represented as a pair of integers `start` and `end` that
represents a booking on the half-open interval `[start, end)`, the range of real
numbers `x` such that `start <= x < end`.

Implement the `MyCalendar` class:

* `MyCalendar()` Initializes the calendar object.
* `boolean book(int start, int end)` Returns `true` if the event can be added to
  the calendar successfully without causing a double booking. Otherwise,
  return `false` and do not add the event to the calendar.

### Constraints

* `0 <= start < end <= 10^9`
* At most 1000 calls will be made to book.

### Examples

```text
Input
["MyCalendar", "book", "book", "book"]
[[], [10, 20], [15, 25], [20, 30]]
Output
[null, true, false, true]

Explanation
MyCalendar myCalendar = new MyCalendar();
myCalendar.book(10, 20); // return True
myCalendar.book(15, 25); // return False, It can not be booked because time 15 is already booked by another event.
myCalendar.book(20, 30); // return True, The event can be booked, as the first event takes every time less than 20, but not including 20.
```

## Solutions

### Using BTreeMap

In order to be able to book an event, its interval must not overlap with the
interval of some other event. We can use a BTreeMap to get the events sorted in
ascending order by their start value.

An event is NOT overlapping with another event if and only if:

* it ends before the other event starts
* it starts after the other event ends

So we can use the `.range()` method to get all events that start before the new
event. From this iterator we are only interested in the last event, because we
know that the events in the tree are not overlapping. Thus, if the last event in
the returned range end after the current event starts, then it is overlapping
with it.

```rust
use std::collections::BTreeMap;

#[derive(Default)]
struct MyCalendar {
    inner: BTreeMap<i32, i32>,
}

impl MyCalendar {
    fn new() -> Self {
        Default::default()
    }

    fn book(&mut self, start: i32, end: i32) -> bool {
        if let Some((&_s, &e)) = self.inner.range(..end).next_back() {
            if start < e {
                return false;
            }
        }

        self.inner.insert(start, end);
        true
    }
}
```