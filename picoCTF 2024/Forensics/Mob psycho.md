
# Mob psycho
[Challenge](https://play.picoctf.org/practice/challenge/420)

## Description

Can you handle APKs?Download the android apk [here](https://artifacts.picoctf.net/c_titan/53/mobpsycho.apk).

## Hints

1. Did you know you can `unzip` APK files?
2. Now you have the whole host of shell tools for searching these files.

## Solution

我覺得這題的提示太大了，基本上就是解壓縮，找到flag檔案而已，最後再把hex轉回文字就好。

```bash
┌──(kali㉿kali)-[~]
└─$ cd Downloads                
                                                                             
┌──(kali㉿kali)-[~/Downloads]
└─$ unzip mobpsycho.apk 

┌──(kali㉿kali)-[~/Downloads]
└─$ find -name 'flag.txt'
./res/color/flag.txt
                                                                             
┌──(kali㉿kali)-[~/Downloads]
└─$ cat res/color/flag.txt 
7069636f4354467b6178386d433052553676655f4e5838356c346178386d436c5f61336562356163327d
```