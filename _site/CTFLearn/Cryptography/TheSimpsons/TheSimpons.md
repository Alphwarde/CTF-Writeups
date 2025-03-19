# The Simpsons - Writeup  
## Solve Time: ~15 mins  

## Challenge Description  
The challenge, **The Simpsons**, involves data decryption through octal and shift ciphers.  

### Challenge Prompt  
![image](https://github.com/user-attachments/assets/b81d1ad8-0d5b-43ef-bf12-98bbb244cfcd)  

## My Approach and Thinking Process  

- Opening the image given in the challenge,  
  ![image](https://github.com/user-attachments/assets/4698a116-2d2c-4de0-9805-b5ad40bf124f)  

- Nothing here, let's do our standard procedure (`exiftool` and `strings`)  
  ![image](https://github.com/user-attachments/assets/27920b03-8bad-4214-8bd5-a7520f8001bd)  
  Nothing in the image's metadata,  

  ![image](https://github.com/user-attachments/assets/cc913ab4-bf95-4e6c-be29-8fe277699535)  
  - After running `strings`, I found this, so I decided to put the octal in CyberChef and it gave me:  
  ![image](https://github.com/user-attachments/assets/c02b32df-8872-4391-bca8-e401a42e2986)  

  - Decrypting the `key` variable gave me this:  
  ![image](https://github.com/user-attachments/assets/07da250c-f3c2-4287-b949-eac25a30d4c0)  

After doing a quick Google search, it turns out that Maggie costs **847.63**, divided by **8** becomes **105.95375**.  
Rounded becomes **106**, and adding **4** results in **`110`**.  

- So, `chr(110)` is **n**  
- Then, `key = key + key + chr(ord(key)-4)`,  
  which means `n + n + (n - 4)`, which is **j**.  
- Key becomes **`nnj`**  

## My Solution  

- Now that we have an encoded text and a key, we have two choices:  
  either **XOR** or **Vigenere cipher**, since it's gibberish.  
  - We can't use **XOR** on octal since we can't turn the key into octal.  

- With **Vigenere cipher**:  
  ![image](https://github.com/user-attachments/assets/2ebeec3f-74b2-4ead-9232-c4f042161e2e)  
  The output was **`wearenumberone`**  

## Flag  - `wearenumberone`  

## Support the Page ❤️  

I hope you enjoyed this writeup! If you want me to keep uploading writeups often, please show your love to the page and star the repo,Thank you for your time. Your support keeps me motivated to share more solutions! 

