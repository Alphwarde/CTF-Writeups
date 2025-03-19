## Hash Collision Attack - Writeup
### Solve time: 1hr 40mins {was trying to generate a collision attack from the original hashes for over 1hour :)))))) }
## Challenge Description

The challenge presented, titled "Hash collision attack," involved exploiting a vulnerability related to hashing algorithms. The challenge included the following components:

* **Challenge Prompt:**  The hash was generated using a common hashing algorithm, and the username and salt are available to you. 
Your task is to crack the original password and then log in to the website.
Once logged in, you'll be required to provide two different strings that hash to the same value, Has exploiting a hash collision.
Successfully submitting these two strings will grant you the flag.
* **Note:**  The challenge description included a note stating that this scenario simulates a real-world attack where hash collisions are used to bypass security measures.
* **Website Link:**  A standard login page was provided, requiring the username "GRC" and the unknown password.
* **Python File:** A Python script was provided, outlining the hashing process.

## The login page 
![image](https://github.com/user-attachments/assets/f7972fee-d762-4fef-8f2b-bd3683d2cb97)
and this is the python script provided:
```python
import hashlib

username = "GRC"
password = "[Redacted]"
salt = "coll"

combined = salt + password

hashed = hashlib.md5(combined.encode()).hexdigest()

print("MD5 hash with salt:", hashed)
# MD5 hash with salt: 3ac226cead2ac3b22c0fbe93d455c89f
```

### Solution and Approach

The first step in addressing the challenge was to analyze the provided Python file. This script reveals the use of the `hashlib` library and the `md5` hashing algorithm. The `salt` variable is set to "coll." The process involves concatenating the `salt` with the `password` and then generating the MD5 hash. The comment at the end of the script provides the target hash value: `3ac226cead2ac3b22c0fbe93d455c89f`.
And also looking at the salt being called "coll" i remembered a github tool called ```coll```

To recover the original password, a brute-force approach was employed. This method involves systematically trying different passwords and comparing the resulting salted hash with the target hash. A common wordlist used for this purpose is `rockyou.txt`.
- rockyou.txt is a wordlist for common password
The following script was used to perform the brute-force attack:

```python
import hashlib

salt = "coll"
target_hash = ["3ac226cead2ac3b22c0fbe93d455c89f"]

def check_password(password):
    combined = salt + password
    hashed = hashlib.md5(combined.encode()).hexdigest()
    return hashed in target_hash

with open("rockyou.txt", "r", encoding="utf-8", errors='ignore') as f:
    for line in f:
        password = line.strip()
        if check_password(password):
            print(f"Found password: {password}")
            break
```

This script reads each line from the `rockyou.txt` file, treats it as a potential password, prepends the salt, calculates the MD5 hash, and checks if it matches the target hash. The `errors='ignore'` parameter handles potential decoding issues in the wordlist. Executing this script revealed the password to be `passpasspassthrough`.

With the password recovered, it was possible to log in to the provided website using the username "GRC" and the identified password.



![image](https://github.com/user-attachments/assets/cfd2ef9b-b004-4a81-80d2-663f9f9b3b05)



The second part of the challenge required identifying two distinct strings that produce the same MD5 hash, demonstrating a hash collision. Discovering such collisions for MD5 manually can be computationally intensive. so, publicly known examples of MD5 collisions were the choice. I found that the strings "TEXTCOLLBYfGiJUETHQ4hEcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak" and "TEXTCOLLBYfGiJUETHQ4hEcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak" are known to produce the same MD5 hash value in certain contexts if using a certain salt+password suffix or prefix.

![image](https://github.com/user-attachments/assets/d8f14859-0a5d-40f6-be55-aa712268dbce)



Submitting these two strings on the website solved the challenge,resulting in the flag.

![image](https://github.com/user-attachments/assets/6a5529c9-47a7-4311-84af-3c59ae09c512)
Flag is : ASU{hash_collision_found_https://x.com/realhashbreaker/status/1770161965006008570_Successfully!!!}
