# Duck RPG

**Description:** Can you patch the batch to get the secret ending of the game ?  
**File:** [duck_rpg.zip](./duck_rpg.zip)

## File Structure:
duck_rpg.zip contains two files:  
- game.bat
- result.bat

## Given File Content:

### game.bat:
```batch
@echo off
setlocal EnableDelayedExpansion

:mainmenu
cls
echo ==========================
echo        DUCK RPG
echo ==========================
echo.
echo 1. Start Adventure
echo 2. Quit
echo.
set /p choice=Choose: 
if "%choice%"=="1" goto intro
if "%choice%"=="2" exit
goto mainmenu

:intro
cls
echo In a world overrun by evil ducks...
echo Only YOU can stop the feathery menace.
set /a hero_hp=100
pause
goto battle1

:battle1
call :fight "Angry Duck" 50 8
if "!hero_dead!"=="1" goto gameover
set "frag1=unlock"
goto battle2

:battle2
call :fight "Duck Mage" 100 12
if "!hero_dead!"=="1" goto gameover
set "frag2=the"
goto battle3

:battle3
call :fight "Mother Goose" 420 69
if "!hero_dead!"=="1" goto gameover
set "frag3=goose"
goto victory

:victory
cls
echo You defeated all the evil ducks!
echo The pond is safe again, hero.
set "full=%frag1%%frag2%%frag3%"
set "self=%~f0"
set "hash="
for /f "skip=1 tokens=1" %%H in ('certutil -hashfile "%self%" SHA256') do (
    call set "hash=%%H"
    goto result
)

:result
call result.bat !full! !hash!
exit /b

:gameover
echo You were defeated by the ducks...
echo Try again, brave one.
exit /b
goto mainmenu

:fight

set "enemy_name=%~1"
set /a enemy_hp=%~2
set /a enemy_atk=%~3
set hero_dead=0

:combat
cls
echo ==========================
echo Battle: !enemy_name!
echo ==========================
echo Your HP: !hero_hp!
echo !enemy_name! HP: !enemy_hp!
echo.
echo 1. Attack
echo 2. Heal (+10 HP)
echo 3. Run
set /p act=Action: 

if "!act!"=="1" (
    set /a enemy_hp-=10
    echo You hit !enemy_name! for 10!
) else if "!act!"=="2" (
    set /a hero_hp+=10
    echo You healed 10 HP!
) else if "!act!"=="3" (
    echo You cannot run from ducks! 
) else (
    echo Invalid.
)

if !enemy_hp! LEQ 0 (
    echo.
    echo You defeated !enemy_name!
    goto :eof
)

set /a dmg=%random% %% %enemy_atk% + 5
set /a hero_hp-=dmg
echo.
echo !enemy_name! hits you for !dmg! damage!

if !hero_hp! LEQ 0 (
    set "hero_dead=1"
    endlocal & set "hero_dead=1"
    goto :eof
)

pause
goto combat

:battle0
call :fight "Tiny Duck" 1 1
if "!hero_dead!"=="1" goto gameover
set "frag3=duck"
goto victory
```

### result.bat:
```text
䀦汣♳獀瑥∠䊜썣䀽氱坙啚歲䭳䴹硷㉤䱐祇䡰㠶佦瑓㑆扁煡稳䑘略乊捒䈠㝯と癧楮䤵浪呃噑≅┊䊜썣縺ⰰ┱鰥捂㫃㍾ⰹ┱鰥捂㫃㑾ⰴ┱鰥捂㫃㑾ⰹ┱鰥捂㫃㑾ⰷ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰷ┱鰥捂㫃㉾ⰵ┱鰥捂㫃㉾ⰵ┱਍㨺鰥捂㫃㑾ⰵ┱鰥捂㫃ㅾⰰ┱鰥捂㫃㕾ⰴ┱믡▃䊜썣縺㠵ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㠲ㄬ┥䊜썣縺ⰷ┱鰥捂㫃㍾ⰳ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㕾ⰳ┱뫡▿䊜썣縺〴ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺ⰸ┱鰥捂㫃㑾ⰹ┱듃鰥捂㫃㕾ⰳ┱鰥捂㫃㕾ⰱ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰴ┱돃鰥捂㫃㑾ⰵ┱鰥捂㫃㉾ⰸ┱鰥捂㫃㑾ⰹ┱鰥捂㫃㍾ⰳ┱鰥捂㫃㕾ⰸ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㥾ㄬ醻਍鰥捂㫃㕾ⰴ┱鰥捂㫃㉾ⰵ┱鰥捂㫃㑾ⰵ┱┢ㅾ㴢∽•ന┊䊜썣縺㔴ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㤳ㄬ┥䊜썣縺㐱ㄬ┥䊜썣縺㐵ㄬ┥䊜썣縺㠲ㄬ┥䊜썣縺㔴ㄬ⼥鰥捂㫃㍾ⰲ┱਍ഩഊ┊䊜썣縺㐵ㄬ┥䊜썣縺㔲ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㌵ㄬ┥䊜썣縺㜴ㄬ┥䊜썣縺㠲ㄬ┥䊜썣縺㔴ㄬ∥縥∲㴽㠢㤳搲捣户昶敤摢愵〷ㄲ挱攱ㄲ㤴愷㔵戳ㄳ㉦㝣㐰㠰㝢㈷㑣㍡㌱ㄶ搵㝦㙢∰⠠਍鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㍾ⰹ┱鰥捂㫃㑾ⰴ┱鰥捂㫃㑾ⰹ┱鰥捂㫃㑾ⰷ┱ꤥꞶ썍棃┥䊜썣縺㔴ㄬ┥䊜썣縺㈴ㄬ┥䊜썣縺〴ㄬ┥䊜썣縺㤴ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺〴ㄬ┥䊜썣縺㤴ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺〲ㄬ┥䊜썣縺㜴ㄬ┥䊜썣縺〴ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㐴ㄬ┥䊜썣縺㌳ㄬ┥䊜썣縺㌵ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㌵ㄬ┥䊜썣縺㜴ㄬ┥䊜썣縺㠲ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㐴ㄬ┥䊜썣縺㤴ㄬ┥䊜썣縺㌳ㄬ┥䊜썣縺㌵ㄬ┥䊜썣縺ㄵㄬ┥䊜썣縺㤳ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㠲ㄬ┥䊜썣縺㤴ㄬ┥쎨쏃抜╈鰥捂㫃㍾ⰹ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㕾ⰱ┱鰥捂㫃㍾ⰳ┱鰥捂㫃㕾ⰸ┱鰥捂㫃㍾ⰹ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰴ┱鰥捂㫃㑾ⰷ┱鰥捂㫃ㅾⰵ┱鰥捂㫃㍾ⰹ┱਍鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㍾ⰹ┱鰥捂㫃ㅾⰴ┱鰥捂㫃㕾ⰴ┱鰥捂㫃㉾ⰸ┱鰥捂㫃㑾ⰵ┱┯䊜썣縺㈳ㄬഥ⤊਍਍鰥捂㫃㕾ⰴ┱鰥捂㫃㉾ⰵ┱鰥捂㫃㑾ⰵ┱┢ㅾ㴢∽湵潬正桴摥捵≫⠠਍鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㍾ⰹ┱鰥捂㫃㑾ⰴ┱鰥捂㫃㑾ⰹ┱鰥捂㫃㑾ⰷ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㕾ⰲ┱鰥捂㫃ㅾㄬ┥䊜썣縺㠲ㄬ笥鰥捂㫃㉾ⰱ┱鰥捂㫃㍾ⰰ┱鰥捂㫃㉾ⰸ┱鰥捂㫃㑾ⰴ┱鰥捂㫃㑾ⰹ┱╟䊜썣縺㠲ㄬ┥䊜썣縺㤴ㄬ┥䊜썣縺㔳ㄬ弥鰥捂㫃㍾ⰲ┱鰥捂㫃㍾ⰰ┱鰥捂㫃㉾ⰸ┱鰥捂㫃㑾ⰴ┱鰥捂㫃㑾ⰹ┱╟䊜썣縺㠲ㄬ┥䊜썣縺〵ㄬ弥鰥捂㫃㕾ⰱ┱鰥捂㫃㍾ⰵ┱鰥捂㫃㉾ⰸ┱╟䊜썣縺㠲ㄬ┥썑쏃䲱╯鰥捂㫃㑾ⰹ┱鰥捂㫃㍾ⰵ┱╟䊜썣縺ⰹ┱鰥捂㫃㍾ⰵ┱鰥捂㫃㑾ⰴ┱鰥捂㫃㝾ㄬ┥䊜썣縺㔳ㄬ┥䊜썣縺㠲ㄬ弥鰥捂㫃㍾ⰵ┱鰥捂㫃㕾ⰳ┱鰥捂㫃ㅾⰵ┱鰥捂㫃ㅾㄬ┥䊜썣縺㌵ㄬ┥䊜썣縺ㄵㄬ紥਍┩䊜썣縺㔴ㄬ┥䊜썣縺㤳ㄬ┥䊜썣縺ⰲ┱鰥捂㫃㥾ㄬ┥䊜썣縺㤳ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㐵ㄬ┥淃멩秃╏鰥捂㫃㉾ⰵ┱鰥捂㫃㑾ⰵ┱┢ㅾ㴢∽湵潬正桴来潯敳•ന┊䊜썣縺㔴ㄬ┥뮯濃穌◃鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㍾ⰹ┱鰥捂㫃㑾ⰴ┱鰥捂㫃㑾ⰹ┱넥ꂢ冡曃┥䊜썣縺㜴ㄬ┥썕睌ꡍ╨鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ㄬ┥癘뇃쎼╧鰥捂㫃㍾ⰹ┱鰥捂㫃㉾ㄬ┥䊜썣縺ⰲ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㉾ⰸ┱鰥捂㫃㑾ⰹ┱鰥捂㫃㍾ⰳ┱鰥捂㫃㉾ⰸ┱鰥捂㫃㑾ⰵ┱鰥捂㫃ㅾⰳ┱鰥捂㫃㍾ⰳ┱鰥捂㫃㥾ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㔲ㄬ┥䊜썣縺〴ㄬ┥䊜썣縺㌵ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㈳ㄬ┥䊜썣縺〴ㄬ┥䊜썣縺㠲ㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㠲ㄬ┥䊜썣縺㤴ㄬ┥䊜썣縺㤳ㄬ┥䊜썣縺ⰷ┱鰥捂㫃㍾ⰹ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㕾ⰳ┱鰥捂㫃㑾ⰷ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㉾ⰵ┱鰥捂㫃㉾ㄬ┥檨썱쎤╶鰥捂㫃㍾ⰳ┱쌥쎥䭡썏┥䊜썣縺ㄵㄬ┥䊜썣縺㔴ㄬ┥䊜썣縺㤴ㄬ┥䊜썣縺㤳ㄬ┥䊜썣縺ⰷ┱鰥捂㫃㍾ⰹ┱਍┩䊜썣縺㔴ㄬ┥䊜썣縺㤳ㄬ┥䊜썣縺ⰲ┱鰥捂㫃㥾ㄬ┥䊜썣縺㤳ㄬ┥䊜썣縺㔴ㄬ⠥਍鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㑾ⰵ┱鰥捂㫃㍾ⰹ┱鰥捂㫃ㅾⰴ┱鰥捂㫃㕾ⰴ┱鰥捂㫃㉾ⰸ┱鰥捂㫃㑾ⰵ┱┯䊜썣縺㈳ㄬഥ⤊
```

## Writeup:
When I first opened `game.bat`, it looked like a simple text-based RPG made entirely using Windows batch scripting. It started with a main menu displaying options to either start the adventure or quit the game. Selecting `Start Adventure` triggered a short storyline introduction, followed by a series of battles implemented through functions using `:fight` labels. Each battle had parameters defining the enemy’s name, health and attack power, and player choices like Attack, Heal or Run controlled the flow. After successfully defeating the enemies, the game gradually built up three fragments `frag1`, `frag2`, and `frag3` corresponding to `unlock`, `the`, and `goose` respectively. These were later concatenated into a single variable `full=%frag1%%frag2%%frag3%`, forming the string `unlockthegoose`.  
At the very end of the game, after victory, there was a line that caught my attention:
```batch
call result.bat !full! !hash!
```
This line passed two arguments into another batch file, `result.bat`: the concatenated string `(unlockthegoose)` and a `SHA256` hash of the script itself, which was calculated using the Windows `certutil` command. This clearly meant the second file was the verification layer that determined whether the input was correct and if it was, it would likely print the flag or `secret ending`.  
When I opened `result.bat`, things immediately looked strange. Instead of readable commands, the entire file was filled with nonsensical Unicode characters and symbols. Running `file result.bat` on my Mac terminal confirmed this suspicion:
```bash
result.bat: Unicode text, UTF-16, little-endian text, with very long lines (1576), with no line terminators
```
The file was encoded in `UTF-16 Little Endian`, and the lack of line breaks explained why it appeared as a single unreadable mess. Viewing the leading bytes using `xxd` showed the typical `ff fe` BOM signature, confirming the encoding.  
To analyze it, I first converted the file into a readable `UTF-8` version using the `iconv` command:
```bash
iconv -f UTF-16 -t UTF-8 result.bat > result.decoded.bat
```
However, even after conversion, the contents were still unintelligible because of how the script was written. It wasn’t actually encrypted; it was obfuscated using batch substring syntax like `%VAR:~N,1%`, where each character of the final text was built by taking one character from a long stored string based on index offsets.  
To understand what the script was doing, I opened a short portion of it using Python to decode it in a single-byte view (`Latin-1`) so I could see the hidden ASCII bytes properly:
```python
python3 - <<'PY'
b = open('result.bat','rb').read()
print(b[:2000].decode('latin-1', errors='replace'))
PY
```
This immediately revealed a key line near the top:
```bat
@set "BcÃ=@1lYWZUrksK9Mwxd2PLGypH68fOStF4Abaq3zXDeuJNRc Bo7h0gvni5IjmCTQVE"
```
That long alphanumeric string was clearly the lookup table, each of those characters served as an indexable source that the later `%VAR:~N,1%` tokens would use to reconstruct readable output.  
To automate the process of decoding all those substring references and reassemble the hidden strings, I wrote a quick Python script in the same directory as the files. Its purpose was to identify the variable that stores the lookup string (`VAR`), extract all patterns of `%VAR:~N,1%` used throughout the file, map each index `N` to the corresponding character from the lookup string and concatenate those characters in order to reconstruct the actual readable text the batch script would have printed during execution.
```python
import re
s = open('result.bat','rb').read().decode('latin-1', errors='ignore')
m = re.search(r'set\s+"([^=]+)=([^"]+)"', s, flags=re.IGNORECASE)
varname = m.group(1)
val = m.group(2)
print(f"Found var {varname!r} with length {len(val)}")

def char_at(n):
    n = int(n)
    return val[n] if 0 <= n < len(val) else '?'

occ = []
for g in re.finditer(r'((?:%'+re.escape(varname)+r':~\d+,1%)+)', s):
    occ.append(g.group(1))
occ += re.findall(r'\{([^}]{4,})\}', s)
seen = []
for o in occ:
    if o not in seen:
        seen.append(o)

results = []
for o in seen:
    idxs = re.findall(re.escape('%')+re.escape(varname)+r':~(\d+),1%', o)
    out = ''.join(char_at(int(i)) for i in idxs)
    results.append((o, out))

more = []
for m in re.finditer(r'(.{0,40}(%'+re.escape(varname)+r':~\d+,1%)[^%]{0,200})', s):
    block = m.group(0)
    idxs = re.findall(re.escape('%')+re.escape(varname)+r':~(\d+),1%', block)
    if idxs:
        out = ''.join(char_at(int(i)) for i in idxs)
        more.append((block.strip(), out))

uniq = {}
for k,v in results+more:
    uniq[v] = uniq.get(v, []) + [k]

print("\nAssembled candidates (unique):")
for assembled, contexts in uniq.items():
    print(">>>", assembled)
print("\nIf you see v1t{...} above, that's the flag.")
```
After running this script on the original obfuscated `result.bat`, it printed multiple reconstructed strings including various echo statements, internal messages and one clearly recognizable flag construction:
```text
echo v1t
p4tch
th3
b4tch
t0
g3t
th3
s3cr3t
3nd1ng
```
Combining these fragments together exactly as the batch script would have concatenated them during runtime revealed the final flag.

## Flag:
```text
v1t{p4tch_th3_b4tch_t0_g3t_th3_s3cr3t_3nd1ng}
```
