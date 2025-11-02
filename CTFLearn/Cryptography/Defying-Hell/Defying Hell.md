# Defying Hell - Writeup

## Challenge description
The Challenge, **Defying Hell** involves data key exchange protocol.

### Challenge prompt
<img width="787" height="691" alt="image" src="https://github.com/user-attachments/assets/1084ea2f-632c-4445-950b-3e79dacfcf12" />

## My Approach And Thinking Process 
- Opening the `data.txt` given in the challenge i found
<img width="253" height="138" alt="image" src="https://github.com/user-attachments/assets/69bfd524-fcdd-413e-843c-abf8dd8f68e8" />

Which Points me (along with the challenge name) to Diffie-Hellman Key exchange

## Diffe-Hellman Explanation
<img width="253" height="138" alt="image" src="https://github.com/user-attachments/assets/971ab44c-8f11-4b0b-a2c8-4c3b1b915200" />


- First of all `p` and `g` are public parameters
- `A` and `B` are public keys generated using `a` and `b` respectively
- `p` is a prime number, and all the mathematics and arithmetic is done modulo `p`
- `g` is a generator
- `a` is Alice's private integer, and `b` is Bob's secret integer
- Alice computes `A` using (g^a) % p (generator to the power of secret key alice) % (prime) 
- Bob Computes `B` using (g^b) % p (generator to the power of secret key bob) % (prime)

### Shared Secret
- Alice Computes:

  $K_a = B^a \pmod{p} \xrightarrow{\text{simplify}} (g^a)^b \pmod{p} \longrightarrow g^{ab} \pmod{p} \longrightarrow g^{ab} \pmod{p}$
  
  $K_b = A^b \pmod{p} \xrightarrow{\text{simplify}} (g^b)^a \pmod{p} \longrightarrow g^{ba} \pmod{p} \longrightarrow g^{ab} \pmod{p}$

- We notice that `K_a` and `K_b` are the same
- Essentialy we figure out that if we manage to find the Public key `A` and the private key `b` we can break Diffie-Hellman and get the shared secret

## Breaking Diffie-Hellman
- To recover Alice's private key `a` from public values ( `g` , `p` ,`A`) i used discrete logarithm problem (DLP):
- Find integer x such that g ^ x  = A mod p
- This x is the discrete logarithm log_g A % p and once we have `a` or `b` we can find k for either one using:
- $k_a/k_b = B^a (% p)
- So our goal is to : compute the discrete log

## My Solution
- We have `p` , `g` , `A` and `B`
- That means we have all the public parameters and we need to find the private parameters ( `a` and `b` ) and after we extract them we can find the Shared Secret
```python
from sympy.ntheory.residue_ntheory import discrete_log
p= 0x8c5378994ef1b 
g= 0x02


A= 0x269beb3b0e968
B= 0x4757336da6f70 

a = discrete_log(p,A,g)
b = discrete_log(p,B,g)
fa = hex(a)[2:]
fb = hex(b)[2:]
abytes=bytes.fromhex(fa)
bbytes=bytes.fromhex(fb)
astr = abytes.decode('utf-8')
bstr= bbytes.decode('utf-8')
print(f"a = {abytes} and b ={bbytes}")


k = pow(B,a,p)
print(f"shared_secret_is {k}")
print(f"Flag is: CTFlearn{{{astr}_{bstr}}}")
```
- I wrote a simple script to find `a` and `b` through discrete logarithmic and changed it from hex to bytes and to UTF-8 strings to be read more clearly since the challenge specified that the flag is the private keys and not the Shared Secret
- Running the script gives me
 ```
a = b'H3ll0' and b =b'Fr13nd'
shared_secret_is 920743183798827
Flag is: CTFlearn{H3ll0_Fr13nd}
```
## Flag  - CTFlearn{H3ll0_Fr13nd}
