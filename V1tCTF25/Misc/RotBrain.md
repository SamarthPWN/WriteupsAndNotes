# RotBrain

**Description:** Ts fr lwk pmo gng  
**File:** [gnp.egami](./gnp.egami)

## Writeup:
When I first saw the challenge, the file name immediately caught my attention. It looked suspiciously like `image.png` spelled backwards, which hinted that the file might be reversed in some way. The description itself didn’t make much sense directly, but given the title, I figured it might involve some form of rotation or reversal.  
I started by renaming the file to `image.png` and checking its type using the `file` command:
```bash
mv gnp.egami image.png
file image.png
```
Surprisingly, the output showed just `data` instead of `PNG image data`, which meant the file wasn’t recognized as a valid image format. This suggested that its internal bytes were corrupted or transformed in some way. To investigate further, I examined the first few bytes using `hexdump`:
```bash
head -c 40 image.png | hexdump -C
```
The output contained a lot of `c2` and `c3` byte patterns, which often appear when binary data has been mistakenly interpreted as `UTF-8` text. To fix this, I attempted to restore the original bytes by decoding and re-encoding the file properly:
```python
python3 - <<'PY'
data = open("image.png", "rb").read()
fixed = data.decode("utf-8", errors="strict").encode("latin-1")
open("fixed.bin", "wb").write(fixed)
print("wrote fixed.bin")
PY
```
After creating `fixed.bin`, I checked its type again:
```bash
file fixed.bin
head -c 16 fixed.bin | hexdump -C
```
It still showed as just `data`, but the first few bytes `82 60 42 ae 44 4e 45 49` looked interesting. When I reversed that sequence, it read as `49 45 4E 44 AE 42 60 82`, which roughly translated to `IEND...`, the ending chunk marker of a PNG file. That confirmed it: the entire file was stored backwards.  
To restore it, I reversed all the bytes in the file using a simple Python snippet:
```python
python3 - <<'PY'
data = open("fixed.bin", "rb").read()
open("final.png", "wb").write(data[::-1])
print("Reversed file written to final.png")
PY
```
This created `final.png`. Running `file final.png` finally gave the confirmation I was waiting for:
```bash
final.png: PNG image data, 220 x 39, 8-bit/color RGBA, non-interlaced
```
That meant the image had been successfully recovered. I opened it with:
```bash
open final.png
```
The image now displayed the flag clearly.

## Flag:
```text
v1t{r3v3rs3_y0ur_br41n}
```
