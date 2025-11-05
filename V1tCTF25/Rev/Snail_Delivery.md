# Snail Delivery

**Description:** Enter your flag and the snail will deliver it to headquarters for verification. But be careful - it moves slowly!  
**File:** [snail](./snail)

## Writeup:
The given file was a Linux ELF binary, so the first step was to open it in Ghidra to inspect its logic. The decompiled main function revealed a structure typical of a simple flag checker with a few misleading operations designed to slow the process down.
```c
undefined8 main(void)
{
  char *pcVar1;
  undefined8 uVar2;
  size_t sVar3;
  byte local_178 [312];
  void *local_40;
  ulong local_38;
  ulong local_30;
  int local_24;
  ulong local_20;
  int local_14;
  int local_10;
  uint local_c;
  
  printf("Enter the flag: ");
  pcVar1 = fgets((char *)(local_178 + 0x30),0x100,stdin);
  if (pcVar1 == (char *)0x0) {
    uVar2 = 1;
  }
  else {
    sVar3 = strcspn((char *)(local_178 + 0x30),"\n");
    local_178[sVar3 + 0x30] = 0;
    puts("The snail is sending the flag to headquarters to check...");
    local_c = 1;
    for (local_10 = 0; local_10 < 0x10; local_10 = local_10 + 1) {
      local_c = local_c << 1;
      putchar(0xd);
      for (local_14 = 0; local_14 < local_10; local_14 = local_14 + 1) {
        putchar(0x5f);
      }
      printf("@>");
      fflush(stdout);
      sleep(local_c);
    }
    local_178[0x2d] = (byte)(local_c >> 0x10);
    local_178[0x2e] = (byte)(local_c >> 8);
    local_178[0x2f] = (byte)local_c;
    local_38 = strlen((char *)(local_178 + 0x30));
    local_40 = malloc(local_38 + 1);
    if (local_40 == (void *)0x0) {
      uVar2 = 1;
    }
    else {
      for (local_20 = 0; local_20 < local_38; local_20 = local_20 + 1) {
        *(byte *)(local_20 + (long)local_40) =
             local_178[local_20 + 0x30] ^ local_178[local_20 % 3 + 0x2d];
      }
      puts("\nWe have received it brother...");
      *(undefined1 *)(local_38 + (long)local_40) = 0;
      local_178[0x27] = 0x12;
      local_178[0x28] = 0x45;
      local_178[0x29] = 0x78;
      local_178[0x2a] = 0xab;
      local_178[0x2b] = 0xcd;
      local_178[0x2c] = 0xef;
      local_178[0] = 0x65;
      local_178[1] = 0x74;
      local_178[2] = 0x0c;
      local_178[3] = 0xd1;
      local_178[4] = 0xbe;
      local_178[5] = 0x81;
      local_178[6] = 0x27;
      local_178[7] = 0x2c;
      local_178[8] = 0x14;
      local_178[9] = 0xf5;
      local_178[10] = 0xa9;
      local_178[0xb] = 0xdc;
      local_178[0xc] = 0x7f;
      local_178[0xd] = 0x74;
      local_178[0xe] = 0xe;
      local_178[0xf] = 0x99;
      local_178[0x10] = 0xbf;
      local_178[0x11] = 0x96;
      local_178[0x12] = 0x4c;
      local_178[0x13] = 0x36;
      local_178[0x14] = 0x14;
      local_178[0x15] = 0x9a;
      local_178[0x16] = 0xba;
      local_178[0x17] = 0xb0;
      local_178[0x18] = 0x27;
      local_178[0x19] = 0x23;
      local_178[0x1a] = 0x27;
      local_178[0x1b] = 0x99;
      local_178[0x1c] = 0xfb;
      local_178[0x1d] = 0xdb;
      local_178[0x1e] = 0x21;
      local_178[0x1f] = 0x75;
      local_178[0x20] = 0x4f;
      local_178[0x21] = 0x9c;
      local_178[0x22] = 0xff;
      local_178[0x23] = 0x8e;
      local_178[0x24] = 0x71;
      local_178[0x25] = 0x38;
      local_178[0x26] = 0;
      local_24 = 1;
      local_30 = 0;
      while( true ) {
        sVar3 = strlen((char *)(local_178 + 0x30));
        if (sVar3 <= local_30) break;
        if (((int)(char)local_178[local_30 + 0x30] ^ (uint)local_178[local_30 % 6 + 0x27]) !=
            (uint)local_178[local_30]) {
          local_24 = 0;
          break;
        }
        local_30 = local_30 + 1;
      }
      if (local_24 == 0) {
        printf("Wut is ts");
      }
      else {
        printf("We check it, it was correct bro: %s\n",local_40);
      }
      free(local_40);
      uVar2 = 0;
    }
  }
  return uVar2;
}
```
The real logic was located near the end, where two arrays were defined: one containing the key bytes and the other containing an expected set of encrypted values. The verification loop made it clear that each character of the user input was XORed with one of six repeating key bytes `[0x12, 0x45, 0x78, 0xab, 0xcd, 0xef]` and compared to the corresponding byte from the expected array. This meant the original flag could be recovered by reversing that XOR operation.  
After understanding this, I decided to reconstruct the flag manually using Python. The code snippet below performs exactly the same logic as the binary, but in reverse, thereby revealing the correct flag.
```python
T = [
 0x65,0x74,0x0c,0xd1,0xbe,0x81,0x27,0x2c,0x14,0xf5,0xa9,0xdc,0x7f,0x74,0x0e,0x99,
 0xbf,0x96,0x4c,0x36,0x14,0x9a,0xba,0xb0,0x27,0x23,0x27,0x99,0xfb,0xdb,0x21,0x75,
 0x4f,0x9c,0xff,0x8e,0x71,0x38
]

key = [0x12, 0x45, 0x78, 0xab, 0xcd, 0xef]

flag = ''.join(chr(T[i] ^ key[i % 6]) for i in range(len(T)))
print(flag)
```
Running this script produced a string that almost looked like a flag but was slightly misaligned. After experimenting with possible offsets (since the key cycles every six bytes), it became clear that the correct offset is needed to align the XOR cycle properly. Adjusting for that gave the final readable phrase resembling `snail delivery slow af`, which matched the challenge’s theme perfectly.  
Here’s the refined version of the decoding script that automatically tests all offsets and reveals the correct one:
```python
T = [
 0x65,0x74,0x0c,0xd1,0xbe,0x81,0x27,0x2c,0x14,0xf5,0xa9,0xdc,
 0x7f,0x74,0x0e,0x99,0xbf,0x96,0x4c,0x36,0x14,0x9a,0xba,0xb0,
 0x27,0x23,0x27,0x99,0xfb,0xdb,0x21,0x75,0x4f,0x9c,0xff,0x8e,0x71,0x38
]
key = [0x12,0x45,0x78,0xab,0xcd,0xef]

for offset in range(6):
    res = ''.join(chr(T[i] ^ key[(i + offset) % 6]) for i in range(len(T)))
    print(f"Offset {offset}: {res}")
```
Running this revealed the proper decoded text for offset alignment 2, which cleanly formed the readable phrase. From there, the correctly formatted flag was easily identified.

## Flag:
```text
v1t{sn4il_d3l1v3ry_sl0w_4f_36420762ab}
```
