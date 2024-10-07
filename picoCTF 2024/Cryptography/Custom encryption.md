# Custom encryption
[Challenge](https://play.picoctf.org/practice/challenge/412)

## Description

Can you get sense of this code file and write the function that will decode the given encrypted file content.Find the encrypted file here [flag_info](https://artifacts.picoctf.net/c_titan/18/enc_flag) and [code file](https://artifacts.picoctf.net/c_titan/18/custom_encryption.py) might be good to analyze and get the flag.

## Hints

Understanding encryption algorithm to come up with decryption algorithm.

## Solution

看加密寫出解密的重點就是，很多地方不需要花力氣研究，只要看加密的過程然後逆寫就好了。以這題為例，前面有很多製作 key 的 code，但解密跟加密要產生的 key 一定要一樣，所以不需要研究。重點只有57, 58行的加密 function，底下將這段程式碼列出：

```python
def dynamic_xor_encrypt(plaintext, text_key):
    cipher_text = ""
    key_length = len(text_key)
    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    return cipher_text

def encrypt(plaintext, key):
    cipher = []
    for char in plaintext:
        cipher.append(((ord(char) * key*311)))
    return cipher

semi_cipher = dynamic_xor_encrypt(plain_text, text_key)
cipher = encrypt(semi_cipher, shared_key)
```

就是對明文和`text_key`做XOR，再把結果跟`shared_key`做乘法。所以解密只需要先除以`shared_key`再跟key做`text_key`就好，程式如下：

```python
def decrypt(cipher, key):
    semi_cipher = []
    for num in cipher:
        semi_cipher.append(chr(num // (key*311)))
    return ''.join(semi_cipher)

def test_decrypt(cipher, text_key):
    p = 97
    g = 31
    if not is_prime(p) and not is_prime(g):
        print("Enter prime numbers")
        return
    a = 94
    b = 29
    print(f"a = {a}")
    print(f"b = {b}")
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return
    decrypted_semi_cipher = decrypt(cipher, shared_key)
    decrypted_text = dynamic_xor_encrypt(decrypted_semi_cipher[::-1], text_key)
    print(f'decrypted text is: {decrypted_text[::-1]}')
```

最後就可以透過解密程式得到flag了~

```Shell
a = 94
b = 29
decrypted text is: picoCTF{custom_d2cr0pt6d_751a22dc}
```