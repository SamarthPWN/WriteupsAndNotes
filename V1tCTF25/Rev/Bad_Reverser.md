# Bad Reverser

**Description:** I always beat myself up because I'm bad at reverse engineering :<  
**File:** [chal_goon](./chal_goon)

## Writeup:
I began by opening the binary in Ghidra to analyze its behavior and check what kind of verification logic was used.  
After loading the binary in Ghidra, I explored the `main` function and found a large function named `FUN_001011a0`, which seemed to handle most of the logic. Before diving into the details, I noticed several library functions like `fgets`, `puts`, `ptrace` and `clock_gettime` being called, indicating the program was performing anti-debug checks and time-based validation.  
Scrolling through `FUN_001011a0`, I found that the program first prompted for input using `__printf_chk(2,"Enter flag: ");`, then read the input with `fgets`. Right after that, it used `strcspn` and `strlen` to strip newlines and check the length of the flag. The crucial check was:
```c
if (sVar3 != 0xb) goto LAB_00101393;
if (local_98 != 0x7b743176) goto LAB_00101393;
```
This revealed that the flag length must be 11 characters, and the first four bytes of the input, stored in `local_98`, must equal `0x7b743176`. Interpreting that in ASCII gives the sequence `v1t{` when read in little-endian order. This confirmed that the flag starts with `v1t{` and is 11 bytes long, meaning the complete flag follows the pattern `v1t{?????}`.  
Next, the code called `ptrace(PTRACE_TRACEME, 0, 0)` followed by a clock comparison using `clock_gettime`. If tracing or excessive delays were detected, the program would jump to a “Wrong!” path and exit. This is a simple anti-debug measure often used in reverse challenges to prevent attaching a debugger.  
After passing the checks, the code computed a key byte and started decoding a hidden data block:
```c
uVar7 = (byte)local_98 + 7 ^ 0x5a;
```
Since `local_98` starts with `v (0x76)`, this expression evaluates to:
```c
uVar7 = (0x76 + 7) ^ 0x5a = 0x27
```
Then, it iterated over a block of 0x29 bytes (41 bytes) located at address `DAT_00102040`, XORing each byte with `uVar7` and `0xaa`:
```c
local_c8[lVar5] = (&DAT_00102040)[lVar5] ^ (byte)uVar7 ^ 0xaa;
```
This transformed the static data into an interpreted “bytecode” array that the program executed instruction by instruction.  
In Ghidra, I navigated to `0x00102040` and found the 41-byte array:
```c
8c d1 8f 8e 8d 8c c7 8f 8e 8c 8c d3 8f 8e 8f 8c f5 8f 8e 8e
8c c8 8f 8e 89 8c 9e 8f 8e 88 8c ee 8f 8e 8b 8c d7 8f 8e 8a 72
```
Applying the XOR operation with `(uVar7=0x27)` and `0xaa` gave the decoded 41-byte sequence:
```c
01 5c 02 03 00 01 4a 02 03 01 01 5e 02 03 02 01 78 02 03 03
01 45 02 03 04 01 13 02 03 05 01 63 02 03 06 01 5a 02 03 07 ff
```
From the decompiled logic I concluded the program implements a tiny custom interpreter that walks the 41-byte array and executes simple opcodes one by one. The interpreter reads an opcode and, when it sees `0x01`, it pushes the following byte onto a small local stack. When it sees `0x02`, it takes the value on the top of that stack and xors it with the previously computed single-byte key `0x27`. The `0x03` opcode then performs a comparison between the xor result and a specific byte taken from the user input. If the values do not match the program jumps to the failure path. Finally, `0xff` is the success marker and causes the program to print the success message "Correct quack!". By decoding the 41 bytes and emulating this tiny VM step by step, I was able to recover each compared value and thus reconstruct the exact characters the program expects at each input position.  
Running the decoded byte sequence in a quick Python emulator showed the flag verification steps in order. Each comparison corresponded to one character from the user input. The reconstructed flag characters were obtained by emulating the custom bytecode logic.
The following Python script was used. It performs the same XOR operations and interpreter emulation as the binary, automatically revealing the valid flag.
```python
data_bytes = [
    0x8c,0xd1,0x8f,0x8e,0x8d,0x8c,0xc7,0x8f,0x8e,0x8c,
    0x8c,0xd3,0x8f,0x8e,0x8f,0x8c,0xf5,0x8f,0x8e,0x8e,
    0x8c,0xc8,0x8f,0x8e,0x89,0x8c,0x9e,0x8f,0x8e,0x88,
    0x8c,0xee,0x8f,0x8e,0x8b,0x8c,0xd7,0x8f,0x8e,0x8a,
    0x72
]

def compute_key_from_prefix(b):
    return ((b + 7) ^ 0x5a) & 0xff

def decode_blob(blob, key):
    return [b ^ key ^ 0xaa for b in blob]

def extract_checks(decoded):
    checks, i = [], 0
    while i + 4 < len(decoded):
        if decoded[i] == 0x01 and decoded[i+2] == 0x02 and decoded[i+3] == 0x03:
            value, index = decoded[i+1], decoded[i+4]
            checks.append((index, value))
            i += 5
        else:
            i += 1
    return checks

def reconstruct_flag(checks):
    flag = list("v1t{???????}")  
    key = compute_key_from_prefix(ord('v'))
    for idx, val in checks:
        flag[idx + 3] = chr(val ^ key)
    return "".join(flag)

if __name__ == "__main__":
    key = compute_key_from_prefix(ord('v'))
    decoded = decode_blob(data_bytes, key)
    checks = extract_checks(decoded)
    flag = reconstruct_flag(checks)
    print("[+] Computed key:", hex(key))
    print("[+] Recovered flag:", flag)
```
Running this script prints the computed key and the recovered flag.

## Flag:
```text
v1t{my_b4D}
```
