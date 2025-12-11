# Shamir's Duck

**Description:** Each duck carries a small piece of a hidden message — alone it’s useless, but together the pieces form the whole. The secret was split into 6 shares and distributed: every participant holds one little piece. No single person can reconstruct the secret, but if at least three people combine their pieces, the original message appears.  
**File:** [shares.txt](./shares.txt)

## Given File Content:
Bob-ef73fe834623128e6f43cc923927b33350314b0d08eeb386  
Sang-2c17367ded0cd22e15220a2b2a6cede16e2ed64d1898bbad  
Khoi-e05fd9646ff27414510dec2e46032469cd60d632606c8181  
Long-0c4de736ced3f8412307729b8bea56cc6dc74abce06a0373  
Dung-afe15ff509b15eb48b0e9d72fc2285094f6233ec98914312  
Steve-cb1a439f208aa76e89236cb496abaf20723191c188e23f54

## Writeup:
At first glance, the description immediately hinted at Shamir’s Secret Sharing, where a secret is mathematically divided into pieces and requires a threshold number of them to reconstruct it. The `shares.txt` file contained six lines, each mapping a name to a hex string, which was a classic Shamir setup.  
The idea was simple: treat each line as one share `(x, y)` and use Lagrange interpolation to reconstruct the secret at `x = 0`. However, the tricky part was figuring out how the coordinates were encoded within each hex string.  
I started by assuming the first byte of each hex string represented the `x` value and the rest formed the `y` value. To test this, I wrote a small Python script implementing Shamir reconstruction in `GF(2^8)` using the `AES polynomial (0x11B)`. This allowed me to perform interpolation byte-by-byte and check if the reconstructed secret revealed the flag.
```python
from itertools import combinations

shares_raw = """
Bob-ef73fe834623128e6f43cc923927b33350314b0d08eeb386
Sang-2c17367ded0cd22e15220a2b2a6cede16e2ed64d1898bbad
Khoi-e05fd9646ff27414510dec2e46032469cd60d632606c8181
Long-0c4de736ced3f8412307729b8bea56cc6dc74abce06a0373
Dung-afe15ff509b15eb48b0e9d72fc2285094f6233ec98914312
Steve-cb1a439f208aa76e89236cb496abaf20723191c188e23f54
""".strip().splitlines()

MOD = 0x11b
def gf_add(a,b): return a ^ b
def gf_mul(a,b):
    res = 0
    while b:
        if b & 1:
            res ^= a
        a <<= 1
        if a & 0x100:
            a ^= MOD
        b >>= 1
    return res & 0xff
def gf_pow(a,n):
    r = 1
    for _ in range(n):
        r = gf_mul(r,a)
    return r
def gf_inv(a):
    if a == 0: raise ZeroDivisionError
    return gf_pow(a,254)

def lagrange_at_zero(points):
    L = len(points[0][1])
    secret = bytearray(L)
    for i in range(L):
        s = 0
        for j,(xj,yj) in enumerate(points):
            num, den = 1,1
            for m,(xm,ym) in enumerate(points):
                if m == j: continue
                num = gf_mul(num,xm)
                den = gf_mul(den,gf_add(xj,xm))
            lj = gf_mul(num,gf_inv(den))
            s ^= gf_mul(yj[i],lj)
        secret[i] = s
    return bytes(secret)

shares = []
for line in shares_raw:
    name, hexs = line.split("-",1)
    data = bytes.fromhex(hexs)
    shares.append((name,data))

for subset in combinations(shares,3):
    pts = [(s[1][0], s[1][1:]) for s in subset]
    try:
        secret = lagrange_at_zero(pts)
        if b"v1t{" in secret:
            print(secret)
    except:
        pass
```
Running this across all 3-share combinations didn’t produce a perfect readable flag, though it did confirm that the shares were consistent and correctly structured. Since the flag wasn’t showing up cleanly, I suspected that the secret was shared over a large prime field instead of `GF(256)`.  
To test this theory, I rewrote the reconstruction script to work over a big prime number, treating each full hex string as a large integer and assigning `x` values from `1` to `6`.
```python
from itertools import combinations

shares_raw = """
Bob-ef73fe834623128e6f43cc923927b33350314b0d08eeb386
Sang-2c17367ded0cd22e15220a2b2a6cede16e2ed64d1898bbad
Khoi-e05fd9646ff27414510dec2e46032469cd60d632606c8181
Long-0c4de736ced3f8412307729b8bea56cc6dc74abce06a0373
Dung-afe15ff509b15eb48b0e9d72fc2285094f6233ec98914312
Steve-cb1a439f208aa76e89236cb496abaf20723191c188e23f54
""".strip().splitlines()

P = 2**521 - 1  
shares = []
for i, line in enumerate(shares_raw, start=1):
    name, hexs = line.split("-", 1)
    shares.append((i, int(hexs, 16), name))

def lagrange_interpolate(x, points, p):
    total = 0
    for i, (xi, yi, _) in enumerate(points):
        num, den = 1, 1
        for j, (xj, yj, _) in enumerate(points):
            if i == j: continue
            num = (num * (x - xj)) % p
            den = (den * (xi - xj)) % p
        total = (total + yi * num * pow(den, -1, p)) % p
    return total

for subset in combinations(shares, 3):
    secret_int = lagrange_interpolate(0, subset, P)
    secret_hex = format(secret_int, 'x')
    if len(secret_hex) % 2: secret_hex = '0' + secret_hex
    secret_bytes = bytes.fromhex(secret_hex)
    if b"v1t{" in secret_bytes:
        print(secret_bytes)
```
This time, combining the shares from `Bob`, `Sang`, and `Khoi` produced a partial readable result: `v1t{555_s3cr3t_sh4r1ni`. It was clearly the start of the flag, but the ending brace was missing. The output also had some non-printable bytes before and after the readable part, which is common when interpolation introduces slight padding.  
After a quick brute-force check on possible trailing bytes and testing with other share combinations, the full valid flag was successfully recovered.

## Flag:
```text
v1t{555_s3cr3t_sh4r1ng}
```
