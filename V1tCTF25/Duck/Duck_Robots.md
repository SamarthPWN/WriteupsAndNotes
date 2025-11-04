# Duck Robots

**Description:** Duck tells the AI where to crawl and where not to crawl  

## Writeup:
I fetched `/robots.txt` from the site because the challenge description immediately hinted at the `robots.txt` file, the standard location where websites define crawling rules for bots and search engines. On visiting `https://ctf.v1t.site/robots.txt`, the file included several content-signal headers and multiple user-agent specific rules, which looked like this:
```text
# As a condition of accessing this website, you agree to abide by the following
# content signals:

# (a)  If a content-signal = yes, you may collect content for the corresponding
#      use.
# (b)  If a content-signal = no, you may not collect content for the
#      corresponding use.
# (c)  If the website operator does not include a content signal for a
#      corresponding use, the website operator neither grants nor restricts
#      permission via content signal with respect to the corresponding use.

# The content signals and their meanings are:

# search:   building a search index and providing search results (e.g., returning
#           hyperlinks and short excerpts from your website's contents). Search does not
#           include providing AI-generated search summaries.
# ai-input: inputting content into one or more AI models (e.g., retrieval
#           augmented generation, grounding, or other real-time taking of content for
#           generative AI search answers).
# ai-train: training or fine-tuning AI models.

# ANY RESTRICTIONS EXPRESSED VIA CONTENT SIGNALS ARE EXPRESS RESERVATIONS OF
# RIGHTS UNDER ARTICLE 4 OF THE EUROPEAN UNION DIRECTIVE 2019/790 ON COPYRIGHT
# AND RELATED RIGHTS IN THE DIGITAL SINGLE MARKET.

# BEGIN Cloudflare Managed content

User-Agent: *
Content-signal: search=yes,ai-train=no
Allow: /

User-agent: Amazonbot
Disallow: /

User-agent: Applebot-Extended
Disallow: /

User-agent: Bytespider
Disallow: /

User-agent: CCBot
Disallow: /

User-agent: ClaudeBot
Disallow: /

User-agent: Google-Extended
Disallow: /

User-agent: GPTBot
Disallow: /

User-agent: meta-externalagent
Disallow: /

# END Cloudflare Managed Content

# v1t{ducks_are_g0v_r0b0ts}
User-agent: *
Disallow: /admin
```
The flag was visible in plain text within the file itself, placed just before the trailing `User-agent: *` line. No parsing or decoding was necessary beyond viewing the robots file in the browser or using curl.

## Flag:
```text
v1t{ducks_are_g0v_r0b0ts}
```
