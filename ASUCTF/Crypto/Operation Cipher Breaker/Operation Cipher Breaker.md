## Operation Cipher Breaker
i can see why nobody else solved this question 
### Challenge Description

The challenge presented, "Operation Cipher Breaker," is a multi-stage cryptography puzzle. The primary goal was to decrypt an AES encrypted file, which in turn contained a Python script that has  an RSA encryption . Solving the RSA challenge within the decrypted script would give us the flag. While I don't remember the wording of the initial prompt, it explicitly stated that decrypting the provided `.aes` file would be a code (likely Python), and this code would contain an RSA problem that needed to be solved to obtain the flag. This hints at knowing both symmetric and assymetric encodings.

- A `.aes` file named`Python_Code.aes`
- A `netcat` command,.

  ## Netcat and terminal interactions

The challenge begins bn using `netcat`. For those unfamiliar, `netcat` (also called  `nc`) is a powerful command-line utility for network connections. It allows you to read and write data across network connections using TCP or UDP. In this challenge, `netcat` provided the first thing you have to solve.

Upon executing the provided `netcat` command, I was presented with a terminal application prompt. The first prompt asked for the "password length." This was  confusing, as it wasn't immediately clear what password this referred to. My initial attempts involved entering random numbers, which led nowhere. I then tried starting from 1 and increasing. This process continued until I entered `21`, at which point the application proceeded to the next stage. This indicated that the password I needed to guess was exactly 21 characters long.

The next prompt was "guess the password," and it displayed  a series of underscores: ` ---------------`. This indicated that I needed to input the password character by character. As I inputted characters, the interface provided feedback. If a guessed letter was correct and present in the password, it would be revealed in its correct position(s). For instance, if I typed "A" and the password contained the letter 'A' in the 5th and 12th positions, the display might update to look like `----A----A-----`. This interactive feedback mechanism allowed me to  guess letters and deduce the password. Through  trial and error, inputting various letters and observing their placement, I eventually uncovered the complete password: `oracleattackisthebest`.

  ## AES Decryption

With the password obtained from the `netcat`, the next step was to decrypt the `Python_Code.aes` file. AES (Advanced Encryption Standard) is a symmetric-key encryption algorithm, meaning the same key is used for both encryption and decryption. Typically, AES requires both a password (the key) and an Initialization Vector (IV). The IV is a random value that ensures that encrypting the same plaintext multiple times with the same key produces different ciphertexts. This adds an extra layer of security.

However, the challenge description didn't provide any information about an IV. This led me to consider the different modes of operation for AES. After some research, I found the various AES modes and whether they utilize an IV:

```
ECB (Electronic Codebook): Does NOT use an IV.
CBC (Cipher Block Chaining): Uses an IV.
PCBC (Propagating Cipher Block Chaining): Uses an IV.
CFB (Cipher Feedback): Uses an IV.
CTR (Counter): Uses an IV.
OFB (Output Feedback): Uses an IV.
GCM (Galois/Counter Mode): Uses an IV (and an authentication tag).
CCM (Counter with CBC-MAC): Uses an IV (and an authentication tag).
```

Given that an IV was not provided, the most likely AES mode used for encryption was ECB. ECB mode is generally avoided in practice for encrypting more than one block of data because it encrypts each block independently. This can lead to patterns in the ciphertext that reflect patterns in the plaintext, making it vulnerable to certain types of attacks. like the `Penguin attack` which was named like this after the image of the linux penguin

![image](https://github.com/user-attachments/assets/6b408862-642a-4351-9a5c-084a40d6e6a4)


To decrypt the file, I used the `openssl` command-line tool. `openssl` is a versatile and widely used cryptography toolkit. The following command was used for decryption:

```bash
openssl enc -aes-256-ecb -d -in Python_Code.aes -out decrypted_file.py -pass pass:oracleattackisthebest
```

Let's break down this command:

*   `openssl enc`: tells openssl to encrypt or decrypt.
*   `-aes-256-ecb`: Specifies the encryption algorithm as AES with a 256-bit key and the ECB mode of operation.
*   `-d`: means that we want to decrypt.
*   `-in Python_Code.aes`: Specifies the input file as `Python_Code.aes`.
*   `-out decrypted_file.py`: Specifies the output file where the decrypted content will be saved as `decrypted_file.py`.
*   `-pass pass:oracleisthebest`: Provides the decryption password, `oracleattackisthebest`, which was obtained from the `netcat`. The `pass:` prefix indicates that the following string is the password itself.

Executing this command successfully decrypted the AES file, and the resulting `decrypted_file.py` contained a Python script that had an RSA challenge.

  ## RSA Analysis and Solution

The decrypted Python code presented an RSA encryption scenario with some trick. Here's the code:

```python
import random
from Crypto.Util.number import getPrime, bytes_to_long

p = getPrime(512)
q = getPrime(512)
n = p * q
e = 65537

off_p = random.randint(2**127, 2**128)
off_q = random.randint(2**127, 2**128)
m_p = p - off_p
m_q = q - off_q

a = random.randint(2**10, 2**30)
b = random.randint(2**10, 2**30)

c = -(a * (p**2) + b * q)

flag = b"ASU{REDACTED}"
m = bytes_to_long(flag)
ct = pow(m, e, n)

out = {
    'n': n,
    'e': e,
    'ct': ct,
    'a': a,
    'b': b,
    'c': c,
    'm_p': m_p,
    'm_q': m_q
}

for k,v in out.items():
    print(f"{k} = {v}")

"""
ct = 27269193117198408529057603732666981805296994550819334288149326078774442249995062137190695481980142396757301290477026833959002692605149033744458303704512628026322236579587527423170389662441904217640346031398698546066760821519815911416594182643909570963086557581689712265753792131078821710848288011077809649760
a = 398956658
n = 79720563485054306688554767380892915858803248029497392990424655344841018915665658109307763257773067036586920144477403367967705790444346588991617330961045992512279558009642631446878277417385777682242480790313736534918106758165226159499672774935940438724503613897940523207339621376191913605585695224241192007663
e = 65537
b = 1013449699
c = -18751472181131306344848757411333396797307015457096661524610622303315684271752711072755783832868633607087138778801225839362896290775909130634006331173536537974886077170195558986900255772609753615857931854211688093880308892107186038035527869641186419946572916792723937089105559291051678993921935783318656328524730100135
m_p = 6855747695715482747202755101102817503896083596450891824868593446382635050035536327876517791360928938529532601296513430810276412241583367798674807312600479
m_q = 11628281410483626608977070041248810263357067120265838705464116752004012236472241260656775920106898687394314883337175198939599419683261210344782618623753480
print("m_q is estimated to q")
print("!!!Do you really love MATHS!!!")
"""
```

The code generates two large prime numbers, `p` and `q`, and calculates `n` as their product. This is the modulus in RSA. The public exponent `e` is set to the common value 65537. The code then hass some  random offsets (`off_p`, `off_q`) and calculates `m_p` and `m_q`. Importantly, the comment `"m_q is estimated to q"` and the equation `c = -(a * (p**2) + b * q)` are strong hints.

Standard RSA decryption requires knowledge of the private exponent `d`, which is the modular multiplicative inverse of `e` modulo Euler's totient function which is φ(n) = (p-1)(q-1). To calculate `d`, we need the actual values of `p` and `q`.

The comments and the equation for `c` provide the necessary leverage to find `p`. The comment `"!!!Do you really love MATHS!!!"` is a  hint towards using the equation. The equation `c = -(a * (p**2) + b * q)` can be rearranged to solve for `p`,  we have a value for `q`. The code gives us `m_q` and the hint that it's an estimation of `q`. Let's assume that `m_q` is indeed equal to `q`.

Based on this assumption, I created a simple Python script to solve for `p`:

```python
import math

# Given values
a = 398956658
b = 1013449699
c = -18751472181131306344848757411333396797307015457096661524610622303315684271752711072755783832868633607087138778801225839362896290775909130634006331173536537974886077170195558986900255772609753615857931854211688093880308892107186038035527869641186419946572916792723937089105559291051678993921935783318656328524730100135
m_q = 1162828141048362660897707004124881026335706712026583870  # The value of q

p_squared = (-c - b * m_q) / a
p = math.sqrt(p_squared)

print(p)
```

Running this script gave me the value for `p`: `10893116995437082634253255190369177378274560426561720925726293776440233993315026449559534114767485615040563229986718164259825798200675182847225173529438282`.

Now that we have `p` and  `n` is provided, we can find the actual value of `q` using the relationship `n = p * q`:

```python
n = 105103299098661240466673854508923513545612931970107874087894112314830733626105845893880200172657563334552911775135658410874651790888664060152079529728589155690692182746619328876657012617737367997985113503684085439279971330854582767543462678264780620204513026825401567294269308187544074677212768144944773887211
p = 10893116995437082634253255190369177378274560426561720925726293776440233993315026449559534114767485615040563229986718164259825798200675182847225173529438282

q = n // p
print(q)
```

This gave us the true value of `q`. With both `p` and `q` known, we can now solve the RSA challenge using standard methods. One approach is to write a Python script using the `Crypto` library (specifically `Crypto.Util.number` for modular inverse) or to modify the original decrypted file. Here are the two ways :

**Method 1: Editing the Original File**

By uncommenting the provided ciphertext (`ct`), modulus (`n`), and public exponent (`e`) in the Python code and adding the calculated values for `p` and `q`, we can directly decrypt the message within the original script. The core of the decryption process involves calculating the private exponent `d` and then using it to decrypt the ciphertext.

**Method 2: Separate RSA Solver Script**

Alternatively,we can solve it by making a dedicated RSA solver script which is what i did, using only `n`, `e`, `ct`, `p`, and `q`.

```python
from Crypto.Util.number import long_to_bytes, inverse

n = 79720563485054306688554767380892915858803248029497392990424655344841018915665658109307763257773067036586920144477403367967705790444346588991617330961045992512279558009642631446878277417385777682242480790313736534918106758165226159499672774935940438724503613897940523207339621376191913605585695224241192007663
p = 6855747695715482747202755101102817503896083596450891824868593446382635050035536327876517791360928938529532601296513682404041776755685430604374649714328761
q = 11628281410483626608977070041248810263357067120265838705464116752004012236472241260656775920106898687394314883337175198939599419683261210344782618623753480
e = 65537
ct = 27269193117198408529057603732666981805296994550819334288149326078774442249995062137190695481980142396757301290477026833959002692605149033744458303704512628026322236579587527423170389662441904217640346031398698546066760821519815911416594182643909570963086557581689712265753792131078821710848288011077809649760

phi_n = (p - 1) * (q - 1)
d = pow(e, -1, phi_n)
m = pow(ct, d, n)
plaintext = long_to_bytes(m)
print(plaintext)
```

Running either of these scripts with the correct values gives us the decrypted message in bytes. Converting these bytes to a string (often in hexa) reveals the flag.

```
0x4153557b5253415f4b45595f344e445f4145535f4349504845525f305241434c455f323032357d
```

Finally, converting this hexadecimal representation to ASCII reveals the flag: `ASU{RSA_KEY_4ND_AES_CIPHER_0RACLE_2025}`.


![image](https://github.com/user-attachments/assets/bd2d08a0-b436-48b1-8673-b08988111244)
