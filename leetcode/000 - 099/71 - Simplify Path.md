# [71. Simplify Path](https://leetcode.com/problems/simplify-path/)

## Problem

### Description

Given a string `path`, which is an absolute path (starting with a slash `/`) to
a file or directory in a Unix-style file system, convert it to the simplified
canonical path.

In a Unix-style file system, a period `.` refers to the current directory, a
double period `..` refers to the directory up a level, and any multiple
consecutive slashes (i.e. `//`) are treated as a single slash `/`. For this
problem, any other format of periods such as `...` are treated as file/directory
names.

The canonical path should have the following format:

* The path starts with a single slash `/`.
* Any two directories are separated by a single slash `/`.
* The path does not end with a trailing `/`.
* The path only contains the directories on the path from the root directory to
  the target file or directory (i.e., no period `.` or double period `..`)

Return the simplified canonical path.

### Constraints

* `1 <= path.length <= 3000`
* `path` consists of English letters, digits, period `.`, slash `/` or `_`.
* `path` is a valid absolute Unix path.

### Examples

```text
Input: path = "/home/"
Output: "/home"
Explanation: Note that there is no trailing slash after the last directory name.
```

```text
Input: path = "/../"
Output: "/"
Explanation: Going one level up from the root directory is a no-op, as the root level is the highest level you can go.
```

```text
Input: path = "/home//foo/"
Output: "/home/foo"
Explanation: In the canonical path, multiple consecutive slashes are replaced by a single one.
```

## Solutions

### Using a stack

```rust
pub fn simplify_path<S: AsRef<str>>(path: S) -> String {
    let mut segments = vec![];

    for segment in path.as_ref().split('/') {
        // The split method returns an empty string in certain cases such as
        // a repeating separator, i.e. "///" will return several empty strings
        if segment.is_empty() {
            continue;
        }

        // skip patterns for current directory, because they fo not
        // do anything: "/./" is the same as "/"
        if segment == "." {
            continue;
        }

        // Go to the previous directory when we encounter the previous
        // directory pattern - "..". This is done by just popping the
        // current directory entry
        if segment == ".." {
            // There are "bad" test cases such as "/../" which would require us to
            // go even before the root directory, so do not unwrap the result in
            // order to satisfy them.
            segments.pop(); //.unwrap();
            continue;
        }

        segments.push(segment);
    }

    let mut canonical_path = String::with_capacity(path.as_ref().len());
    for segment in segments.into_iter() {
        canonical_path.push('/');
        canonical_path.push_str(segment);
    }

    if canonical_path.is_empty() {
        canonical_path.push('/');
    }

    canonical_path
}
```