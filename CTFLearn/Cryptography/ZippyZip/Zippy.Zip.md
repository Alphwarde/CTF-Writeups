# ZippyZip - Writeup
### Solve time: ~5mins

## Challenge description
The challenge,`ZippyZip` is obiously a challenge that includes zipfiles

**Challenge Prompt**:
![image](https://github.com/user-attachments/assets/f4a7fb4f-f7b7-454b-bed0-28f0fbc813cb)

## My Approach and Thought Process
- I started by first unzipping the file provided,
  ![image](https://github.com/user-attachments/assets/d3cfad33-0434-4f67-aaad-9440fdc53a98)
- a lot of zipfiles so its hard to check all of them at once, but i tried it
![image](https://github.com/user-attachments/assets/98bb0db8-3610-4d74-9030-d6d219f785c8)
- Now this is what the Challenge meant by a Password on the zipfiles, i tried `Zippy` and `ZippyZip` and a combination of both multiple times on all the files but to no avail,so i figured that maybe the "very very STRONG" password could either be
- sarcasm
- or the key isnt in unzipping these files
- I Decided to *try* and crack the Zipfiles' passwords using a tool named `zip-crc-cracker` so i installed it using `git clone https://github.com/kmyk/zip-crc-cracker`
## My solution:
![image](https://github.com/user-attachments/assets/65ea70bb-f8b1-455a-882b-9462b1fa199a)
- running `python crack.py` with all the file names
- ![image](https://github.com/user-attachments/assets/aac45a8b-e464-4afa-8e51-2edc3cf52f2d)
- looking closely at the bottom part:
   ![image](https://github.com/user-attachments/assets/6d8b60cc-8c4d-42f1-a0bb-9929afd2e2bc)
## The flag is `CTFLearn{S0m3t1m35_u$1ng_h4rd_p4s5w0rd_i5_n0t_3n0ugh}`
