# Blank

**Description:** This image is blank is it ?  
**File:** [white.png](./white.png)

## Writeup:
When I first opened the file, it appeared to be a plain white rectangle, nothing unusual at first glance. The challenge name and description hinted that the image wasn’t actually blank, so I started by checking the file details. Running a quick `file` command revealed that it was a valid PNG image of size 238×62 with RGBA color mode. I then ran `pngcheck -v` to see all the PNG chunks and confirm there was nothing suspicious like hidden text chunks (`tEXt`, `zTXt`, or `iTXt`). The output showed only standard chunks: `IHDR`, `sRGB`, `gAMAv`, `pHYs`, `IDAT`, and `IEND`. This ruled out any simple metadata-based flag hiding.  
With no obvious text data, I moved on to look deeper into the pixel layer. I first checked for appended or embedded data using `binwalk -e white.png`, but the tool declined to extract anything since the image was only 1161 bytes with no extra signatures. That suggested the flag was probably embedded in the actual pixel values, which meant some form of pixel-level steganography.  
Since PNGs store pixel data inside the `IDAT` chunk in a compressed `zlib` stream, I decided to manually analyze that part. I dumped the file in hexadecimal using:
```bash
hexdump -v -e '16/1 "%02x " "\n"' white.png
```
and confirmed the `IDAT` block started after the header with a length of 1054 bytes. I then extracted the compressed data and decompressed it using `zlib` to obtain the raw pixel bytes. The image dimensions (238×62) and RGBA format meant each scanline had 1 filter byte followed by 238×4 = 952 pixel bytes, giving 953 bytes per row. With 62 rows, the total decompressed length matched exactly 953×62 = 59086 bytes, confirming the full image data was intact and valid.  
After decompressing, I parsed each scanline, skipping the first filter byte (which turned out to be 0 for most rows, meaning no filtering). From the raw bytes, I rebuilt the pixel list in RGBA form. To check if there was any hidden variation, I printed out all unique pixel tuples. Surprisingly, there were only two unique colors:
```text
(255, 255, 255, 255)
(254, 255, 255, 255)
```
That tiny 1-point difference in the red channel immediately stood out, a classic sign of least-significant-bit (LSB) steganography, where an image that appears completely white actually encodes binary data through single-bit channel changes invisible to the human eye.  
At first, I tried to interpret these variations as bit streams by extracting the least significant bit of each red value, packing them into bytes and trying all combinations of bit orders and inversions. However, none of those produced a readable ASCII string or a direct `v1t{}` pattern. That hinted that the encoding wasn’t meant to be read as a binary stream but rather as a visible glyph pattern, possibly text drawn using 254-value pixels on an otherwise white canvas.  
To test this, I converted all pixels into a black-and-white map based on their red value:
```python
from PIL import Image

width, height = 238, 62
bw = Image.new("L", (width, height))
vals = []

for r, g, b, a in pixels:
    vals.append(255 if r == 255 else 0)

bw.putdata(vals)
bw.save("red_channel_binary.png")
```
In this script, every pixel with red = 255 was treated as white and red = 254 as black. Saving and opening this new image finally revealed the truth: black letters clearly formed on a white background. The previously “blank” image now contained a readable message written in pixel form.  
To verify the exact flag text, I used `OCR (Tesseract)` on the reconstructed binary image:
```python
import pytesseract
from PIL import Image

img = Image.open("red_channel_binary.png")
extracted_text = pytesseract.image_to_string(img)
print(extracted_text)
```
The OCR output finally revealed the flag.

## Flag:
```text
vit{wh1t3_3y3s}
```
