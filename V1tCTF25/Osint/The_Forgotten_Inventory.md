# The Forgotten Inventory

**Description:**  
In the summer of 2007, a massive archive quietly surfaced — a meticulous ledger of items that once rolled across desert sands under foreign command. The file was structured, line after line, page after page, each entry tied to a unit whose banners flew far from home.  
Someone wasn’t happy about its release. A message was sent, demanding its silence — a digital plea from a uniformed gatekeeper. The request was denied.  
Your task is to uncover the sender of that plea.  
Clues hide in old public archives — look for a tabular trail documenting what was once called Operation Freedom, catalogued in a comma-separated vault of **1,996 pages** worth of equipment.  
Some say the sands between two nations hold the real context — where east met west, and war turned into a spreadsheet.  
Find the** email address** of the official who tried to bury the list.

Format: v1t{hello.hi@ehatever.com}

**Hint:** https://www.youtube.com/watch?v=dQw4w9WgXcQ

## Writeup:
The text referenced a `comma-separated vault of 1,996 pages`, which immediately made me think of a CSV file, possibly one that was leaked or archived somewhere. The phrase `Operation Freedom` and the mention of `equipment rolling across desert sands under foreign command` suggested a connection to Iraq or Afghanistan war-related data leaks, particularly those that came out in the mid-to-late 2000s.  
Given there was no direct file download, I decided to start looking for archives matching that description. Searching for `Operation Freedom site:wikileaks.org csv` and `equipment.csv 1996 pages site:wikileaks.org` quickly led me to a Wikileaks release titled Iraq War Logs and related pages referencing `Iraq Equipment Database`. One entry in particular, known as the Iraq Equipment CSV leak, matched the description of a large tabular dataset listing items, units and logistics data from the U.S. military operations in Iraq. This was exactly what the challenge described. Around the time of this release, there had indeed been official correspondence attempting to take down the material.  
I began by searching keywords from the challenge text:
```text
"Iraq equipment" "csv" "Wikileaks" "takedown request"
```
This search brought me to references of a takedown notice issued by a U.S. Army official to Wikileaks. The document itself was public and available in the Wikileaks archives. I opened the specific file: `wikileaks.org/wiki/Iraq_equipment_database_(CSV)` and found details about how Wikileaks had published the full database, 1,996 pages long, exactly as the challenge hinted.  
Scrolling further down that entry, I found a link to the takedown request email sent by a U.S. Army representative, asking for the removal of the database due to its sensitive nature. The sender of that email was listed as `David J. Hoskins`, with the email address `david.j.hoskins@us.army.mil`. This perfectly fit the challenge’s description of `a uniformed gatekeeper` who sent `a digital plea` that `was denied`.  
At this point, I had cross-verified that this was an authentic, publicly available government correspondence tied directly to the specific 1,996-page CSV database release about Operation Freedom. Therefore, this was clearly the correct flag.

## Flag:
```text
v1t{david.j.hoskins@us.army.mil}
```
