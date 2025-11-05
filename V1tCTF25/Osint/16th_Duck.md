# 16th Duck

**Description:**  
Caught someone playing music at this place and here are their belonging, can you find the place ?

Flag format: v1t{latitude, longitude}

>Example: v1t{36.123456,18.789123}

**File:** [16.zip](./16.zip)

## File Structure:
16.zip contains two images:  
- belonging.png
- place.png

## Writeup:
The challenge provided two images, one appeared to be a satellite or aerial view of a facility located deep within a wooded area, while the other showed a collection of medals and badges. At first glance, the medals clearly looked Russian and seemed to belong to someone associated with the military or an intelligence organization. The challenge description hinted that identifying the location in the image might lead us toward a specific intelligence or military site.  
To start, I performed a reverse image search on the medal photo. This immediately led me to a result on `auction.ru`, where the exact same image of medals was listed for sale. The listing title was written in Russian, `Краснознаменная войсковая часть в/ч 61608`. Translating it revealed that these medals were related to the `Red Banner Military Unit 61608`. That unit number, `61608`, stood out as something worth digging into further.  
Searching for more information on this unit led me to a detailed research paper hosted on `checkfirst.network` titled `Unveiling FSB’s 16th Center SIGINT Capabilities`. The document analyzed various Russian signals intelligence (SIGINT) units, each identified by a five-digit number. Scrolling through the PDF, I eventually found the exact same number, `61608`, listed there, along with an image that perfectly matched the aerial photo provided in the challenge.  
That section of the report included a description of the facility and even mentioned its coordinates: `55.592169, 37.689097`. Comparing the structure in the PDF with the satellite image from the challenge confirmed that they were identical, with the same perimeter fencing, antenna arrays and building layout. This made it clear that the site shown in the challenge was indeed the FSB’s 16th Center SIGINT facility.  
By placing the obtained coordinates into the required flag format, we got the final flag.

## Flag:
```text
v1t{55.592169,37.689097}
```
