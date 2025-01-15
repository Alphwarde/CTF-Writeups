## Operation Quantum Key
I can also see how nobody else solved this too, This was on the harder side of crypto challenges i've solved in my short journey as a member of JordanSec, but easily the most fun question
### Solve time: 1hr 2mins
### Challenge description 
The challenge presented, "Operation Quantum Key" is also a multi-stage cryptographic puzzle and it included multiple things:
-  the prompt: An unknown hacker collective has leaked a cryptic message intercepted by intelligence agencies:
"The keys to the quantum vault have been compromised, but only partially. SecureHintCrypt might hold the rest."
Your mission begins here.
The intercepted message refers to a sophisticated encryption system used to hide sensitive information. Intelligence suggests that parts of this encryption, called SecureHintCrypt, have appeared online, hidden among various repositories. The message also hints at weaknesses in a cryptographic exchange between two key figures, whom we will refer to by codenames for confidentiality reasons: Alice and Bob. Further investigation reveals that a remote server is actively using these cryptographic principles to safeguard sensitive data. However, the server's responses are encoded in a mysterious quantum format, believed to align with BB84 quantum key distribution protocols. Your objective is to infiltrate the hacker collective's encryption scheme, intercept their communication, and retrieve what we really need, hidden within the quantum-encoded messages. Be prepared to uncover and decode cryptic patterns, do what ever it takes through obfuscated keys, and navigate a quantum labyrinth.
The fate of the quantum vault lies in your hands

- a `nc` command : `nc 40.113.160.144 9999`
- and a `server.py` file

## Netcat and terminal interactions
We run `nc 40.113.160.144 9999` and get this:

![image](https://github.com/user-attachments/assets/8b97a24c-991e-4678-8283-8a37726eb14f)

pressing 1 would give us both "alice" and "bob" public keys which are `866625684913145634932280744953337763315848615365530199150247291824611888025329946595913979185296557954239883717976747215938590753230570173736463142550918`
and `2091304188572541307992512561367284963250706488208768345901828234262996364127594747162976645235842759156132559281708316208679192877145289410633284186420857` and P which is `3015877229509163204946821698271256218400564263313243446013749420312960675772755810435574752127720601723403882931420600264309136874939530029653810377761127`
and g which is `2`

![image](https://github.com/user-attachments/assets/e61ad99a-950c-47e2-ad95-94def90ba83e)

in this order, but note that everytime you end your terminal connection it gives you random numbers except the g stays the same,,, so Rereading the challenge's prompt i caught "SecureHintCrypt" which looked weird and new at the same time, so a quick google search got me across this reddit link 


![image](https://github.com/user-attachments/assets/8e5e9186-acc4-4779-96ee-09ad24893368)


```https://github.com/ammarjo365/SecureHintCrypt```, so cloning this repo in my terminal using `git clone https://github.com/ammarjo365/SecureHintCrypt` i get 3 files which were
![image](https://github.com/user-attachments/assets/f73438a4-aad4-4adf-b935-d1e1ef1b54e7)

- opening the python file:
```python
import hashlib                          
import base64
def custom_encrypt(plaintext, key):
    key = hashlib.sha256(key.encode()).digest()
    ciphertext = bytes([ord(c) ^ key[i % len(key)] for i, c in enumerate(plaintext)])
    return base64.b64encode(ciphertext).decode('utf-8')

if __name__ == "__main__":
    hint = "No Hint for you :)"
    key = "No Key for you :)"   01101000 01100001 01110010 01100100 00101101 01100101 01111000 01110100 01110010 01100101 01101101 01100101 01101100 01111001 00101101 01101110 01101111 00101101 01110111 01100001 01111001 00101101 01110100 01101111 00101101 01110011 01101111 01101100 01110110 01100101 00101101 01101001 01110100 00100001 00100001 00100001

    encrypted_hint = custom_encrypt(hint, key)
    print(f"Encrypted Hint: {encrypted_hint}")

Encrypted Hint: /+6K8zWk5QC8PfjrMuKp2djX2eBMPpLCwRLu2FIMghfX8ZfjcOviTu893u03rK6KiMTDtkEvlonPE7eMXkOZF5A=
```
- To gain a better understanding of the challenge, I reviewed the provided PDF file., which was `SecureHintCrypt_paper.pdf`, i will only type out the important parts that are needed for the question:
- Core Components:
```
1. SHA-256 Hashing: Used to derive a fixed-length key from a variable-length
input.
2. XOR Operation: Ensures that each character of the plaintext is securely
obfuscated with the hashed key.
3. Base64 Encoding: Converts the encrypted bytes into a readable format for
safe transmission.
```
- Decryption
```
The decryption process reverses the encryption steps:
1. Hash the provided key using SHA-256 to generate the same fixed-length key.
2. Decode the Base64 string into its original byte array.
3. XOR each byte of the ciphertext with the corresponding byte of the hashed
key, cycling through the key if necessary, to retrieve the plaintext.
```
- Limitations
```
Key Management: The security of the algorithm relies heavily on the
secrecy of the key.
Known Plaintext Attacks: If an attacker knows part of the plaintext, they
could deduce information about the key.
```
## Reverse Engineering 
Reviewing the code provided, i assumed that the comment with the binary was the "key" and the Encrypted base64 string was the hint and following the decryption process i should first hash the key then decode the string and then XOR each byte of the Ciphertext, so making a quick python script and called it `safe_decrypt.py`
```python
import hashlib
import base64

def custom_decrypt(ciphertext, key):
    key = hashlib.sha256(key.encode()).digest()
    
    ciphertext_bytes = base64.b64decode(ciphertext)
    
    decrypted_bytes = bytes([ciphertext_bytes[i] ^ key[i % len(key)] for i in range(len(ciphertext_bytes))])
    
    try:
        plaintext = decrypted_bytes.decode('utf-8')
    except UnicodeDecodeError:
        plaintext = decrypted_bytes
        
    return plaintext

if __name__ == "__main__":
    encrypted_hint = "/+6K8zWk5QC8PfjrMuKp2djX2eBMPpLCwRLu2FIMghfX8ZfjcOviTu893u03rK6KiMTDtkEvlonPE7eMXkOZF5A="
    key = "hard-extremely-no-way-to-solve-it!!!" # I converted the binary to text
    
    decrypted_hint = custom_decrypt(encrypted_hint, key)
    print(f"Decrypted Hint: {decrypted_hint}")
  ```
this gave me the Decrypted Hint: `Alice and Bob's private key consists of 7 digits as leaked to us.`
which left me even more confused so i went back to the challenge's prompt and found only BB84 which wasn't helpful at the time, but judging by all the different variables and the hint being about a secret key and opening the server.py and finding a `Diffie-Hellman` function
i was left with the assumption that this was a "Diffie-Hellman" encryption which i had to read about [https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange](url) after reading it for around ~40 mins i was left with the idea of getting G to the power of any private key mod P and that should give us the Public key, so its just about reversing it or finding it through python

## Diffie-Hellman 
opening the `server.py`:

```
import random
import sympy
import socket
import threading

def generate_large_prime(bits):
    return sympy.nextprime(random.getrandbits(bits))

def diffie_hellman(p, g):
    alice_private = random.randint([REDACTED], [REDACTED])
    pop_private = random.randint([REDACTED], [REDACTED])

    alice_public = pow(g, alice_private, p)
    pop_public = pow(g, pop_private, p)

    return alice_private, pop_private, alice_public, pop_public

def bb84_ciphertext(shared_secret, flag_part):
    binary_string = ''.join(format(ord(c), '08b') for c in flag_part)
    
    bb84_cipher = ''.join('+' if bit == '1' else 'x' for bit in binary_string)
    
    return bb84_cipher

def bb84_vh_ciphertext(flag_part):
    binary_string = ''.join(format(ord(c), '08b') for c in flag_part)
    
    vh_cipher = ''.join('v' if bit == '1' else 'h' for bit in binary_string)
    
    return vh_cipher

def handle_client(client_socket):
    client_socket.settimeout(420)

    try:
        p = generate_large_prime(512)
        g = 2

        alice_private, pop_private, alice_public, pop_public = diffie_hellman(p, g)

        shared_secret = pow(alice_public, pop_private, p)

        full_flag = "ASU{REDACTED}"

        part1 = full_flag[:15]
        part2 = full_flag[15:]

        client_socket.send(b"Welcome to the Diffie-Hellman and BB84 Challenge!\n")

        while True:
            client_socket.send(b"\nChoose an option:\n1. Generate DH parameters\n2. Enter Shared Secret\n3. Exit\n")
            client_socket.send(b"Enter your choice: ")

            choice = client_socket.recv(1024).decode().strip()

            if choice == '1':
                client_socket.send(f"Alice's Public Key: {alice_public}\n".encode())
                client_socket.send(f"Pop's Public Key: {pop_public}\n".encode())
                client_socket.send(f"Prime p: {p}\nGenerator g: {g}\n".encode())

            elif choice == '2':
                client_socket.send(b"Enter the shared secret: ")
                try:
                    user_secret = int(client_socket.recv(1024).decode().strip())
                except ValueError:
                    client_socket.send(b"Invalid input. Please enter a number.\n")
                    continue

                if user_secret == shared_secret:
                    client_socket.send(b"Correct! Now generating BB84 ciphertext for the first part...\n")

                    bb84_cipher_part1 = bb84_ciphertext(shared_secret, part1)
                    client_socket.send(f"BB84 Ciphertext (in +x format): {bb84_cipher_part1}\n".encode())

                    client_socket.send(b"Please decrypt the first part and enter it back: ")

                    decrypted_part1 = client_socket.recv(1024).decode().strip()

                    if decrypted_part1 == part1:
                        client_socket.send(b"Correct! Now generating BB84 ciphertext for the second part...\n")

                        bb84_cipher_part2 = bb84_vh_ciphertext(part2)
                        client_socket.send(f"BB84 Ciphertext (in vh format): {bb84_cipher_part2}\n".encode())

                        client_socket.send(b"Please decrypt the second part and enter it back: ")

                        decrypted_part2 = client_socket.recv(1024).decode().strip()

                        if decrypted_part2 == part2:
                            client_socket.send(b"Congratulations! You've solved the challenge!\n")

                            client_socket.send(f"The full flag is: {full_flag}\n".encode())

                        else:
                            client_socket.send(b"Incorrect second part. Try again.\n")
                    else:
                        client_socket.send(b"Incorrect first part. Try again.\n")
                else:
                    client_socket.send(b"Incorrect shared secret. Please try again.\n")

            elif choice == '3':
                client_socket.send(b"Goodbye!\n")
                client_socket.close()
                break

            else:
                client_socket.send(b"Invalid choice. Please select a valid option.\n")

    except socket.timeout:
        client_socket.send(b"Connection timed out. Goodbye!\n")
        client_socket.close()
    except Exception as e:
        client_socket.send(f"An error occurred: {str(e)}\n".encode())
        client_socket.close()

def start_server():
    server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    server.bind(('0.0.0.0', 9999))
    server.listen(5)
    print("Server started. Waiting for connections...")

    while True:
        client_socket, addr = server.accept()
        print(f"Connection from {addr}")
        client_handler = threading.Thread(target=handle_client, args=(client_socket,))
        client_handler.start()

if __name__ == "__main__":
    start_server()
```
- I realized after reading the code and understanding it, that i need to have the private key in order to find the "shared secret" which was the 2nd option in the `nc` as shown above.
- Using the hint i got which was that the secret key was only `7 digits long`, it was obvious that this was an invite to try and bruteforce the key which wasnt too hard to do since its only 7 digits.
- Going to the diffie-hellman function which should give us the private of one of them atleast, i decided to tweak it a little especially since it has [REDACTED] which reinforced my idea, so i exported the function to another file with my own variables (P,G, alice and bob's public)
```



def find_private_key(public_key, p, g):
    for private_key in range(1000000, 10000000):
        if pow(g, private_key, p) == public_key:
            return private_key
    return None


p = 2097947000756935589608332006513979869804680285113472092732511697964058804291837406041335467618185055009764810923489091292965356311634401988656813522620219
g = 2
alice_public = 944343232190072225757732522653751527269721401673125445917313822158617642491446271758224871929022022841661017436241653415443762683408594182140273771711648
pop_public = 2002832255168757964823617278001218206863012971467164422926470261237925584448109141927913700659285807701132784625929149727371838287126294543688530716240676

alice_private = find_private_key(alice_public, p, g)

    print(f"Alice's Private Key: {alice_private}")
   
pop_private = find_private_key(pop_public, p, g)
    print(f"Pop's Private Key: {pop_private}")
```

in this scenario we will use Alice's private key which is `4295721` and now to find the shared secret its fairly simple, its just the public key to the power of the private key mod P so by removing everything from the script we just made and putting alice's secret as a variable and using it to find the shared secret, do note that we have to use either bob's public with alice private key or the opposite, we cant use alice's private and public at the same time
```python

# Given values for the Diffie-Hellman public parameters
p = 2097947000756935589608332006513979869804680285113472092732511697964058804291837406041335467618185055009764810923489091292965356311634401988656813522620219
g = 2
alice_public = 944343232190072225757732522653751527269721401673125445917313822158617642491446271758224871929022022841661017436241653415443762683408594182140273771711648
pop_public = 2002832255168757964823617278001218206863012971467164422926470261237925584448109141927913700659285807701132784625929149727371838287126294543688530716240676
alice_secret = 4295721
shared_secret = pow(pop_public,alice_secret,p)

print(shared_secret)
```
this printed out ` 1197596624653641676029328792644663063556936155141580998378675183302868669889179362730133398556689968212131806358956158344182770010467402879698694717400241 `, so i went to the `nc` to input this number into the 2nd option and it gave me this: `x+xxxxx+x+x+xx++x+x+x+x+x++++x++x+xxxx+xx+xxxx+xxx+++xxxxx++x+xxx+xxxx++x++x+xxxxx++x+xxx++x++xxx++x++xxxx++xx++x++x+++x` which really confused me until i remembered...

![image](https://github.com/user-attachments/assets/2539f3b7-d204-464b-a49c-3e449ba15de3)

## BB84 
i remembered that there was still a section that we havent seen which was the BB84 encryption, after searching online, I found this article talking about BB84 so i checked it out and to my surprise it had the way to solve this encryption::[https://medium.com/quantum-untangled/quantum-key-distribution-and-bb84-protocol-6f03cc6263c5](url)

![image](https://github.com/user-attachments/assets/da4aa183-f7cf-4d6d-b970-3af5aae982fc)

- After reviewing the BB84 protocols, I implemented a script to decrypt it, mapping 'x' to 0 and '+' to 1
 
```python
  def bb84_to_bits(bb84_string):
    # Map '+' to '1' and 'x' to '0'
    return ''.join('1' if char == '+' else '0' for char in bb84_string)


bb84_string = "x+xxxxx+x+x+xx++x+x+x+x+x++++x++x+xxxx+xx+xxxx+xxx+++xxxxx++x+xxx+xxxx++x++x+xxxxx++x+xxx++x++xxx++x++xxxx++xx++x++x+++x"

binary= bb84_to_bits(bb84_string)


print(binary)
  ```
![image](https://github.com/user-attachments/assets/7ac8702b-57af-4696-9653-f1b8b571c64b)

- and it gave me part of the flag `ASU{BB84Ch4ll3n` and gave me another string which was `hvvhhvvvhhvvhhvvhvhvvvvvhvhhhvhhhvhhvhhhhvhvvvvvhvhhhhhvhvvhvvhhhvvhvhhvhvvhhhvvhvvhhvhvhvhvhhhhhvvhvvvvhvvvhhhhhvhvvvvvhvhvhhvvhvvhvhhhhvvhhhhvhvvvhhvhhvvhhvhvhvvhhvhhhvhvhhvvhhvvhhvvhvvhhhvvhvvvhhvhhhvvhhvvhvvvhvhhhvhvvvvvhvhvhhhhhvvvhhvhhvvhvhhvhvvvhvvhhhvvhvhhhvvvhvhhhhvvhhvvhvhhvhvvhhvvhhvvhvvvvhhvhvhvvvvvhvhhhhvhhvvvhhvhhvvvhvhvhvvvhvhhhvvhhvhvhvhhhvvhhhvvhhhhhvvvhhvhhvvhhhvvhhvvhhvvhvhvvvvvhvhvhvvvhhvvhhhvhvvhvvvhhvhhvvvhhhvvhhvvhvvvhhvhhvhvvvvvhvhhhhvvhhvvhhhhhvvhhvhhhhvvhhvvhvvvvvhv`
- 
![image](https://github.com/user-attachments/assets/8ed24e5a-84fc-44b6-b8b2-49f0f77ec205)

  i kept the same code but just changed the input string so i mapped h=0 and v = 1
```python

  def bb84_to_bits(bb84_string):
    # Map '+' to '1' and 'x' to '0'
    return ''.join('1' if char == 'v' else '0' for char in bb84_string)

bb84_string = "hvvhhvvvhhvvhhvvhvhvvvvvhvhhhvhhhvhhvhhhhvhvvvvvhvhhhhhvhvvhvvhhhvvhvhhvhvvhhhvvhvvhhvhvhvhvhhhhhvvhvvvvhvvvhhhhhvhvvvvvhvhvhhvvhvvhvhhhhvvhhhhvhvvvhhvhhvvhhvhvhvvhhvhhhvhvhhvvhhvvhhvvhvvhhhvvhvvvhhvhhhvvhhvvhvvvhvhhhvhvvvvvhvhvhhhhhvvvhhvhhvvhvhhvhvvvhvvhhhvvhvhhhvvvhvhhhhvvhhvvhvhhvhvvhhvvhhvvhvvvvhhvhvhvvvvvhvhhhhvhhvvvhhvhhvvvhvhvhvvvhvhhhvvhhvhvhvhhhvvhhhvvhhhhhvvvhhvhhvvhhhvvhhvvhhvvhvhvvvvvhvhvhvvvhhvvhhhvhvvhvvvhhvhhvvvhhhvvhhvvhvvvhhvhhvhvvvvvhvhhhhvvhhvvhhhhhvvhhvhhhhvvhhvvhvvvvvhv"

binary = bb84_to_bits(bb84_string)

print(binary)
```

![image](https://github.com/user-attachments/assets/664946d6-2bc6-499b-9e88-046cf0379c81)

- and it finally gave it,, the 2nd and final part of the flag: `g3_DH_AlicePop_SharedS3cr3t_Priv4t3K3y_BruteF0rc3_W1nN3r_C0d3}` and by submitting the final part we get the full flag:

![image](https://github.com/user-attachments/assets/195bcf72-8deb-463d-87b0-c1e76dbcdf8e)

### `ASU{BB84Ch4ll3ng3_DH_AlicePop_SharedS3cr3t_Priv4t3K3y_BruteF0rc3_W1nN3r_C0d3}`

