# The Safest Encryption - Writeup
### Solve time: ~20 mins
## Challenge description
The Challenge presented,"The Safest Encryption" is a very hidden use of XOR encryption
* *The Challenge Prompt*: I intercepted this zip file, the contents seem to be encrypted, but it looks like the key is also in there. Could you try recovering the encrypted file?
* *ZipFile* named `CTFLearn.zip`
* Challenge diffiulty: Medium
* Challenge Points: 40
## My approach and thought process
- So at first i checked the zipfile and unzipped it to find a `.txt` file and a `.pdf` file

![image](https://github.com/user-attachments/assets/1979d10a-d015-461c-a12d-5e0939d83fb4)

- I tried opening both the files and they didnt work so i used `file` to check their usability 

![image](https://github.com/user-attachments/assets/069977eb-9aca-4aa3-be17-fdd7a8294569)

- I decided to fix the file headers for both the files, starting with the `.pdf` file, the magic bytes for the `.pdf` should be `25 50 44 46 2D`
![image](https://github.com/user-attachments/assets/485c0a8c-5b24-4dc3-85d4-03a563d4da2c) Becomes:
![image](https://github.com/user-attachments/assets/b32b5f7a-4804-4693-a45d-778605088bc4)

- Now when i tried opening the PDF, i couldnt open it so i knew that the PDF is empty or maybe its just a `Red Herring`
- When i read the Challenge's name i thought `AES` but seeing it provided nothing with 2 almost useless file
- i thought about seeing if they are actually containing anything so i run `wc` on both files (`word count`)
  ![image](https://github.com/user-attachments/assets/1211992f-4f1b-4da8-879a-949d9a684330)
- `113` lines / `79` lines
- `547` words/`547` words
- and the same bytes which are `24056`
- I ran `xxd` to make sure that my idea is correct
- ![image](https://github.com/user-attachments/assets/a12a40f6-9aab-4610-a4c4-0b54e199f0f7)
- ![image](https://github.com/user-attachments/assets/59a2dcd9-f951-465c-b902-eb30f309948f)

## The Solution
- so seeing that the bytes are identical, and the xxd shows gibberish binary data,,, i figured this might be `XOR` since usually they key and cipher are the same length, if either the key is repeated for the ciphertext or its just a long key

```python
file1 = bytearray(open("CTFlearn.pdf", "rb").read())  # open("CTFlearn.pdf", "rb"): Opens CTFlearn.pdf in binary read mode (rb).
file2 = bytearray(open("CTFlearn.txt", "rb").read()) # same thing here


xord_byte_array = bytearray(len(file1_b))   

for i in range(len(file1_b)): 
    xord_byte_array[i] = file1_b[i] ^ file2_b[i]


with open("output.pdf", "wb") as out_file:
    out_file.write(xord_byte_array)

print("done") 
```
- so checking out The `output.pdf` in hopes of finding something
-![image](https://github.com/user-attachments/assets/790447a6-03de-474c-b35d-c7b96eb3fc49)
#### The flag is: `CTFlearn{CTFlearn_is_fun_hope_you_enjoyed_it!}`
## hopefully you enjoyed reading this writeup, if so, please show your love and appreciation so i can keep  making writeups like this in the future <3
