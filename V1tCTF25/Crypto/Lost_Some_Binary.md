# Lost Some Binary

**Description:** SOS we lost some binary sir!  
**File:** [Lost_Some_Binary.txt](./Lost_Some_Binary.txt)

## Given File Content:
01001000 01101001 01101001 01101001 00100000 01101101 01100001 01101110 00101100 01101000 01101111 01110111 00100000 01110010 00100000 01110101 00100000 00111111 01001001 01110011 00100000 01101001 01110100 00100000 00111010 00101001 00101001 00101001 00101001 01010010 01100001 01110111 01110010 00101101 01011110 01011110 01011011 01011101 00100000 00100000 01001100 01010011 01000010 01111011 00111110 00111100 01111101 00100001 01001100 01010011 01000010 01111110 01111110 01001100 01010011 01000010 01111110 01111110 00101101 00101101 00101101 01110110 00110001 01110100 00100000 00100000 01111011 00110001 00110011 00110101 00111001 00110000 00110000 01011111 00110001 00110011 00110011 00110111 00110000 01111101

## Writeup:
The challenge file was just a long list of 8-bit binary values separated by spaces. Since each group was exactly 8 bits, the first instinct was that it probably represented ASCII text. The title and the description sounded like a hint that something was hidden, but I decided to start with a basic binary-to-text conversion just to see what it showed.  
I used a quick Python one-liner to decode the binary into readable characters:
```python
binary = "01001000 01101001 01101001 01101001 00100000 01101101 01100001 01101110 00101100 01101000 01101111 01110111 00100000 01110010 00100000 01110101 00100000 00111111 01001001 01110011 00100000 01101001 01110100 00100000 00111010 00101001 00101001 00101001 00101001 01010010 01100001 01110111 01110010 00101101 01011110 01011110 01011011 01011101 00100000 00100000 01001100 01010011 01000010 01111011 00111110 00111100 01111101 00100001 01001100 01010011 01000010 01111110 01111110 01001100 01010011 01000010 01111110 01111110 00101101 00101101 00101101 01110110 00110001 01110100 00100000 00100000 01111011 00110001 00110011 00110101 00111001 00110000 00110000 01011111 00110001 00110011 00110011 00110111 00110000 01111101"
print(''.join([chr(int(b, 2)) for b in binary.split()]))
```
The output was:
```text
Hiii man,how r u ?Is it :))))Rawr-^^[]  LSB{><}!LSB~~LSB~~---v1t  {135900_13370}
```
So the file decoded cleanly into text, which at first made it look solved. But then I noticed something odd: the word “LSB” appeared multiple times in the message and the challenge name was “Lost Some Binary.” Both of these are classic hints for Least Significant Bit (LSB) steganography. It basically means some hidden information might be stored in the smallest (least significant) bits of the data.  
That got me thinking, maybe the visible ASCII text was just a distraction and the real message was hidden inside the binary pattern itself. To test that theory, I wrote a small script to extract only the last bit (the LSB) from each 8-bit group and form new bytes out of them.
```python
binary_data = binary.split()
lsb_bits = ''.join(b[-1] for b in binary_data)
hidden_message = ''.join(chr(int(lsb_bits[i:i+8], 2)) for i in range(0, len(lsb_bits), 8))
print(hidden_message)
```
This time, the output revealed the actual hidden message, which turned out to be the real flag.

## Flag:
```text
v1t{LSB:>}
```
