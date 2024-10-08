# GET aHEAD
[Challenge](https://play.picoctf.org/practice/challenge/132)

## Solution

連上它給的網頁，兩個按鈕，試了一下只是改變網頁的css。

![](/img/GETaHEAD_1.png)

提示說使用[Burpsuite](https://portswigger.net/burp)修改發出的請求。用Burp攔截兩個按鈕送出的封包後，發現一個是用GET；另一個用POST，但都沒有發送任何資料。題目用aHEAD我還嘗試修改request的header但也沒什麼結果，突然想起來HTTP methods好像有HEAD可以用，將request的method改成HEAD，response就有回傳flag了！

![](/img/GETaHEAD_2.png)


> [!Tip] 這邊順便複習一下http methods：
> | method | 描述 |
> | --- | --- |
> | GET | 用於讀取資料 |
> | POST | 用於提交資料 |
> | PUT | 也是用於提交資料，但是屬於idempotent（如果一個方法重複執行多次，產生的效果是一樣的）；更接近於更新的概念 |
> | PATCH | 跟PUT類似，但只更新部分資料 |
> | DELETE | 用於刪除資料 |
> | TRACE | 回傳伺服器收到的request，用於測試 |
> | OPTIONS | 確認伺服器可以使用的methods |
> | HEAD | 取得response的標頭 |
> | CONNECT | 建立連結隧道 |

## Flag

picoCTF{r3j3ct_th3_du4l1ty_2e5ba39f}