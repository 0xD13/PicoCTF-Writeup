# ARMssembly 0

[Challenge](https://play.picoctf.org/practice/challenge/160)

## Solution

題目給了兩個輸入跟一隻程式。

輸入：4004594377, 4110761777

部分程式：

```assembly
func1:
	sub	sp, sp, #16
	str	w0, [sp, 12]
	str	w1, [sp, 8]
	ldr	w1, [sp, 12]
	ldr	w0, [sp, 8]
	cmp	w1, w0
	bls	.L2
	ldr	w0, [sp, 12]
	b	.L3
```

對，我只看了部分的程式碼，因為我逆向很爛，這裡決定用猜的。畢竟是40分的題目，算是初級題目，另外提示也說了Simple compare，那我想應該跟輸入的兩個數字做一些運算脫不了關係，我決定只看唯一的function。

這邊我幾乎都不會，我只知道cmp可以做比大小，bls是用來跳轉的，所以我猜應該就是挑大的輸出或是小的。另外題目有說答案格式要用16進位全小寫作為flag，很幸運的被我繞過一題。

## Flag

picoCTF{f5053f31}