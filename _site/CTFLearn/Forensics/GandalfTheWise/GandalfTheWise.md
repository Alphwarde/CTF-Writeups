# GandalfTheWise - Writeup  

### Solve Time: ~10 mins  

## Challenge Description  
The challenge, **"GandalfTheWise"**, involves steganography and extracting hidden data from images.  

### Challenge Prompt  
![Challenge Prompt](https://github.com/user-attachments/assets/f3938cb6-ff78-426d-ad3c-afcbb194ceab)  

---

## My Approach and Thought Process  

### Step 1: Inspecting the Image  
I started by opening the provided `Gandalf.jpg` file:  

![Gandalf Image](https://github.com/user-attachments/assets/243d7053-202e-4855-b09d-068eeee7957b)  

There was nothing visually important in the image, so I moved on to metadata analysis using **ExifTool** and **Strings**.  

### Step 2: Extracting Metadata  
Running `ExifTool`, I found this hidden comment:  

![ExifTool Output](https://github.com/user-attachments/assets/ef88c1d6-0c31-4983-b55f-3f58c35839c5)  

After decrypting the comment, it revealed:  

- `CTFlearn{xor_is_your_friend}`
  
However, this didn't make sense as the flag, and sure enough, it wasn’t.  

### Step 3: Checking for Hidden Strings  
Next, I ran `strings` on the image and found this:  

![Strings Output](https://github.com/user-attachments/assets/192999af-ac49-4574-b00b-27a0ad63b3e0)  

The same comment appeared, but this time, there were two **encrypted texts of equal length**—which looked suspicious. Given the previous hint about **XOR**, I decided to investigate further.  

---

## My Solution  

### Step 4: Using XOR Decryption  
I used **CyberChef** to decrypt the data:  

![CyberChef Decryption](https://github.com/user-attachments/assets/1f361a94-14e2-4d79-8927-71a51542b4ea)  

At first, I only decrypted the key. But then, I realized I also needed to decrypt the ciphertext itself.  

So, after decrypting both:  

![Final XOR Decryption](https://github.com/user-attachments/assets/c329e6c4-f1b0-43c8-b91a-07b2c7bc7497)  

I **found the flag!** 

---
## **`CTFlearn{Gandalf.BilboBaggins}`**

---

## Support the Page ❤️  

I hope you enjoyed this writeup! If you want me to keep uploading writeups often, please show your love to the page. Your support keeps me motivated to share more solutions! 
