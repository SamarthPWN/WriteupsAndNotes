# Among USniversity

**Description:**  
Bro, I found "Among Us" at this school!  

Can you spot the hidden acronym?  
Wrap it in v1t{...} to submit your answer.  

>Example: University of Economics Ho Chi Minh City => v1t{UEH}  

**File:** [amongus.png](./amongus.png)

## Writeup:
The challenge provided an image showing a large building with Vietnamese text written on top that read `Trường Đại học Công nghệ Thông tin`. At first glance, it seemed to depict a university or educational institution, so I decided to investigate what the text meant. To confirm its origin, I performed a reverse image search using Google Lens. The results immediately pointed to a university located in `Ho Chi Minh City, Vietnam`. Translating the Vietnamese text `Trường Đại học Công nghệ Thông tin` revealed that it stands for `University of Information Technology`. Based on the challenge hint about finding the hidden acronym, it became clear that the acronym of this university, `UIT`, was the intended answer. Therefore, wrapping it in the required flag format, we got the final flag.

## Flag:
```text
v1t{UIT}
```
