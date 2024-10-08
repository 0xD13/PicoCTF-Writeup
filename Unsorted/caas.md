# caas
---

## Question
[Challenge](https://caas.mars.picoctf.net/cowsay/Hello%20World)

## Solution

下載下來的js就是這個網站的服務呼叫方式，簡單來說就是呼叫他的api配上參數，會幫你印出一隻講出你參數的牛。

```
 ______
< test >
 ------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

那應該就是要靠injection了，研究了下那個js，試著用單引號或分號的去看看不能不讀取目錄，最後成功找到injection方式跟儲存flag的記事本：
- `https://caas.mars.picoctf.net/cowsay/test;ls`
- `https://caas.mars.picoctf.net/cowsay/test;cat%20falg.txt`

## Flag

picoCTF{moooooooooooooooooooooooooooooooooooooooooooooooooooooooooooo0o}

