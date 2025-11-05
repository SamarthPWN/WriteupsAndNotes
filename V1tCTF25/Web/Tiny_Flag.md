# Tiny Flag

**Description:** Do you see the tiny flag :>  
**Website:** https://tommytheduck.github.io/tiny_flag

## Writeup:
At first glance, the challenge presented a simple webpage. The phrase tiny flag immediately stood out as it suggested something small or hidden in plain sight, possibly within a favicon, a small embedded image or some metadata. Opening the website showed nothing particularly interesting in the visible content, so I decided to inspect how the site was being served.  
Since the URL indicated that the page was hosted on GitHub Pages, I checked the username portion of the URL, `tommytheduck`, and navigated to `https://github.com/tommytheduck`. Sure enough, I found a public repository named `tiny_flag` that corresponded exactly to the hosted page. Opening the repository revealed a few files: `index.html`, `style.css`, `script.js`, and `favicon.ico`.  
Given the challenge’s description emphasizing the tiny aspect, I immediately suspected the favicon file might be the key. Favicon files (`.ico`) are small images that appear next to a website’s tab title and CTF creators often hide flags inside them either visually or through steganography. So, I downloaded and opened the `favicon.ico` file directly from `https://github.com/tommytheduck/tiny_flag/blob/main/favicon.ico`.  
Upon opening it, the image itself clearly contained the flag. It wasn’t obfuscated or encoded, just hidden in plain sight within the tiny icon. The flag text was visibly embedded within the favicon image, confirming that the challenge title and description were literal hints guiding toward the solution.

## Flag:
```text
v1t{T1NY_ICO}
```
