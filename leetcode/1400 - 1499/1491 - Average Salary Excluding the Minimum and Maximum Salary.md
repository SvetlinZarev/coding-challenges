# [1491. Average Salary Excluding the Minimum and Maximum Salary](https://leetcode.com/problems/average-salary-excluding-the-minimum-and-maximum-salary/)

## Problem

### Description

You are given an array of **unique** integers `salary` `where salary[i]` is the
salary of the `i`th employee.

Return the average salary of employees excluding the minimum and maximum salary.
Answers within `10^-5` of the actual answer will be accepted.

### Constraints

* `3 <= salary.length <= 100`
* `1000 <= salary[i] <= 10^6`
* All the integers of `salary` are unique.

### Examples

```text
Input: salary = [4000,3000,1000,2000]
Output: 2500.00000
Explanation: Minimum salary and maximum salary are 1000 and 4000 respectively.
Average salary excluding minimum and maximum salary is (2000+3000) / 2 = 2500
```

```text
Input: salary = [1000,2000,3000]
Output: 2000.00000
Explanation: Minimum salary and maximum salary are 1000 and 3000 respectively.
Average salary excluding minimum and maximum salary is (2000) / 1 = 2000
```

## Solution

```rust
pub fn average(salary: Vec<i32>) -> f64 {
    let mut min = f64::MAX;
    let mut max = f64::MIN;

    let mut avg = 0.0;
    for s in salary.iter().map(|&s| s as f64) {
        if s < min {
            min = s;
        }

        if s > max {
            max = s;
        }

        avg += s;
    }

    (avg - min - max) / (salary.len() - 2) as f64
}
```