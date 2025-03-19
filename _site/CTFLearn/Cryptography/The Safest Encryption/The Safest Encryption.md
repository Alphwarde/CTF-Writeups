# The Safest Encryption - Writeup  
### Solve time: ~20 mins  

## Challenge description  
The challenge, *"The Safest Encryption"*, involves a hidden use of XOR encryption.

**Challenge Prompt**:  
I intercepted this zip file, and the contents seem to be encrypted, but it looks like the key is also inside. Could you try recovering the encrypted file?

- **Zip File**: `CTFLearn.zip`
- **Challenge Difficulty**: Medium
- **Challenge Points**: 40

---

## My Approach and Thought Process

1. **Initial Inspection**  
   I started by unzipping `CTFLearn.zip`, which revealed two files: `CTFLearn.pdf` and `CTFLearn.txt`.

   ![Unzipped Files](https://github.com/user-attachments/assets/1979d10a-d015-461c-a12d-5e0939d83fb4)

2. **Checking the Files' Usability**  
   I tried opening both files, but neither worked. I used the `file` command to inspect their formats.

   ![File Command Result](https://github.com/user-attachments/assets/069977eb-9aca-4aa3-be17-fdd7a8294569)

3. **Fixing the File Headers**  
   After checking the files, I suspected that the file headers might be incorrect. I began by fixing the header of the `.pdf` file. The magic bytes for a PDF file should be `25 50 44 46 2D`. 

   Before fixing the header:  
   ![Original PDF Header](https://github.com/user-attachments/assets/485c0a8c-5b24-4dc3-85d4-03a563d4da2c)

   After fixing the header:  
   ![Fixed PDF Header](https://github.com/user-attachments/assets/b32b5f7a-4804-4693-a45d-778605088bc4)

4. **Opening the Fixed PDF**  
   Even after fixing the header, the PDF wouldn’t open. This led me to believe the PDF was either empty or just a red herring in the challenge.

5. **Analyzing the Challenge Name**  
   Given the name of the challenge, I initially thought it could be related to AES encryption, but after testing it with no results, I decided to investigate further using the provided files.

6. **Looking for Clues**  
   I ran `wc` (word count) on both files to check their byte size.

wc CTFLearn.pdf 
113 565 24065 CTFLearn.pdf

wc CTFLearn.txt
79 547 24065 CTFLearn.txt

Both files had the same number of bytes (`24065`), indicating they might be related.

7. **Verifying the Identical Files with `xxd`**  
I ran `xxd` on both files to examine their content. The output showed identical, seemingly random binary data.

![xxd output](https://github.com/user-attachments/assets/a12a40f6-9aab-4610-a4c4-0b54e199f0f7)

![xxd output 2](https://github.com/user-attachments/assets/59a2dcd9-f951-465c-b902-eb30f309948f)

---

## The Solution

Based on my observations, I deduced that the two files were XORed with each other, likely because the encryption key is the same length as the data itself.

### **XOR Decryption Code**

I wrote a simple Python script to XOR the two files together:

```python

file1 = bytearray(open("CTFlearn.pdf", "rb").read())  
file2 = bytearray(open("CTFlearn.txt", "rb").read())  

xord_byte_array = bytearray(len(file1))

for i in range(len(file1)):
 xord_byte_array[i] = file1[i] ^ file2[i]

with open("output.pdf", "wb") as out_file:
 out_file.write(xord_byte_array)

print("done")
```
Result:
- After running the script, I checked the resulting output.pdf and found the flag inside.
![image](https://github.com/user-attachments/assets/008d2cd2-ea29-4d1d-9ff5-da901dad801c)
- Also after solving it through this script i found a tool called `XORfiles`
- ![image](https://github.com/user-attachments/assets/12448cb8-e40b-4a8a-8794-9f34878a6c29)

  
# The Flag:
### CTFlearn{CTFlearn_is_fun_hope_you_enjoyed_it!}

### I hope this writeup helped you understand the solution to the challenge! The key was recognizing the XOR encryption and understanding that both files were the same length and likely encrypted using XOR. If you found this writeup helpful, feel free to show your appreciation so I can continue creating writeups like this. Thanks for reading and for your time! 💖
