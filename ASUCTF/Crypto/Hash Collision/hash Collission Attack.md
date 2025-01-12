# Hash collision attack

## The challenge includes:
- the prompt:
The hash was generated using a common hashing algorithm, and the username and salt are available to you. 
Your task is to crack the original password and then log in to the website.
Once logged in, you'll be required to provide two different strings that hash to the same value, Has exploiting a hash collision.
Successfully submitting these two strings will grant you the flag.
- a NOTE: This challenge simulates a real-world attack scenario where attackers leverage hash collisions to bypass security systems and impersonate users.
-  website link that is just a login page that takes GRC as username and the unknown password
- and a ```.py``` file
## how to solve and thought process
  at first i read the prompt and immediately remembered the ```Hash collision attack``` which is when 2 different strings have the same hash so i checked out the ```.py``` file 

  ```
import hashlib

username = "GRC"
password = "[Redacted]"
salt = "coll"

combined = salt + password

hashed = hashlib.md5(combined.encode()).hexdigest()

print("MD5 hash with salt:", hashed)
# MD5 hash with salt: 3ac226cead2ac3b22c0fbe93d455c89f
```
  so reading this file i realized that i have to brute force the password and keep trying until the new output prints out the same as the comment, so i made this simple script:

  ```
import hashlib

salt = "coll"
target_hash = [
    "3ac226cead2ac3b22c0fbe93d455c89f"
]

def checkpassword(password):
    combined = salt + password
    hashed = hashlib.md5(combined.encode()).hexdigest()
    return hashed in target_hash

# Brute-force using rockyou.txt ofc , errors= ignore so you can avoid UnicodeDecodeError so it can run freely until it finds your passwrod
with open("rockyou.txt", "r", encoding="utf-8", errors="ignore") as f:
    for line in f:
        password = line.strip()
        if check_password(password):
            print(f"pass si: {password}")
            break
```
This script found me the password and it was: passpasspassthrough
now we can Log in to our login page, after logging in we are introduced to a new page that tells us to put 2 strings that have the same MD5 hash (which is what the ```hash collission attack``` is about, 
so after trying to find 2 strings on my own, i gave up and decided to google it ,,, 
so after searching for a bit i found out that "Pass" has the same has value as "pass1234" in certain algorithms so i had to try, after submitting both these strings i got the flag 






