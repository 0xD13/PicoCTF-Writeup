# New Caesar
[Challenge](https://play.picoctf.org/practice/challenge/158)

## Solution

題目有給加密的原始碼，核心概念就是透過進位轉換數字，再用Key做位移。底下說明一下這兩個步驟及核心程式碼。

首先，程式會把明文轉成2進制的，再以4碼（0000~1111）為一個整數轉回十進位數字，並以英文字母前16個的方式儲存，也就是原始碼中的b16_encode function：

```python
def b16_encode(plain):
    enc = ""
    for c in plain:
        # 把明文轉成8位的二進制形式 ex A = 65 = 01000001
        binary = "{0:08b}".format(ord(c))
        # 將前四碼轉成int, 以A為例會變成4跟1最後enc = 'eb'
        # int(input, base) 後面參數為input的進制格式, default =10
        enc += ALPHABET[int(binary[:4], 2)] 
        enc += ALPHABET[int(binary[4:], 2)]
    return enc
```

再將`enc`與key做位移，位移的範圍一樣是前16個字母（a-p）

```python
def shift(c, k):
    t1 = ord(c) - LOWERCASE_OFFSET
    t2 = ord(k) - LOWERCASE_OFFSET
    return ALPHABET[(t1 + t2) % len(ALPHABET)]
```

原始碼中有提到key的長度為1且範圍是a-p（如下方code），所以這題只要寫一個逆向解密的程式，並嘗試16組key即可還原出flag。

```python
assert all([k in ALPHABET for k in key])
assert len(key) == 1
```

我寫的解密程式：

```python
import string

LOWERCASE_OFFSET = ord("a")
ALPHABET = string.ascii_lowercase[:16]  # abcdefghijklmnop

def b16_decode(encoded):
    dec = ""
    for i in range(0, len(encoded), 2):
        first_half = "{0:04b}".format(ALPHABET.index(encoded[i]))
        second_half = "{0:04b}".format(ALPHABET.index(encoded[i+1]))
        binary = first_half + second_half
        dec += chr(int(binary, 2))
    return dec

def shift_decrypt(c, k):
    t1 = ord(c) - LOWERCASE_OFFSET
    t2 = ord(k) - LOWERCASE_OFFSET
    return ALPHABET[(t1 - t2) % len(ALPHABET)]

secret = "mlnklfnknljflfjljnjijjmmjkmljnjhmhjgjnjjjmmkjjmijhmkjhjpmkmkmljkjijnjpmhmjjgjj"
key = "k"
assert all([k in ALPHABET for k in key])
assert len(key) == 1

for key in ALPHABET:
    enc = ""
    for i, c in enumerate(secret):
        enc += shift_decrypt(c, key[i % len(key)])
    b16 = b16_decode(enc)
    print(b16)
```

解出來的16組答案

```
a: ËÚµÚÛµÌËÇÊÈÊÊÊËÇÉ
b: ºÉ¤ÉÊ¤»º¶¹·¹¹¹º¶¸
c: ©¸¸¹sy{vwªx©{u¥t{wz¨w¦u¨u}¨¨©xv{}¥§tw
d: §§¨bhjefgjdcjfifddlgejlcf
e: qQqWYTUVYSRYUXUSS[VTY[RU
f: v`@`FHCDwEvHBrAHDGuDsBuBJuuvECHJrtAD
g: et_tu?_5723f4e71a0736d3b1d19dde4279ac03
h: TcNcd.N$&!"U#T& P/&"%S"Q S (SST#!&(PR/"
i: CR=RS=DCOB@BBBCOA
j: 2A,AB,
k: !001ûñóþÿ"ð!óý-üóÿò ÿ.ý ýõ  !ðþóõ-/üÿ
l: /
/ ê
àâíîïâìëâîáîììäïíâäëî
m: ùÙùßÑÜÝ
```

挑可以正常還原的就是flag了。

## Flag

picoCTF{et_tu?_5723f4e71a0736d3b1d19dde4279ac03}

