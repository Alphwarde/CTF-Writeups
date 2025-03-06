# Image Editing - Writeup  

### Solve Time: ~10 mins  

## Challenge Description  
The challenge, **Image Editing**, involves extracting data from an image using various editing and analysis techniques.  

### Challenge Prompt  
![Challenge Prompt](https://github.com/user-attachments/assets/2bedce24-515e-4df2-a69a-7c7590c3d2a9)  

---

## My Approach and Thought Process  

### Step 1: Initial Inspection  
I opened the provided `final.png` file:  

![Opened Image](https://github.com/user-attachments/assets/d4915148-aec8-4d04-a709-1380a25a16fa) 
- Nothing Important in the metadata

There was nothing visually important in the image, so I moved on to metadata analysis using **ExifTool** and **Strings**.  

![ExifTool Output](https://github.com/user-attachments/assets/fac0aec4-57c6-41f8-bb77-2ed5ae8654c2)  

The `strings` output was really long and didn’t reveal anything useful.  

---

### Step 2: Checking for Hidden Data  
Next, I tried `binwalk` and `zsteg`:  

![Binwalk & Zsteg Results](https://github.com/user-attachments/assets/f2800aac-8684-4d51-af14-9fd91173f037)  

- `binwalk` detected a **hidden file**.  
- `zsteg` revealed that there’s a **PNG image hidden inside** the red channel of the original image.  

---

### Step 3: Extracting the Hidden Image  

#### **Using Binwalk**  
First, I checked the extracted files:  

![Binwalk Extraction](https://github.com/user-attachments/assets/862f5b9a-6faa-4599-b59d-cdbbf82b6996)  

- One file was empty, and the other was a **`.zlib`** file.  
- Running `strings` on it gave nothing useful.  

Since `binwalk` didn’t lead to a solution, I returned to `zsteg`.  

#### **Using Zsteg**  
The output included:  
`b1,r,lsb,xy`

Which means:  

- `b1` → **Bit plane 1**  
- `r` → **Red color channel**  
- `lsb` → **Least Significant Bit**  
- `xy` → **Both X and Y directions**  

This confirmed that the hidden image was stored using **LSB steganography** in the **red channel**.  

I proceeded with extracting it.  

---

## My Solution  

After extraction, I got the following output:  

![Extracted Image](https://github.com/user-attachments/assets/b75d6686-29e4-46d3-82bb-24ef49b1b5d7)  

At first, it looked like a corrupted file. However, I noticed the presence of a **PNG header (IHDRM)**—which meant it was an actual image.  

I saved it as a new file and opened it:  

![Opened Hidden Image](https://github.com/user-attachments/assets/97d13a9e-8dd3-4f64-aa4a-6972b2808774)  

---

### Step 4: Analyzing the Hidden Image  

Looking closely, I noticed something **strange at the top** of the image:  

![Corrupted Top Section](https://github.com/user-attachments/assets/2488cfec-c656-4f4b-9114-4179b6bec88d)  

I decided to run `exiftool`, `strings`, and `binwalk` again—starting with `exiftool`:  

![ExifTool Hidden Image](https://github.com/user-attachments/assets/46429fa9-6345-4f09-a670-cb57613e91bd)  

The **trailer data after the IEND chunk** indicated **additional hidden data**. So that was a hint to use `steg` again

---

### Step 5: Running Zsteg Again  

I ran `zsteg` on the extracted image:  

![Final Zsteg Output](https://github.com/user-attachments/assets/bc7f6242-1605-4b8e-977e-ca727f3a2a6a)  

I spotted something that **looked like a flag**, so I wrapped it in `CTFlearn{}`:  

---

## Flag  Becomes : - `CTFlearn{1_kn3W_tH3_r3D_w4s_0ff}`

---

## Support the Page ❤️  

I hope you enjoyed this writeup! If you want me to keep uploading writeups often, please show your love to the page. Your support keeps me motivated to share more solutions! 

