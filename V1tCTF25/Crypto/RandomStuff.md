# RandomStuff

**File:** [source.zip](./source.zip)

## File Structure:
source.zip contains two files:  
- part1.py
- part2.py

## Given File Content:

### part1.py:
```python
from Crypto.Util.number import *
from hashlib import *
from Crypto.Cipher import AES
from Crypto.Util.Padding import *
part_1 = "s0me_r4nd0m_str1ng".encode()

class LCG():
    
    def __init__(self, seed, a, c, m):
        self.seed = seed
        self.a = a
        self.c = c
        self.m = m
        self.state = seed
        
    def next(self):
        
        self.seed = (self.a * self.seed ** 65537 + self.c) % m
        return self.seed >> 20
    
a = getPrime(50)
c = getPrime(50)
m = getPrime(100)
seed = getRandomInteger(50)

lcg = LCG(seed, a, c, m)

key = sha256(long_to_bytes(seed)).digest()
enc = AES.new(key, AES.MODE_ECB).encrypt(pad(part_1, 16))


print(f"{enc = }")
print(f"{a = }")
print(f"{c = }")
print(f"{m = }")
print(f"{lcg.next() = }")
'''
enc = b'\xe6\x97\x9f\xb9\xc9>\xde\x1e\x85\xbb\xebQ"Ii\xda\'\x1f\xae\x19\x05M\x01\xe1kzS\x8fi\xf4\x8cz'
a = 958181900694223
c = 1044984108221161
m = 675709840048419795804542182249
lcg.next() = 176787694147066159797379
'''
```

### part2.py:
```python

def main():
    flag = bytearray(b"th4t_y0u_h4ve_t0_f1nd")
    length = len(flag)

    for i in range(length):
        if i > 0:
            flag[i] ^= flag[i-1]

        v = flag[i] & 0xFF
        v ^= (v >> 4)
        v &= 0xFF
        v ^= (v >> 3)
        v &= 0xFF
        v ^= (v >> 2)
        v &= 0xFF
        v ^= (v >> 1)
        v &= 0xFF

        flag[i] = v
        print(f"{v:02x}", end="")

if __name__ == "__main__":
    main()

'''
6768107b1a357132741539783d6a661b5f3b
'''
```

## Writeup:
Starting with `part1.py`, it defined a `Linear Congruential Generator (LCG)` but with a twist: instead of the usual `a * seed + c`, it raised the seed to the power of `65537`. The script generated random primes for `a`, `c` and `m`, and a random 50-bit integer as the seed. The `AES` key was derived by taking the `SHA-256` hash of the seed converted to bytes and then encrypting the string `"s0me_r4nd0m_str1ng"` using `AES-ECB`. The printed output gave us the encrypted bytes, as well as all the `LCG` parameters and one iteration of `lcg.next()`.  
So essentially, we had everything except the original seed. Our task was to recover the seed value to get the AES key and decrypt the ciphertext.  
The `next()` function computed `(a * seed ** 65537 + c) % m` and then right-shifted it by 20 bits before printing. Since we knew `a`, `c`, `m` and the truncated output (`lcg.next()`), we could use modular arithmetic to recover seed. The key trick was that `seed' = (a * seed ** 65537 + c) % m` and from the printed value (which was `seed' >> 20`), we could brute force the lower 20 bits of `seed'` to get the full value modulo `m`. Once we found a consistent `seed` that produced the correct truncated output, we could compute the AES key.  
The following script does exactly that:  
```python
from Crypto.Util.number import *
from hashlib import sha256
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad

a = 958181900694223
c = 1044984108221161
m = 675709840048419795804542182249
lcg_next = 176787694147066159797379
enc = b'\xe6\x97\x9f\xb9\xc9>\xde\x1e\x85\xbb\xebQ"Ii\xda\'\x1f\xae\x19\x05M\x01\xe1kzS\x8fi\xf4\x8cz'

for low in range(1 << 20):
    possible = (lcg_next << 20) + low
    x = (possible - c) * pow(a, -1, m) % m
    try:
        seed = pow(x, pow(65537, -1, m - 1), m)
        key = sha256(long_to_bytes(seed)).digest()
        aes = AES.new(key, AES.MODE_ECB)
        try:
            dec = unpad(aes.decrypt(enc), 16)
            if b"v1t{" in dec:
                print("Decrypted:", dec)
                print("Seed:", seed)
                break
        except:
            pass
    except:
        continue
```
Running this script successfully decrypted the ciphertext, revealing the first part of the flag: `b'v1t{Pseud0_R4nd0m_G3ner4t0r_'`.  
So now we had the prefix of the flag. Moving on to `part2.py`, it seemed to define another transformation routine operating on a bytearray initialized as `"th4t_y0u_h4ve_t0_f1nd"`. The script applied a chain of XORs, bit shifts and printed out the resulting bytes as hex. The comment below the script showed the final printed hex value `6768107b1a357132741539783d6a661b5f3b`, confirming that this was the obfuscated form of the string.  
The simplest way to verify this was to run the given function as-is and then invert the operations to recover the readable string. Since the code’s logic was deterministic, we could directly identify that the plaintext used in `flag = bytearray(b"th4t_y0u_h4ve_t0_f1nd")` represented the second half of the flag.  
I used this small Python snippet to invert the `part2.py` transformation and get the remaining half of the flag:
```python
from binascii import unhexlify

hex_obs = "6768107b1a357132741539783d6a661b5f3b"
obs = list(unhexlify(hex_obs))

recovered = []
t_prev = None

for i, t in enumerate(obs):
    found = False
    for p in range(256):
        x = p
        if i > 0:
            x ^= t_prev 
        v = x & 0xFF
        v ^= (v >> 4); v &= 0xFF
        v ^= (v >> 3); v &= 0xFF
        v ^= (v >> 2); v &= 0xFF
        v ^= (v >> 1); v &= 0xFF
        if v == t:
            recovered.append(p)
            t_prev = t
            found = True
            break
    if not found:
        raise ValueError(f"Could not invert byte at position {i}")

rec_bytes = bytes(recovered)
print("recovered hex:", rec_bytes.hex())
try:
    print("recovered ascii:", rec_bytes.decode())
except UnicodeDecodeError:
    print("recovered ascii: (contains non printable bytes)")
    print(repr(rec_bytes))
```
Running this revealed the second part of the flag: `_1s_n0t_th4t_h4rd}`  
Combining both parts gave the final flag.

## Flag:
```text
v1t{Pseud0_R4nd0m_G3ner4t0r_1s_n0t_th4t_h4rd}
```
