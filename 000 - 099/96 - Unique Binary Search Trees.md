# [96. Unique Binary Search Trees](https://leetcode.com/problems/unique-binary-search-trees/)

## Problem

Given an integer `n`, return the number of structurally unique BST's (binary search trees) 
which has exactly `n` nodes of unique values from `1` to `n`.

#### Constraints

* `1 <= n <= 19`

#### Examples

```text
Input: n = 1
Output: 1
```

```text
Input: n = 3
Output: 5
```

```text
Input: n = 4
Output: 14
```

## Solutions

### Catalan numbers

```rust
pub fn num_trees(n: u64) -> u64 {
    if n <= 1 {
        return 1;
    }

    // catalan numbers formula: Product(for k=2 to n) -> (n + k)/k
    let mut product = 1;
    let mut divisor = 1;
    for k in 2..=n {
        product *= n + k;

        // the product grows too quickly, so we have to
        // try to reduce it somehow
        if product % k == 0 {
            product /= k;
        } else {
            divisor *= k;
        }
    }

    product / divisor
}
```


### Enumeration

See [95. Unique Binary Search Trees II]


### Dynamic programming

TODO


## Related problems

* [95. Unique Binary Search Trees II]


## Resources

* [Catalan numbers](https://en.wikipedia.org/wiki/Catalan_number)


[95. Unique Binary Search Trees II]: 95%20-%20Unique%20Binary%20Search%20Trees%20II.md
