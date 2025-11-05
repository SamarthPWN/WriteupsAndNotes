# Optimus

**Description:** uu ee ac ac  
**File:** [optimus](./optimus)

## Writeup:
The given file was a Linux ELF binary, so the first step was to open it in Ghidra to to analyze its logic and see what it was doing internally.  
Once inside Ghidra, I quickly located the `main` function, which contained all the core logic. Here’s the decompiled code:
```c
undefined8 main(void) {
    char cVar1;
    size_t sVar2;
    char *pcVar3;
    undefined8 uVar4;
    undefined8 uStack_140;
    char local_138[268];
    int local_2c;
    char *local_28;
    int local_20;
    int local_1c;
    size_t local_18;
    int local_10;
    int local_c;

    local_28 = "0ov13tc{9zxpdr6na13m6a73534th5a}";
    uStack_140 = 0x1011f0;
    sVar2 = strlen("0ov13tc{9zxpdr6na13m6a73534th5a}");
    local_2c = (int)sVar2;
    local_c = 0;

    for (local_10 = 0; local_10 < local_2c; local_10 = local_10 + 1) {
        uStack_140 = 0x10120d;
        cVar1 = is_prime(local_10);
        if (cVar1 != '\0') {
            local_c = local_c + 1;
        }
    }

    uStack_140 = 0x101235;
    printf("Input flag: ");
    uStack_140 = 0x101250;
    pcVar3 = fgets(local_138, 0x100, stdin);

    if (pcVar3 == (char *)0x0) {
        uVar4 = 2;
    } else {
        uStack_140 = 0x10126e;
        local_18 = strlen(local_138);
        while ((local_18 != 0 && ((local_138[local_18 - 1] == '\n' || (local_138[local_18 - 1] == '\r'))))) {
            local_138[local_18 - 1] = '\0';
            uStack_140 = 0x101293;
            local_18 = strlen(local_138);
        }

        if (local_c == (int)local_18) {
            local_1c = 0;
            for (local_20 = 0; local_20 < local_2c; local_20 = local_20 + 1) {
                uStack_140 = 0x1012ff;
                cVar1 = is_prime(local_20);
                if (cVar1 == '\x01') {
                    if (local_138[local_1c] != local_28[local_20]) {
                        uStack_140 = 0x101336;
                        puts("WRONG FLAG ");
                        return 1;
                    }
                    local_1c = local_1c + 1;
                }
            }
            uStack_140 = 0x10135f;
            puts("FLAG OK QUACK ");
            uVar4 = 0;
        } else {
            uStack_140 = 0x1012de;
            puts("WRONG FLAG ");
            uVar4 = 1;
        }
    }

    return uVar4;
}
```
At first glance, the code looked long and messy, but after a quick read-through, the logic was actually quite simple. There’s a hardcoded string inside the binary:
```text
0ov13tc{9zxpdr6na13m6a73534th5a}
```
The program starts by calculating how many indices (0-based) of this string are prime numbers. It does this by looping from `0` to the length of the string and calling an `is_prime()` function for each index. Each time `is_prime(i)` returns true, it increments a counter `local_c`.  
After that, it reads user input, the supposed flag and removes any trailing newline or carriage return characters. Then, it checks if the length of the user input matches the number of prime indices found earlier (`local_c`).  
If the lengths match, it proceeds to a second loop through the same string. This time, for each prime index `i`, it compares the corresponding character from the user input with the character at that index in the hardcoded string.  
If all characters match perfectly, the program prints “FLAG OK QUACK”. Otherwise, it prints “WRONG FLAG” and exits. So essentially, the flag is constructed by taking all the characters from the string that are located at prime-numbered indices.  
To solve this, I needed to extract those characters. The string has a total length of 32 characters.  
Now, let’s list the prime indices below 32:  
2, 3, 5, 7, 11, 13, 17, 19, 23, 29 and 31.  
Picking out the characters at these positions and combining them gives the final flag.

## Flag:
```text
v1t{pr1m35}
```
