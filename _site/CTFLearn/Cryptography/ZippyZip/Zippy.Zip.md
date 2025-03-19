# ZippyZip - Writeup  

### Solve Time: ~5 mins  

## Challenge Description  
The challenge, **ZippyZip**, is obviously related to zip files.  

### Challenge Prompt  
![Challenge Prompt](https://github.com/user-attachments/assets/f4a7fb4f-f7b7-454b-bed0-28f0fbc813cb)  

---

## My Approach and Thought Process  

### Step 1: Extracting the ZIP File  
I started by first unzipping the provided file:  

![Extracting ZIP](https://github.com/user-attachments/assets/d3cfad33-0434-4f67-aaad-9440fdc53a98)  

Upon extraction, I found a large number of zip files, making it difficult to check them all manually. However, I attempted it anyway:  

![Multiple ZIP Files](https://github.com/user-attachments/assets/98bb0db8-3610-4d74-9030-d6d219f785c8)  

### Step 2: Analyzing Password Protection  
At this point, I realized what the challenge meant by **"a very very STRONG password."**  
I tried common passwords like `Zippy` and `ZippyZip`, including various combinations, but none worked.  

This led me to consider two possibilities:  
- The phrase **"very very STRONG"** might be sarcasm.  
- The key to solving the challenge might not lie in manually unzipping the files.  

### Step 3: Cracking the ZIP Passwords  
At this point, I decided to try cracking the ZIP files' passwords using a tool named `zip-crc-cracker`.  
To install it, I ran:  
`git clone https://github.com/kmyk/zip-crc-cracker`

## My Solution:
I ran the following command with all the file names:
`python crack.py`
![image](https://github.com/user-attachments/assets/aac45a8b-e464-4afa-8e51-2edc3cf52f2d)
- Looking closely at the bottom part of the output:
![image](https://github.com/user-attachments/assets/6d8b60cc-8c4d-42f1-a0bb-9929afd2e2bc)
- **I found the flag**
- ## `CTFLearn{S0m3t1m35_u$1ng_h4rd_p4s5w0rd_i5_n0t_3n0ugh}`
# Support the Page ❤️
I hope you enjoyed this writeup! If you want me to keep uploading writeups weekly, please show your love to the page. Your support keeps me motivated to share more solutions! <33

