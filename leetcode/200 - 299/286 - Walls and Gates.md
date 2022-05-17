# [286. Walls and Gates](https://leetcode.com/problems/walls-and-gates)

## Problem

### Description

You are given a `m x n` 2D grid initialized with these three possible values.

* `-1` - A wall or an obstacle.
* `0` - A gate.
* `INF` - Infinity means an empty room. We use the value `2^31 - 1 = 2147483647`
  to represent `INF` as you may assume that the distance to a gate is less
  than `2147483647`.

Fill each empty room with the distance to its nearest gate. If it is impossible
to reach a Gate, that room should remain filled with `INF`

### Examples

```text
Input: 
INF  -1  0  INF
INF INF INF  -1
INF  -1 INF  -1
  0  -1 INF INF
  
Output:
  3  -1   0   1
  2   2   1  -1
  1  -1   2  -1
  0  -1   3   4
```

## Solutions

### DFS

```java
class Solution {
    // static final int WALL = -1;
    static final int GATE = 0;
    // static final int INF = Integer.MAX_VALUE;

    public void wallsAndGates(int[][] rooms) {
        for (int r = 0; r < rooms.length; r++) {
            for (int c = 0; c < rooms[r].length; c++) {
                if (rooms[r][c] == GATE) {
                    dfs(rooms, r, c, 0);
                }
            }
        }
    }

    void dfs(int[][] rooms, int r, int c, int dist) {
        // check if we are outside the grid
        if (r < 0 || c < 0 || r >= rooms.length || c >= rooms[r].length) {
            return;
        }

        // the cell is either a WALL(-1) or there is a closer gate
        if (rooms[r][c] < dist) {
            return;
        }

        rooms[r][c] = dist;

        dfs(rooms, r - 1, c, dist + 1);
        dfs(rooms, r, c - 1, dist + 1);
        dfs(rooms, r, c + 1, dist + 1);
        dfs(rooms, r + 1, c, dist + 1);
    }
}
```

## Related Problems

* [994. Rotting Oranges](/900%20-%20999/994%20-%20Rotting%20Oranges.md)

## Resources:

* At LeetCode (premium): [286. Walls and Gates](https://leetcode.com/problems/walls-and-gates)
* At LintCode (free): [663. Walls and Gates](https://www.lintcode.com/problem/663/description)
