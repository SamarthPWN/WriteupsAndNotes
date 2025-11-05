# Login Panel

**Description:** login  
**Website:** https://tommytheduck.github.io/login

## Writeup:
I found a simple client side login challenge hosted at the website. The page prompts for a username and a password. Rather than attempting to brute force the site in the browser, I inspected the project repository because the URL pointed to a GitHub Pages site. Visiting `https://github.com/tommytheduck` led me to the repository named `login` where the `index.html` was available. The `index.html` contains the complete client side logic, so the challenge is solvable without any network trickery. I examined its script to learn exactly what the page was checking and how the flag is produced.  
The page source as found in the repository is included below:
```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Login Panel</title>
</head>
<body>
  <script>
    async function toHex(buffer) {
      const bytes = new Uint8Array(buffer);
      let hex = '';
      for (let i = 0; i < bytes.length; i++) {
        hex += bytes[i].toString(16).padStart(2, '0');
      }
      return hex;
    }

    async function sha256Hex(str) {
      const enc = new TextEncoder();
      const data = enc.encode(str);
      const digest = await crypto.subtle.digest('SHA-256', data);
      return toHex(digest);
    }

    function timingSafeEqualHex(a, b) {
      if (a.length !== b.length) return false;
      let diff = 0;
      for (let i = 0; i < a.length; i++) {
        diff |= a.charCodeAt(i) ^ b.charCodeAt(i);
      }
      return diff === 0;
    }

    (async () => {
      const ajnsdjkamsf = 'ba773c013e5c07e8831bdb2f1cee06f349ea1da550ef4766f5e7f7ec842d836e'; // replace
      const lanfffiewnu = '48d2a5bbcf422ccd1b69e2a82fb90bafb52384953e77e304bef856084be052b6'; // replace

      const username = prompt('Enter username:');
      const password = prompt('Enter password:');

      if (username === null || password === null) {
        alert('Missing username or password');
        return;
      }

      const uHash = await sha256Hex(username);
      const pHash = await sha256Hex(password);

      if (timingSafeEqualHex(uHash, ajnsdjkamsf) && timingSafeEqualHex(pHash, lanfffiewnu)) {
        alert(username+ '{'+password+'}');
      } else {
        alert('Invalid credentials');
      }
    })();
  </script>
</body>
</html>
```
Reading the script made the intended solution obvious. The page computes the SHA 256 digest of the entered username and the entered password, converts those digests to hex strings and compares them to two hard coded hex values named `ajnsdjkamsf` and `lanfffiewnu`. The comparison uses a `timingSafeEqualHex` function which performs a constant time style check. If both the username hash and the password hash match the hard coded values the page shows an alert built as `username + '{' + password + '}'`. Because the code is entirely client side the problem reduces to recovering the preimage values for the two SHA-256 hashes present in the file. No server side attack is required.  
I verified the hash values and found the corresponding preimages by computing SHA 256. First I confirmed the two relevant hashes copied from the file:
```bash
username hash: ba773c013e5c07e8831bdb2f1cee06f349ea1da550ef4766f5e7f7ec842d836e
password hash: 48d2a5bbcf422ccd1b69e2a82fb90bafb52384953e77e304bef856084be052b6
```
Then I checked candidate strings with the standard shell `sha256sum` utility. For example to verify a candidate called `v1t` I used:
```bash
echo -n 'v1t' | sha256sum
```
This produced the `ba773c013e5c07e8831bdb2f1cee06f349ea1da550ef4766f5e7f7ec842d836e` hash which matches the username hash in the file. Similarly I verified the password candidate using:
```bash
echo -n 'p4ssw0rd' | sha256sum
```
This produced the `48d2a5bbcf422ccd1b69e2a82fb90bafb52384953e77e304bef856084be052b6` hash which matches the password hash in the file. Here is the short Python snippet that reproduces the same checks and returns the hex digest when run:
```python
import hashlib
def sha256hex(s):
    return hashlib.sha256(s.encode()).hexdigest()

print('v1t ->', sha256hex('v1t'))        
print('p4ssw0rd ->', sha256hex('p4ssw0rd'))  
```
Because the alert concatenates username, then an open brace, then the password, then a closing brace the page shows the flag when the correct credentials are entered. The challenge statement asked for the flag in `v1t{}` format so the expected final flag is exactly that output. I tested this by entering the recovered values into the prompt after loading the `index.html` copy. The alert displayed the flag confirming the result.

## Flag:
```text
v1t{p4ssw0rd}
```
