# Misconfigured RSA

**Description:** Oops i did it again!  
**File:** [misconfigured_RSA.txt](./misconfigured_RSA.txt)

## Given File Content:
n = 148900953097814724338206947679223698832179691968218755697733749707084556942286184505525791780949441847197006147827388400754499224336852575956050210608024912280019773833889546324355353746095214275985515374968532505153145975517881297436944244066461866248895871696012367810254055557824874852294865749524482337551  
e = 65537  
c = 107217087223013352864419426588613439434708031699522027786711684217439431898186052583896596846379575153070982123347045493427454234913154021933229641985591412104222934496019950746514726800406326146713516918611779367873873294259462206805554572977819244626333164240237423211396727885901436510649294574529712562954

## Writeup:
At first glance, it looks like a regular RSA setup with a public key `(n, e)` and a ciphertext `c`. But the challenge title “Misconfigured RSA” suggested that something was off. Since RSA usually depends on the `modulus n` being the product of two primes `p` and `q`, I decided to verify whether `n` was actually composite or not.  
I checked the value of `n` on [FactorDB](https://factordb.com) to see if it had already been factored. The result showed `Status: P`, meaning that `n` is a prime number. This immediately explains the “misconfiguration” in the challenge name. `n` was never meant to be prime. RSA completely breaks down in this situation because the entire security of RSA depends on `n` being the product of two large primes `p` and `q`.  
Normally, we calculate Euler’s Totient function as `φ(n) = (p - 1) * (q - 1)` and then find the private exponent `d` such that `d ≡ e⁻¹ mod φ(n)`. But since `n` itself is prime, the totient function becomes `φ(n) = n - 1`. This makes it trivial to compute the private key without needing to factor anything. Once that clicked, I computed the modular inverse of `e` with respect to `(n - 1)` and used it to decrypt the ciphertext.  
Here’s the Python code I used:
```python
n = 148900953097814724338206947679223698832179691968218755697733749707084556942286184505525791780949441847197006147827388400754499224336852575956050210608024912280019773833889546324355353746095214275985515374968532505153145975517881297436944244066461866248895871696012367810254055557824874852294865749524482337551
e = 65537
c = 107217087223013352864419426588613439434708031699522027786711684217439431898186052583896596846379575153070982123347045493427454234913154021933229641985591412104222934496019950746514726800406326146713516918611779367873873294259462206805554572977819244626333164240237423211396727885901436510649294574529712562954

phi = n - 1
d = pow(e, -1, phi)

m = pow(c, d, n)
plaintext = m.to_bytes((m.bit_length() + 7) // 8, 'big')

print(plaintext.decode())
```
Running the above code printed the flag.

## Flag:
```text
v1t{f3rm4t_l1ttl3_duck}
```
