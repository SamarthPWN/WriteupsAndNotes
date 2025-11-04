# Modulo Mystery

**Description:** Can you find the number ?  
**File:** [modulo.zip](./modulo.zip)

## File Structure:
modulo.zip contains two files:  
- flag.enc
- modulo.py

## Given File Content:

### flag.enc:
16 49 14 21 7 48 49 15 6 48 44 10 12 49 20 0 23

### modulo.py:
```python
import random

inp = input("Enter plaintext: ")

def encrypt(pt):
    key = random.randint(1, 100)
    results = [str(ord(ch) % key) for ch in pt]   
    print("Encrypted:", " ".join(results))
    with open('flag.enc', 'w') as f:
        f.write(" ".join(results))
    return key

k = encrypt(inp)
print("Key (for debug):", k)
```

## Writeup:
The encryption script was simple: it generated a random key between 1 and 100 and then took each character of the plaintext, converted it to its ASCII value and stored the result of ord(ch) % key. The encrypted output was the sequence of remainders written into `flag.enc`.  
Given the flag file, the goal was to reverse this process and recover the plaintext, essentially the original flag.  
The first step was to guess or deduce the key. Since the encryption only used modulo with a small range (1–100), I could brute-force each possible key and check which one could map to something resembling the flag format `v1t{...}`.  
When I tested possible keys, I found that `key = 51` was the only value that produced valid remainders consistent with `v1t{`. For instance:  
```text
ord('v') % 51 = 16
ord('1') % 51 = 49
ord('t') % 51 = 14
ord('{') % 51 = 21
```
These matched perfectly with the starting sequence `16 49 14 21` from the encrypted file, confirming that `51` was the correct key.  
From here, the next step was to find all printable characters whose `ASCII value modulo 51` gave each remainder from `flag.enc`. Each remainder had a few possible printable matches but sticking to the usual lowercase + digit + underscore pattern used in CTF flags helped narrow it down.  
After reconstructing all possible characters and aligning them into a readable string, the result turned out to be the flag.

## Flag:
```text
v1t{m0dul0_pr1z3}
```
