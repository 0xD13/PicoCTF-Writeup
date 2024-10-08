# Easy Peasy
[Challenge](https://play.picoctf.org/practice/challenge/125)

## Solution

題目會給你一個server，連上去後會直接給你flag加密後的結果，再問你想要加密什麼。這題應該就是要透過加密特定資料去推出加密的key是什麼，再還原出flag。這題也有提供server的原始碼，如下：

````python
#!/usr/bin/python3 -u
import os.path

KEY_FILE = "key"
KEY_LEN = 50000
FLAG_FILE = "flag"


def startup(key_location):
	flag = open(FLAG_FILE).read()
	kf = open(KEY_FILE, "rb").read()

	start = key_location
	stop = key_location + len(flag)

	key = kf[start:stop]
	key_location = stop

	result = list(map(lambda p, k: "{:02x}".format(ord(p) ^ k), flag, key))
	print("This is the encrypted flag!\n{}\n".format("".join(result)))

	return key_location

def encrypt(key_location):
	ui = input("What data would you like to encrypt? ").rstrip()
	if len(ui) == 0 or len(ui) > KEY_LEN:
		return -1

	start = key_location
	stop = key_location + len(ui)

	kf = open(KEY_FILE, "rb").read()

	if stop >= KEY_LEN:
		stop = stop % KEY_LEN
		key = kf[start:] + kf[:stop]
	else:
		key = kf[start:stop]
	key_location = stop

	result = list(map(lambda p, k: "{:02x}".format(ord(p) ^ k), ui, key))

	print("Here ya go!\n{}\n".format("".join(result)))

	return key_location


print("******************Welcome to our OTP implementation!******************")
c = startup(0)
while c >= 0:
	c = encrypt(c)

````

先整理一下原始碼：
第48~52行為連上server時的入口，首先會先呼叫`startup`這個function，再來是不斷呼叫`encrypt`這個function
第9~22行為`startup`這個function，從這段可以看出來，加密的方式就是將key取出與flag一樣的長度，逐個位元XOR後即加密完畢（注意`"{:02x}".format(ord(p) ^ k)`代表flag是將字元轉成兩位的16進制數值），這邊我們先連上去看看flag的長度為多少：

```shell
Last login: Tue Jan 16 15:10:05 on ttys000
d13@MacBook ~ % nc mercury.picoctf.net 36449
******************Welcome to our OTP implementation!******************
This is the encrypted flag!
551257106e1a52095f654f510a6b4954026c1e0304394100043a1c5654505b6b

What data would you like to encrypt?
```

flag是長度為32的字元。在執行完`startup`這個function後，就會換成`encrypt`執行，從程式碼34~39行可以看出來他會將不斷的從key中取出與欲加密的資料同樣長度的key來做XOR，若key用完了就會從頭再取一次。

理解做法後要解這題就很快了，首先因為是XOR的關係，根據其相同就會消失的特性。我們拿到第一個密文是c1=flag^key，接下來加密一組長度跟flag一樣的明文c2=text^key，要還原出flag只要c1^c2^text=flag^key^text^key^text就可以了，這邊要注意因為key是循環取用（長度為50000，在第5行可以得知），所以我們要先加密一組長度為50000-32的資料，讓text與flag使用的key是一樣的。

這邊我們使用python產生固定長度的`'a'`：

```shell
d13@MacBook ~ % python3 -c "print('a'*(50000-32)+'\n'+'a'*32)" | nc mercury.picoctf.net 36449
******************Welcome to our OTP implementation!******************
This is the encrypted flag!
551257106e1a52095f654f510a6b4954026c1e0304394100043a1c5654505b6b

What data would you like to encrypt? Here ya go!
1959513d190...太多了也不重要所以省略...3d1900523d1902563d3d1903583d1958513d190505423d1900023d1900073e

What data would you like to encrypt? Here ya go!
034605413d190050083d1951533d1902053d1903003d1902553d190403500f3d
```

c1 = 0x551257106e1a52095f654f510a6b4954026c1e0304394100043a1c5654505b6b

c2 = 0x034605413d190050083d1951533d1902053d1903003d1902553d190403500f3d

text = 'aaaaaaaaaaaaaaaaaaaaaaaa' = 0x6161616161616161616161616161616161616161616161616161616161616161

```python
Python 3.11.4 (v3.11.4:d2340ef257, Jun  6 2023, 19:15:51) [Clang 13.0.0 (clang-1300.0.29.30)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> c1=0x551257106e1a52095f654f510a6b4954026c1e0304394100043a1c5654505b6b
>>> c2=0x034605413d190050083d1951533d1902053d1903003d1902553d190403500f3d
>>> text=0x6161616161616161616161616161616161616161616161616161616161616161
>>> '{:x}'.format(c1^c2^text)
'3735333032623338363937613837313766306661656539633066643336613537'

```

將`'3735333032623338363937613837313766306661656539633066643336613537'`轉換成ASCII即可得到flag

## Flag

picoCTF{75302b38697a8717f0faee9c0fd36a57}