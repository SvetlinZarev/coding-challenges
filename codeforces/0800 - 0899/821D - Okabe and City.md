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

## Utils

### Board generator

```rust
use std::collections::HashSet;
use std::io::Write;

use rand::Rng; // rand 0.8.5

const CELLS: u16 = 1000;
const ROWS: u16 = 1000;
const COLS: u16 = 1000;

fn main() {
    let args = std::env::args().collect::<Vec<String>>();

    let rows = args
        .get(1)
        .map(|x| x.parse::<u16>().unwrap())
        .unwrap_or(ROWS);

    let cols = args
        .get(2)
        .map(|x| x.parse::<u16>().unwrap())
        .unwrap_or(COLS);

    let cells = args
        .get(3)
        .map(|x| x.parse::<u16>().unwrap())
        .unwrap_or(CELLS);

    let mut grid = HashSet::new();
    let mut rand = rand::thread_rng();

    grid.insert((1, 1));
    while grid.len() < cells as usize {
        let r = rand.gen_range(1..=rows);
        let c = rand.gen_range(1..=cols);
        grid.insert((r, c));
    }

    let out = std::io::stdout();
    let mut out = out.lock();

    writeln!(out, "{} {} {}", rows, cols, cells).unwrap();
    for (r, c) in grid {
        writeln!(out, "{} {}", r, c).unwrap();
    }
}
```

### Script to compare the two solutions

```shell
#!/bin/bash

for i in {1..10}
do
   echo "Iteration $i"
   INPUT=$(~/board 4 4 4)
   FIRST=$(echo "${INPUT}" | ~/first)
   SECOND=$(echo "${INPUT}" | ~/second)

   if [[ $FIRST != $SECOND ]]; then
     echo "First: $SECOND"
     echo "Second: $FIRST"
     echo "${INPUT}" > ~/dif-$i.txt
   else
     echo "Answer: $FIRST"
   fi
  echo "---"
done
```

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

### Dijkstra + virtual nodes

Because of the memory & time constraints it's impractical to consider every
single cell as a valid candidate. Instead, we consider only the "lit" cells.

In order to be able to navigate between the lit cells, without traversing the "
dark" cells, we assume that all cells located in neighbouring rows & columns
within a distance of `+or-2 & +or-1` are connected with a cost of 1. All direct
neighbours are connected with a cost of 0;

```rust
use std::cmp::Ordering;
use std::collections::{BinaryHeap, HashMap, HashSet};
use std::error::Error;
use std::io::BufRead;
use std::io::Write;
use std::ops::Add;

const VISITED: u8 = 0xFF;
const LIGHT: u8 = 0x01;

#[derive(Debug, Copy, Clone)]
struct State(i16, u16, u16);

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
    let mut buffer = String::with_capacity(16);

    let stdin = std::io::stdin();
    let mut stdin = stdin.lock();

    let stdout = std::io::stdout();
    let mut stdout = stdout.lock();

    stdin.read_line(&mut buffer)?;
    let mut nmk = buffer.split_ascii_whitespace();
    let rows = nmk //n
        .next()
        .ok_or(format!("invalid input (n): {:?}", buffer))?
        .parse::<usize>()?;
    let cols = nmk //m
        .next()
        .ok_or(format!("invalid input (m): {:?}", buffer))?
        .parse::<usize>()?;
    let mut k = nmk
        .next()
        .ok_or(format!("invalid input (k): {:?}", buffer))?
        .parse::<usize>()?;
    drop(nmk);

    let mut grid = vec![vec![0u8; cols]; rows];
    let mut vrow = HashMap::new();
    let mut vcol = HashMap::new();

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
        if r == 0 || r > rows {
            return Err(format!("invalid row: {}, N={}", r, rows).into());
        }

        let c = rc
            .next()
            .ok_or("invalid input: missing col")?
            .parse::<usize>()?;
        if c == 0 || c > cols {
            return Err(format!("invalid col: {}, M={}", c, cols).into());
        }

        grid[r - 1][c - 1] = 1;
        vrow.entry(r - 1).or_insert(HashSet::new()).insert(c - 1);
        vcol.entry(c - 1).or_insert(HashSet::new()).insert(r - 1);
        buffer.clear();
    }

    // add the target cell as a virtual node
    vrow.entry(rows - 1).or_default().insert(cols - 1);
    vcol.entry(cols - 1).or_default().insert(rows - 1);

    let mut answer = -1;
    let mut pq = BinaryHeap::new();
    pq.push(State(0, 0, 0));

    while let Some(State(cost, r, c)) = pq.pop() {
        let row = r as usize;
        let col = c as usize;

        if row == rows - 1 && col == cols - 1 {
            answer = cost;
            break;
        }

        if grid[row][col] == VISITED {
            continue;
        }
        grid[row][col] = VISITED;
        if let Some(rx) = vrow.get_mut(&row) {
            rx.remove(&col);
        }
        if let Some(cx) = vcol.get_mut(&col) {
            cx.remove(&row);
        }

        // direct neighbours
        if row > 0 && grid[row - 1][col] == LIGHT {
            pq.push(State(cost, r - 1, c));
        }
        if col > 0 && grid[row][col - 1] == LIGHT {
            pq.push(State(cost, r, c - 1));
        }
        if col < cols - 1 && grid[row][col + 1] == LIGHT {
            pq.push(State(cost, r, c + 1));
        }
        if row < rows - 1 && grid[row + 1][col] == LIGHT {
            pq.push(State(cost, r + 1, c));
        }

        // Neighbours with "distance <= 2", connected via virtual nodes by row
        for vr in row.saturating_sub(2)..=row.add(2).min(rows - 1) {
            if let Some(nodes) = vrow.get(&vr) {
                for vc in nodes.iter().copied() {
                    if vr == rows - 1 && vc == cols - 1 && vr - row > 1 && grid[vr][vc] != LIGHT {
                        continue;
                    }

                    pq.push(State(cost + 1, vr as u16, vc as u16));
                }
            }
        }

        // Neighbours with "distance <= 2", connected via virtual nodes by column
        for vc in col.saturating_sub(2)..=col.add(2).min(cols - 1) {
            if let Some(nodes) = vcol.get(&vc) {
                for vr in nodes.iter().copied() {
                    if vr == rows - 1 && vc == cols - 1 && vc - col > 1 && grid[vr][vc] != LIGHT {
                        continue;
                    }

                    pq.push(State(cost + 1, vr as u16, vc as u16));
                }
            }
        }
    }

    writeln!(stdout, "{}", answer)?;
    Ok(())
}
```

### Dijkstra + virtual nodes #2

We can reduce the memory usage and increase the execution speed by removing the
virtual nodes, once we've pushed them to the queue. Thus there will be much
fewer nodes to process.

```rust
use std::cmp::Ordering;
use std::collections::hash_map::Entry;
use std::collections::{BinaryHeap, HashMap, HashSet};
use std::error::Error;
use std::io::BufRead;
use std::io::Write;
use std::ops::Add;

const VISITED: u8 = 0xFF;
const LIGHT: u8 = 0x01;

#[derive(Debug, Copy, Clone)]
struct State(i16, u16, u16);

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
    let mut buffer = String::with_capacity(16);

    let stdin = std::io::stdin();
    let mut stdin = stdin.lock();

    let stdout = std::io::stdout();
    let mut stdout = stdout.lock();

    stdin.read_line(&mut buffer)?;
    let mut nmk = buffer.split_ascii_whitespace();
    let rows = nmk //n
        .next()
        .ok_or(format!("invalid input (n): {:?}", buffer))?
        .parse::<usize>()?;
    let cols = nmk //m
        .next()
        .ok_or(format!("invalid input (m): {:?}", buffer))?
        .parse::<usize>()?;
    let mut k = nmk
        .next()
        .ok_or(format!("invalid input (k): {:?}", buffer))?
        .parse::<usize>()?;
    drop(nmk);

    let mut grid = vec![vec![0u8; cols]; rows];
    let mut vrow = HashMap::new();
    let mut vcol = HashMap::new();

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
        if r == 0 || r > rows {
            return Err(format!("invalid row: {}, N={}", r, rows).into());
        }

        let c = rc
            .next()
            .ok_or("invalid input: missing col")?
            .parse::<usize>()?;
        if c == 0 || c > cols {
            return Err(format!("invalid col: {}, M={}", c, cols).into());
        }

        grid[r - 1][c - 1] = 1;
        vrow.entry(r - 1).or_insert(HashSet::new()).insert(c - 1);
        vcol.entry(c - 1).or_insert(HashSet::new()).insert(r - 1);
        buffer.clear();
    }

    // add the target cell as a virtual node
    vrow.entry(rows - 1).or_default().insert(cols - 1);
    vcol.entry(cols - 1).or_default().insert(rows - 1);

    let mut answer = -1;
    let mut pq = BinaryHeap::new();
    pq.push(State(0, 0, 0));

    while let Some(State(cost, r, c)) = pq.pop() {
        let row = r as usize;
        let col = c as usize;

        if row == rows - 1 && col == cols - 1 {
            answer = cost;
            break;
        }

        if grid[row][col] == VISITED {
            continue;
        }
        grid[row][col] = VISITED;
        if let Some(rx) = vrow.get_mut(&row) {
            rx.remove(&col);
        }
        if let Some(cx) = vcol.get_mut(&col) {
            cx.remove(&row);
        }

        // direct neighbours
        if row > 0 && grid[row - 1][col] == LIGHT {
            pq.push(State(cost, r - 1, c));
        }
        if col > 0 && grid[row][col - 1] == LIGHT {
            pq.push(State(cost, r, c - 1));
        }
        if col < cols - 1 && grid[row][col + 1] == LIGHT {
            pq.push(State(cost, r, c + 1));
        }
        if row < rows - 1 && grid[row + 1][col] == LIGHT {
            pq.push(State(cost, r + 1, c));
        }

        // Neighbours with "distance <= 2", connected via virtual nodes by row
        for r in row.saturating_sub(2)..=row.add(2).min(rows - 1) {
            match vrow.entry(r) {
                Entry::Vacant(_) => continue,
                Entry::Occupied(mut e) => {
                    for c in e.get().iter().copied() {
                        // we can add the target cell only if it is lit or if it's
                        // within a distance of 1 of the current row/col
                        if r == rows - 1 && c == cols - 1 && r - row > 1 && grid[r][c] != LIGHT {
                            continue;
                        }

                        pq.push(State(cost + 1, r as u16, c as u16));
                    }

                    // Do not remove the target cell, in case it is not lit
                    if r != rows - 1 {
                        e.remove();
                    } else {
                        e.get_mut().retain(|&x_col| cols - 1 == x_col);
                    }
                }
            }
        }

        // Neighbours with "distance <= 2", connected via virtual nodes by column
        for c in col.saturating_sub(2)..=col.add(2).min(cols - 1) {
            match vcol.entry(c) {
                Entry::Vacant(_) => continue,

                Entry::Occupied(mut e) => {
                    for r in e.get().iter().copied() {
                        // we can add the target cell only if it is lit or if it's
                        // within a distance of 1 of the current row/col
                        if r == rows - 1 && c == cols - 1 && c - col > 1 && grid[r][c] != LIGHT {
                            continue;
                        }

                        pq.push(State(cost + 1, r as u16, c as u16));
                    }

                    // Do not remove the target cell, in case it is not lit
                    if c != cols - 1 {
                        e.remove();
                    } else {
                        e.get_mut().retain(|&x_row| rows - 1 == x_row);
                    }
                }
            }
        }
    }

    writeln!(stdout, "{}", answer)?;
    Ok(())
}
```