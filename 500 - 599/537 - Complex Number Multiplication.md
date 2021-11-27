# [537. Complex Number Multiplication](https://leetcode.com/problems/complex-number-multiplication/)

## Problem

A complex number can be represented as a string on the form "real+imaginaryi"
where:

* `real` is the real part and is an integer in the range `[-100, 100]`.
* `imaginary` is the imaginary part and is an integer in the range `[-100, 100]`
  .
* `i^2 == -1`.

Given two complex numbers `num1` and `num2` as strings, return a string of the
complex number that represents their multiplications.

#### Constraints

* `num1` and `num2` are valid complex numbers.

#### Examples

```text
Input: num1 = "1+1i", num2 = "1+1i"
Output: "0+2i"
```

> Explanation: (1 + i) * (1 + i) = 1 + i2 + 2 * i = 2i, and you need convert
> it to the form of 0+2i.

```text
Input: num1 = "1+-1i", num2 = "1+-1i"
Output: "0+-2i"
```

> Explanation: (1 - i) * (1 - i) = 1 + i2 - 2 * i = -2i, and you need convert
> it to the form of 0+-2i.

## Solution

```rust
use std::fmt::{Display, Formatter};
use std::ops::Mul;
use std::str::FromStr;

pub fn complex_number_multiply(num1: String, num2: String) -> String {
    let a: Complex = num1.parse().unwrap();
    let b: Complex = num2.parse().unwrap();
    let r = a * b;
    format!("{}", r)
}


#[derive(Debug, Copy, Clone, Eq, PartialEq)]
struct Complex {
    real: i32,
    imag: i32,
}

impl FromStr for Complex {
    type Err = &'static str;

    fn from_str(s: &str) -> Result<Self, Self::Err> {
        match s.find('+') {
            None => Err("missing '+'"),
            Some(idx) => {
                let r = s[..idx].parse().unwrap();
                let i = s[idx + 1..s.len() - 1].parse().unwrap();

                Ok(Complex { real: r, imag: i })
            }
        }
    }
}

impl Display for Complex {
    fn fmt(&self, f: &mut Formatter<'_>) -> std::fmt::Result {
        f.write_fmt(format_args!("{}+{}i", self.real, self.imag))
    }
}

impl Mul for Complex {
    type Output = Complex;

    fn mul(self, rhs: Self) -> Self::Output {
        let real = self.real * rhs.real - self.imag * rhs.imag;
        let imag = self.real * rhs.imag + self.imag * rhs.real;
        Complex { real, imag }
    }
}
```
