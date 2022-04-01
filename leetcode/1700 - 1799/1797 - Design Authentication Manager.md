# [1797. Design Authentication Manager](https://leetcode.com/problems/design-authentication-manager/)

## Problem

### Description

There is an authentication system that works with authentication tokens. For
each session, the user will receive a new authentication token that will expire
`timeToLive` seconds after the `currentTime`. If the token is renewed, the
expiry time will be extended to expire `timeToLive` seconds after the (
potentially different) `currentTime`.

Implement the `AuthenticationManager` class:

* `AuthenticationManager(int timeToLive)` constructs the `AuthenticationManager`
  and sets the `timeToLive`.
* `generate(string tokenId, int currentTime)` generates a new token with the
  given `tokenId` at the given `currentTime` in seconds.
* `renew(string tokenId, int currentTime)` renews the unexpired token with the
  given `tokenId` at the given `currentTime` in seconds. If there are no
  unexpired tokens with the given `tokenId`, the request is ignored, and nothing
  happens.
* `countUnexpiredTokens(int currentTime)` returns the number of unexpired tokens
  at the given `currentTime`.

**Note** that if a token expires at time `t`, and another action happens on
time `t` (`renew` or `countUnexpiredTokens`), the expiration takes place before
the other actions.

### Constraints

* `1 <= timeToLive <= 10^8`
* `1 <= currentTime <= 10^8`
* `1 <= tokenId.length <= 5`
* `tokenId` consists only of lowercase letters.
* All calls to generate will contain unique values of `tokenId`.
* The values of currentTime across all the function calls will be strictly
  increasing.
* At most 2000 calls will be made to all functions combined.

### Examples

```text
["AuthenticationManager", "renew", "generate", "countUnexpiredTokens", "generate", "renew", "renew", "countUnexpiredTokens"]
[[5], ["aaa", 1], ["aaa", 2], [6], ["bbb", 7], ["aaa", 8], ["bbb", 10], [15]]

Output
[null, null, null, 1, null, null, null, 0]
```

## Solutions

### Using a `HashMap`

```rust
use std::collections::HashMap;

struct AuthenticationManager {
    ttl: i32,
    tokens: HashMap<String, i32>,
}

impl AuthenticationManager {
    fn new(time_to_live: i32) -> Self {
        Self {
            ttl: time_to_live,
            tokens: Default::default(),
        }
    }

    fn generate(&mut self, token_id: String, current_time: i32) {
        self.tokens.insert(token_id, current_time + self.ttl);
    }

    fn renew(&mut self, token_id: String, current_time: i32) {
        if let Some(expires_at) = self.tokens.get_mut(&token_id) {
            // renew only the tokens that have not expired yet
            if *expires_at > current_time {
                *expires_at = current_time + self.ttl;
            }
        }
    }

    fn count_unexpired_tokens(&self, current_time: i32) -> i32 {
        self.tokens.values().filter(|&&x| x > current_time).count() as i32
    }
}
```