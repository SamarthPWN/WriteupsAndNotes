# Discord

**Description:** Join our [Discord](https://discord.gg/WyYTC2EZqK) and look at the duck :>  

## Writeup:
The challenge encoded a binary message across three channel header strings using `:v0:` to represent `0` and `:v1:` to represent `1`. The channel headers contained the following sequences:  
Announcements:
```text
:v0::v1::v1::v1::v0::v1::v1::v0::v0::v0::v1::v1::v0::v0::v0::v1::v0::v1::v1::v1::v0::v1::v0::v0::v0::v1::v1::v1::v1::v0::v1::v1::v0::v1::v1::v0::v0::v1::v0::v0:
```
Updates:
```text
:v0::v0::v1::v1::v0::v0::v0::v1::v0::v0::v1::v1::v0::v1::v0::v1::v0::v0::v1::v1::v0::v1::v0::v1::v0::v1::v1::v0::v0::v0::v1::v1:
```
Ticket:
```text
:v0::v0::v1::v1::v0::v0::v0::v0::v0::v1::v1::v1::v0::v0::v1::v0::v0::v1::v1::v0::v0::v1::v0::v0::v0::v1::v1::v1::v1::v1::v0::v1:
```
The intended approach was to remove the `colons` and the letter `v`, leaving only a string of `0s` and `1s`. Once that was done for all three channels, I concatenated them in the order of `announcements`, `updates` and `ticket` to form a single continuous binary string. I then divided this binary string into groups of eight bits, with each group representing one ASCII character.  
Here is the small Python snippet I used to perform the conversion:
```python
ann = "0111011000110001011101000111101101100100" 
upd = "00110001001101010011010101100011"             
tix = "00110000011100100110010001111101"          

s = ann + upd + tix

bytes_list = [s[i:i+8] for i in range(0, len(s), 8)]
decoded = ''.join(chr(int(b, 2)) for b in bytes_list)
print(decoded)
```
Running this code and converting these binary groups to text using the binary-to-ASCII conversion revealed the hidden message, which turned out to be the flag.

## Flag:
```text
v1t{d155c0rd}
```
