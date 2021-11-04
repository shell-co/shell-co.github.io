+++
title = "Prime Concat"
description = "The flag is concated with a random string and encrypted using a random prime number"
date = 2021-05-01T19:30:00+00:00
updated = 2021-05-01T19:30:00+00:00
draft = false
weight = 30
sort_by = "weight"
template = "wiki/page.html"

[extra]
lead = "Concat the flag with random string and encrypt based on random prime number."
toc = false
top = false
+++
## Overview
Difficulty: Easy

Type: Prime number

Question: The flag is concatenated with a random string then encrypted using a random prime number with `n` bits where `n = 15`.

Observations: If prime is known it's easy to work backwards. In this problem you can find the prime because it is equal to output length. Also, when an unprovided validation check involves prime numbers, it probably has to do with the prime roots.

Comps: [ASIS CTF Quals 2021: Crypto Warmup](https://ctftime.org/task/17801)

## Tools
[Prime root calculator](http://www.bluetulip.org/2014/programs/primitive.html)

## Example Question
Given Python code like the following decode the given output text.
```python
#!/usr/bin/env python3

from Crypto.Util.number import *
import string
from secret import is_valid, flag

def random_str(l):
	rstr = ''
	for _ in range(l):
		rstr += string.printable[:94][getRandomRange(0, 93)]
	return rstr

def encrypt(msg, nbit):
	l, p = len(msg), getPrime(nbit)
	rstr = random_str(p - l)
	msg += rstr
	while True:
		s = getRandomNBitInteger(1024)
		if is_valid(s, p):
			break
	enc = msg[0]
	for i in range(p-1):
		enc += msg[pow(s, i, p)]
	return enc

nbit = 15
enc = encrypt(flag, nbit)
print(f'enc = {enc}')
```

## Solution
To solve this we have to find `s` & `p`. Then we can work backwards. `p` we is equal to message length because of the `random_str` function. `s` we can infer from the `is_valid` function that it is related to the random prime `p`. The most obvious relation would be a prime root. We can plug `p` into a prime root calculator and then brute force that list very quickly with code like the following.

```rust
use rayon::prelude::*;

fn main() {
   // The provided output key
    let output = "";
    let file_name = "";

    let list: Vec<usize> = std::fs::read_to_string(file_name)
        .expect("file not found!")
        .lines()
        .map(|x| x.parse().expect("Error parsing line"))
        .collect();

    list.par_iter().for_each(|n| action(n, output));
}

fn action(guess: &usize, output: &str) {
    // get output length and create similar sized array
    let p = output.len();
    let mut msg = Vec::with_capacity(p);

    let mut chars = output.char_indices();

    // Load the first in since it doesn't change
    let (_, ch) = chars.next().unwrap();
    msg[0] = ch;

    // Iterate over the remaining reversing the encryption
    //
    // Also instead of using our prime roots as a guess we could just brute
    // force 1 to p since it isn't that big. But much easier to just use prime roots.
    chars.for_each(|(i, c)| msg[guess.pow(i as u32 - 1) % p] = c);

    let unencrypt: String = msg.into_iter().collect();

    // Check if it contains the start of flag
    // If it does print the firt 100 chars for us
    if unencrypt.contains("ASIS{") {
        let (first, _last) = unencrypt.split_at(100);
        print!("{}", first);
    }
}
```
