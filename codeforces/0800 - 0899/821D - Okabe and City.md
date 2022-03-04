# [821D - Okabe and City](https://codeforces.com/problemset/problem/821/D)

## Problem

* time limit per test: 4 seconds
* memory limit per test: 256 megabytes

### Description

Okabe likes to be able to walk through his city on a path lit by street lamps.
That way, he doesn't get beaten up by schoolchildren.

Okabe's city is represented by a 2D grid of cells. Rows are numbered from `1`
to `n` from top to bottom, and columns are numbered `1` to `m` from left to
right. Exactly `k` cells in the city are lit by a street lamp. It's guaranteed
that the top-left cell is lit.

Okabe starts his walk from the top-left cell, and wants to reach the
bottom-right cell. Of course, Okabe will only walk on lit cells, and he can only
move to adjacent cells in the up, down, left, and right directions. However,
Okabe can also temporarily light all the cells in any single row or column at a
time if he pays 1 coin, allowing him to walk through some cells not lit
initially.

Note that Okabe can only light a single row or column at a time, and has to pay
a coin every time he lights a new row or column. To change the row or column
that is temporarily lit, he must stand at a cell that is lit initially. Also,
once he removes his temporary light from a row or column, all cells in that
row/column not initially lit are now not lit.

Help Okabe find the minimum number of coins he needs to pay to complete his
walk!

### Input

The first line of input contains three space-separated integers `n`, `m`,
and `k` (`2 ≤ n, m, k ≤ 10^4`).

Each of the next `k` lines contains two space-separated integers `ri`
and `ci` (`1 ≤ ri ≤ n, 1 ≤ ci ≤ m`) — the row and the column of the `i`-th lit
cell.

It is guaranteed that all `k` lit cells are distinct. It is guaranteed that the
top-left cell is lit.

### Output

Print the minimum number of coins Okabe needs to pay to complete his walk,
or `-1` if it's not possible.

### Examples

#### Example 1

```text
4 4 5
1 1
2 1
2 3
3 3
4 3
```

Output: `2`

#### Example 2

```text
5 5 4
1 1
2 1
3 1
3 2
```

Output: `-1`

#### Example 3

```text
2 2 4
1 1
1 2
2 1
2 2
```

Output: `0`

#### Example 4

```text
5 5 4
1 1
2 2
3 3
4 4
```

Output: `3`

## Solutions

### Dijkstra + HashSet (memory exceeded)

This solution fails one of the tests because it consumes too much memory. The
grid has a size of `10^4 x 10^4` which is 100MB. Combined with a `HashSet` for
the visited items it exceeds the limit of 256MB.

```rust
use std::cmp::Ordering;
use std::collections::{BinaryHeap, HashSet};
use std::error::Error;
use std::io::BufRead;
use std::io::Write;

#[derive(Debug, Copy, Clone, Eq, PartialEq, Hash)]
enum Light {
    Off,
    Col(u16),
    Row(u16),
}

#[derive(Debug)]
struct State(i16, u16, u16, Light);

impl PartialEq for State {
    fn eq(&self, other: &Self) -> bool {
        self.0 == other.0
    }
}

impl Eq for State {}

impl PartialOrd for State {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

impl Ord for State {
    fn cmp(&self, other: &Self) -> Ordering {
        self.0.cmp(&other.0).reverse()
    }
}

fn main() -> Result<(), Box<dyn Error>> {
    let stdin = std::io::stdin();
    let mut stdin = stdin.lock();

    let stdout = std::io::stdout();
    let mut stdout = stdout.lock();

    let mut buffer = String::with_capacity(16);

    stdin.read_line(&mut buffer)?;
    let mut nmk = buffer.split_ascii_whitespace();
    let n = nmk
        .next()
        .ok_or(format!("invalid input (n): {:?}", buffer))?
        .parse::<usize>()?;
    let m = nmk
        .next()
        .ok_or(format!("invalid input (m): {:?}", buffer))?
        .parse::<usize>()?;
    let mut k = nmk
        .next()
        .ok_or(format!("invalid input (k): {:?}", buffer))?
        .parse::<usize>()?;
    drop(nmk);

    let mut grid = vec![vec![false; m]; n];
    grid[0][0] = true;

    buffer.clear();
    while stdin.read_line(&mut buffer)? > 0 {
        if k == 0 {
            return Err("unexpected number of lit cells".into());
        }
        k -= 1;

        let mut rc = buffer.split_ascii_whitespace();

        let r = rc
            .next()
            .ok_or("invalid input: missing row")?
            .parse::<usize>()?;
        if r == 0 || r > n {
            return Err(format!("invalid row: {}, N={}", r, n).into());
        }

        let c = rc
            .next()
            .ok_or("invalid input: missing col")?
            .parse::<usize>()?;
        if c == 0 || c > m {
            return Err(format!("invalid col: {}, M={}", c, m).into());
        }

        grid[r - 1][c - 1] = true;
        buffer.clear();
    }

    let mut answer = -1;
    let mut visited = HashSet::new();
    let mut pq = BinaryHeap::new();
    pq.push(State(0, 0, 0, Light::Off));

    while let Some(State(cost, r, c, l)) = pq.pop() {
        if !visited.insert((r, c, l)) {
            continue;
        }

        let ru = r as usize;
        let cu = c as usize;

        if ru == n - 1 && cu == m - 1 {
            answer = cost;
            break;
        }

        // above
        if r > 0 {
            if grid[ru - 1][cu] || l == Light::Col(c) || l == Light::Row(r - 1) {
                pq.push(State(cost, r - 1, c, l));
            } else {
                if grid[ru][cu] {
                    pq.push(State(cost + 1, r - 1, c, Light::Row(r - 1)));
                    pq.push(State(cost + 1, r - 1, c, Light::Col(c)));
                }
            }
        }

        // left
        if c > 0 {
            if grid[ru][cu - 1] || l == Light::Col(c - 1) || l == Light::Row(r) {
                pq.push(State(cost, r, c - 1, l));
            } else {
                if grid[ru][cu] {
                    pq.push(State(cost + 1, r, c - 1, Light::Row(r)));
                    pq.push(State(cost + 1, r, c - 1, Light::Col(c - 1)));
                }
            }
        }

        // right
        if cu < m - 1 {
            if grid[ru][cu + 1] || l == Light::Col(c + 1) || l == Light::Row(r) {
                pq.push(State(cost, r, c + 1, l));
            } else {
                if grid[ru][cu] {
                    pq.push(State(cost + 1, r, c + 1, Light::Row(r)));
                    pq.push(State(cost + 1, r, c + 1, Light::Col(c + 1)));
                }
            }
        }

        // below
        if ru < n - 1 {
            if grid[ru + 1][cu] || l == Light::Col(c) || l == Light::Row(r + 1) {
                pq.push(State(cost, r + 1, c, l));
            } else {
                if grid[ru][cu] {
                    pq.push(State(cost + 1, r + 1, c, Light::Row(r + 1)));
                    pq.push(State(cost + 1, r + 1, c, Light::Col(c)));
                }
            }
        }
    }

    writeln!(stdout, "{}", answer)?;
    Ok(())
}
```

### Dijkstra + inline marking of visited nodes (time limit exceeded)

Instead of a `HashSet` here we use each cell of the frid as a bitfield in which
we store whether we have already visited it or not.

```rust
use std::cmp::Ordering;
use std::collections::BinaryHeap;
use std::error::Error;
use std::io::BufRead;
use std::io::Write;

const SHIFT_LAMP_VISITED: u32 = 1;
const SHIFT_LIGHT_ROW: u32 = 2;
const SHIFT_LIGHT_COL: u32 = 3;

#[derive(Debug, Copy, Clone, Eq, PartialEq, Hash)]
enum Light {
    Off,
    Col(u16),
    Row(u16),
}

#[derive(Debug)]
struct State(i16, u16, u16, Light);

impl PartialEq for State {
    fn eq(&self, other: &Self) -> bool {
        self.0 == other.0
    }
}

impl Eq for State {}

impl PartialOrd for State {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

impl Ord for State {
    fn cmp(&self, other: &Self) -> Ordering {
        self.0.cmp(&other.0).reverse()
    }
}

fn main() -> Result<(), Box<dyn Error>> {
    let stdin = std::io::stdin();
    let mut stdin = stdin.lock();

    let stdout = std::io::stdout();
    let mut stdout = stdout.lock();

    let mut buffer = String::with_capacity(16);

    stdin.read_line(&mut buffer)?;
    let mut nmk = buffer.split_ascii_whitespace();
    let n = nmk
        .next()
        .ok_or(format!("invalid input (n): {:?}", buffer))?
        .parse::<usize>()?;
    let m = nmk
        .next()
        .ok_or(format!("invalid input (m): {:?}", buffer))?
        .parse::<usize>()?;
    let mut k = nmk
        .next()
        .ok_or(format!("invalid input (k): {:?}", buffer))?
        .parse::<usize>()?;
    drop(nmk);

    let mut grid = vec![vec![0u8; m]; n];
    grid[0][0] = 1;

    buffer.clear();
    while stdin.read_line(&mut buffer)? > 0 {
        if k == 0 {
            return Err("unexpected number of lit cells".into());
        }
        k -= 1;

        let mut rc = buffer.split_ascii_whitespace();

        let r = rc
            .next()
            .ok_or("invalid input: missing row")?
            .parse::<usize>()?;
        if r == 0 || r > n {
            return Err(format!("invalid row: {}, N={}", r, n).into());
        }

        let c = rc
            .next()
            .ok_or("invalid input: missing col")?
            .parse::<usize>()?;
        if c == 0 || c > m {
            return Err(format!("invalid col: {}, M={}", c, m).into());
        }

        grid[r - 1][c - 1] = 1;
        buffer.clear();
    }

    let mut answer = -1;
    let mut pq = BinaryHeap::new();
    pq.push(State(0, 0, 0, Light::Off));

    while let Some(State(cost, r, c, l)) = pq.pop() {
        let ru = r as usize;
        let cu = c as usize;

        if grid[ru][cu] & 1 != 0 {
            // we are on an always lit cell
            if grid[ru][cu] & 1 << SHIFT_LAMP_VISITED != 0 {
                continue;
            }
            grid[ru][cu] |= 1 << SHIFT_LAMP_VISITED;
        } else {
            match l {
                Light::Off => unreachable!(),
                Light::Col(_) => {
                    if grid[ru][cu] & 1 << SHIFT_LIGHT_COL != 0 {
                        continue;
                    }
                    grid[ru][cu] |= 1 << SHIFT_LIGHT_COL;
                }
                Light::Row(_) => {
                    if grid[ru][cu] & 1 << SHIFT_LIGHT_ROW != 0 {
                        continue;
                    }
                    grid[ru][cu] |= 1 << SHIFT_LIGHT_ROW;
                }
            }
        }

        if ru == n - 1 && cu == m - 1 {
            answer = cost;
            break;
        }

        // above
        if r > 0 {
            if grid[ru - 1][cu] & 1 != 0 || l == Light::Col(c) || l == Light::Row(r - 1) {
                pq.push(State(cost, r - 1, c, l));
            } else {
                if grid[ru][cu] & 1 != 0 {
                    pq.push(State(cost + 1, r - 1, c, Light::Row(r - 1)));
                    pq.push(State(cost + 1, r - 1, c, Light::Col(c)));
                }
            }
        }

        // left
        if c > 0 {
            if grid[ru][cu - 1] & 1 != 0 || l == Light::Col(c - 1) || l == Light::Row(r) {
                pq.push(State(cost, r, c - 1, l));
            } else {
                if grid[ru][cu] & 1 != 0 {
                    pq.push(State(cost + 1, r, c - 1, Light::Row(r)));
                    pq.push(State(cost + 1, r, c - 1, Light::Col(c - 1)));
                }
            }
        }

        // right
        if cu < m - 1 {
            if grid[ru][cu + 1] & 1 != 0 || l == Light::Col(c + 1) || l == Light::Row(r) {
                pq.push(State(cost, r, c + 1, l));
            } else {
                if grid[ru][cu] & 1 != 0 {
                    pq.push(State(cost + 1, r, c + 1, Light::Row(r)));
                    pq.push(State(cost + 1, r, c + 1, Light::Col(c + 1)));
                }
            }
        }

        // below
        if ru < n - 1 {
            if grid[ru + 1][cu] & 1 != 0 || l == Light::Col(c) || l == Light::Row(r + 1) {
                pq.push(State(cost, r + 1, c, l));
            } else {
                if grid[ru][cu] & 1 != 0 {
                    pq.push(State(cost + 1, r + 1, c, Light::Row(r + 1)));
                    pq.push(State(cost + 1, r + 1, c, Light::Col(c)));
                }
            }
        }
    }

    writeln!(stdout, "{}", answer)?;
    Ok(())
}
```

### Dijkstra + inline marking of visited nodes (time limit exceeded)

This is an alternative implementation of the previous version which strives to
push as few elements in the queue as possible

```rust
use std::cmp::Ordering;
use std::collections::BinaryHeap;
use std::error::Error;
use std::io::BufRead;
use std::io::Write;

const SHIFT_LAMP_VISITED: u32 = 1;
const SHIFT_LIGHT_ROW: u32 = 2;
const SHIFT_LIGHT_COL: u32 = 3;

#[derive(Debug, Copy, Clone, Eq, PartialEq)]
enum N {
    Up,
    Down,
    Left,
    Right,
}

#[derive(Debug, Copy, Clone, Eq, PartialEq, Hash)]
enum Light {
    Off,
    Col(u16),
    Row(u16),
}

#[derive(Debug, Copy, Clone)]
struct State(i16, u16, u16, Light);

impl PartialEq for State {
    fn eq(&self, other: &Self) -> bool {
        self.0 == other.0
    }
}

impl Eq for State {}

impl PartialOrd for State {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
    }
}

impl Ord for State {
    fn cmp(&self, other: &Self) -> Ordering {
        self.0.cmp(&other.0).reverse()
    }
}

fn main() -> Result<(), Box<dyn Error>> {
    let stdin = std::io::stdin();
    let mut stdin = stdin.lock();

    let stdout = std::io::stdout();
    let mut stdout = stdout.lock();

    let mut buffer = String::with_capacity(16);

    stdin.read_line(&mut buffer)?;
    let mut nmk = buffer.split_ascii_whitespace();
    let n = nmk
        .next()
        .ok_or(format!("invalid input (n): {:?}", buffer))?
        .parse::<usize>()?;
    let m = nmk
        .next()
        .ok_or(format!("invalid input (m): {:?}", buffer))?
        .parse::<usize>()?;
    let mut k = nmk
        .next()
        .ok_or(format!("invalid input (k): {:?}", buffer))?
        .parse::<usize>()?;
    drop(nmk);

    let mut grid = vec![vec![0u8; m]; n];
    grid[0][0] = 1;

    buffer.clear();
    while stdin.read_line(&mut buffer)? > 0 {
        if k == 0 {
            return Err("unexpected number of lit cells".into());
        }
        k -= 1;

        let mut rc = buffer.split_ascii_whitespace();

        let r = rc
            .next()
            .ok_or("invalid input: missing row")?
            .parse::<usize>()?;
        if r == 0 || r > n {
            return Err(format!("invalid row: {}, N={}", r, n).into());
        }

        let c = rc
            .next()
            .ok_or("invalid input: missing col")?
            .parse::<usize>()?;
        if c == 0 || c > m {
            return Err(format!("invalid col: {}, M={}", c, m).into());
        }

        grid[r - 1][c - 1] = 1;
        buffer.clear();
    }

    let mut answer = -1;
    let mut pq = BinaryHeap::new();
    pq.push(State(0, 0, 0, Light::Off));

    while let Some(state @ State(cost, r, c, l)) = pq.pop() {
        let ru = r as usize;
        let cu = c as usize;

        if grid[ru][cu] & 1 != 0 {
            // we are on an always lit cell
            if grid[ru][cu] & 1 << SHIFT_LAMP_VISITED != 0 {
                continue;
            }
            grid[ru][cu] |= 1 << SHIFT_LAMP_VISITED;
        } else {
            match l {
                Light::Off => unreachable!(),
                Light::Col(_) => {
                    if grid[ru][cu] & 1 << SHIFT_LIGHT_COL != 0 {
                        continue;
                    }
                    grid[ru][cu] |= 1 << SHIFT_LIGHT_COL;
                }
                Light::Row(_) => {
                    if grid[ru][cu] & 1 << SHIFT_LIGHT_ROW != 0 {
                        continue;
                    }
                    grid[ru][cu] |= 1 << SHIFT_LIGHT_ROW;
                }
            }
        }

        if ru == n - 1 && cu == m - 1 {
            answer = cost;
            break;
        }

        on_cell(&mut grid, &mut pq, state, N::Up);
        on_cell(&mut grid, &mut pq, state, N::Left);
        on_cell(&mut grid, &mut pq, state, N::Right);
        on_cell(&mut grid, &mut pq, state, N::Down);
    }

    writeln!(stdout, "{}", answer)?;
    Ok(())
}

fn on_cell(grid: &mut Vec<Vec<u8>>, pq: &mut BinaryHeap<State>, state: State, dir: N) {
    let mut r = state.1 as usize;
    let mut c = state.2 as usize;

    let coming_from_lamp = grid[r][c] & 1 != 0;

    match dir {
        N::Up => {
            if r == 0 {
                return;
            }

            r -= 1;
        }
        N::Down => {
            if r >= grid.len() - 1 {
                return;
            }

            r += 1;
        }
        N::Left => {
            if c == 0 {
                return;
            }

            c -= 1;
        }
        N::Right => {
            if c >= grid[r].len() - 1 {
                return;
            }

            c += 1;
        }
    }

    if grid[r][c] & 1 != 0 {
        if grid[r][c] & 1 << SHIFT_LAMP_VISITED == 0 {
            pq.push(State(state.0, r as u16, c as u16, state.3));
        }

        return;
    }

    if state.3 == Light::Col(c as u16) && state.2 == c as u16 {
        if grid[r][c] & 1 << SHIFT_LIGHT_COL == 0 {
            pq.push(State(state.0, r as u16, c as u16, state.3));
        }
    }

    if state.3 == Light::Row(r as u16) && state.1 == r as u16 {
        if grid[r][c] & 1 << SHIFT_LIGHT_ROW == 0 {
            pq.push(State(state.0, r as u16, c as u16, state.3));
        }
    }

    if coming_from_lamp {
        if grid[r][c] & 1 << SHIFT_LIGHT_COL == 0 {
            if state.3 != Light::Col(c as u16) || state.2 != c as u16 {
                pq.push(State(state.0 + 1, r as u16, c as u16, Light::Col(c as u16)));
            }
        }
        if grid[r][c] & 1 << SHIFT_LIGHT_ROW == 0 {
            if state.3 != Light::Row(r as u16) || state.1 != r as u16 {
                pq.push(State(state.0 + 1, r as u16, c as u16, Light::Row(r as u16)));
            }
        }
    }
}
```