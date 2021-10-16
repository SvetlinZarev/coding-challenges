# [191. Number of 1 Bits](https://leetcode.com/problems/number-of-1-bits/)

## Problem

Write a function that takes an unsigned integer and returns the number of `1`
bits it has (also known as the Hamming weight).

**Note:**

* Note that in some languages, such as Java, there is no unsigned integer type.
  In this case, the input will be given as a signed integer type. It should not
  affect your implementation, as the integer's internal binary representation is
  the same, whether it is signed or unsigned.
* In Java, the compiler represents the signed integers using 2's complement
  notation. Therefore, in Example 3, the input represents the signed integer.
  `-3`.

#### Examples

```text
Input: n = 00000000000000000000000000001011
Output: 3
Explanation: The input binary string 00000000000000000000000000001011 has a total of three '1' bits.
```

```text
Input: n = 00000000000000000000000010000000
Output: 1
Explanation: The input binary string 00000000000000000000000010000000 has a total of one '1' bit.
```

## Solutions

### Process all 32 bits

```rust
pub fn hammingWeight(n: u32) -> i32 {
    let mut n = n;
    let mut bits = 0;
    for _ in 0..32 {
        bits += n & 1;
        n = n >> 1;
    }
    bits as i32
}
```

### Process only the 1 bits

This algorithm removes the last 1 bit and thus runs proportionally to the number
of 1 bits set.

**Intuition:** in order to set a bit to `1`, for instance `100`, one must have
previously set all previous bits to `1` - `011`. Thus, when one subtracts one
from an int such as `1100` he gets `1011`, and the bitwise AND will yield `1000`
, i.e. we've just removed the least significant `1` bit

```rust
pub fn hammingWeight(n: u32) -> i32 {
    let mut n = n;
    let mut bits = 0;

    while n > 0 {
        bits += n & 1;
        n = n >> 1;
    }
    bits as i32
}
```