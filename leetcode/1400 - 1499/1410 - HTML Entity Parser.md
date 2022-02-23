# [1410. HTML Entity Parser](https://leetcode.com/problems/html-entity-parser/)

## Problem

HTML entity parser is the parser that takes HTML code as input and replace all
the entities of the special characters by the characters itself.

The special characters and their entities for HTML are:

* Quotation Mark: the entity is `&quot;` and symbol character is `"`.
* Single Quote Mark: the entity is `&apos;` and symbol character is `'`.
* Ampersand: the entity is `&amp;` and symbol character is `&`.
* Greater Than Sign: the entity is `&gt;` and symbol character is `>`.
* Less Than Sign: the entity is `&lt;` and symbol character is `<`.
* Slash: the entity is `&frasl;` and symbol character is `/`.

Given the input text string to the HTML parser, you have to implement the entity
parser.

Return the text after replacing the entities by the special characters.

#### Constraints

* `1 <= text.length <= 10^5`
* The string may contain any possible characters out of all the 256 ASCII
  characters.

#### Examples

```text
Input: text = "&amp; is an HTML entity but &ambassador; is not."
Output: "& is an HTML entity but &ambassador; is not."
Explanation: The parser will replace the &amp; entity by &
```

```text
Input: text = "and I quote: &quot;...&quot;"
Output: "and I quote: \"...\""
```

```text
Input: text = "Stay home! Practice on Leetcode :)"
Output: "Stay home! Practice on Leetcode :)"
```

```text
Input: text = "x &gt; y &amp;&amp; x &lt; y is always false"
Output: "x > y && x < y is always false"
```

```text
Input: text = "leetcode.com&frasl;problemset&frasl;all"
Output: "leetcode.com/problemset/all"
```

#### Hints

* Search the string for all the occurrences of the character `&`.

## Solution

```rust
pub fn entity_parser(text: String) -> String {
    let mut result = String::with_capacity(text.len());
    let mut buf = String::with_capacity(8);
    let mut iter = text.chars();

    while let Some(ch) = iter.next() {
        if ch != '&' {
            result.push(ch);
            continue;
        }

        buf.clear();
        while let Some(ch) = iter.next() {
            if ch == '&' {
                if buf.is_empty() {
                    result.push('&');
                    continue;
                } else {
                    buf.push('&');
                    break;
                }
            }

            buf.push(ch);

            if !ch.is_ascii_alphabetic() {
                break;
            }

            if buf.len() >= 6 {
                break;
            }
        }

        match buf.as_str() {
            "amp;" => result.push('&'),
            "apos;" => result.push('\''),
            "quot;" => result.push('"'),
            "gt;" => result.push('>'),
            "lt;" => result.push('<'),
            "frasl;" => result.push('/'),
            _ => {
                result.push('&');
                result.push_str(&buf);
            }
        }
    }

    result
}
```