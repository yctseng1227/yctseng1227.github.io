# [Malware Analysis] 難道這是免費可以看的嗎？ RedLine InfoStealer


<u style="color:red;">你已成為盜版的受害者。</u>  
雖然 GitHub 提倡的開源文化是可受大眾公開檢視，但不代表就是 100% 安全。

<!--more-->

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063779/blog-posts/2023-08/26-google-search-free-download.webp)

以上截圖是用 Google Search `Free-Download-2023 site:github.com` 找到的一小部分 repo，沒意外裡面都埋藏惡意軟體，這些可疑 repo 的特色是出現時間很短，大概兩三天就會消失，一旦下載後執行就會有資料外洩的疑慮，雖然客家人都不太喜歡買正版，但找盜版也要注意安全R

## What's Happen

OK，事情的發生就是身邊朋友在 8/18 從類似上述的 repo 下載到惡意軟體，結果沒幾天信箱開始收到異常登入的通知，原本的 [repo](https://github.com/Munganyende/MOVAVI-VIDEO-EDITOR-PLUS-CRACK-2023-14737) 在我接到消息時，發現 Owner 已經關帳號了，可見有多可疑==

之後我用相同的關鍵字找到類似的 repo（但寫文當下這 Owner 也關閉了），截圖如下。

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063779/blog-posts/2023-08/26-malicious-repo-screenshot-1.webp)


除了 README 看起來就很可疑，另外注意到右上的 Star 居然有 54 顆 （~~我好羨慕~~），仔細看裡面每個點星星的帳號也都是做類似的散播惡意程式，只是換個不同的商業軟體名稱，內容大同小異。

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063779/blog-posts/2023-08/26-malicious-repo-screenshot-2.webp)

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063779/blog-posts/2023-08/26-malicious-repo-stargazers.webp)


點下 Download 後會連去 Godaddy 的網址，然後轉址到 discordapp 的連結，攻擊者似乎是統一放在 Discord 再拿連結出來散播，而這兩者都是合法的網路平台，實在防不勝防。
- hxxps[:]//launchersoftware.godaddysites[.]com/
- hxxps[:]//cdn.discordapp[.]com/attachments/1042051714754289756/1143174795983343656/Setup.zip

## Malware Analysis

把壓縮檔 unzip (password: 8848) 後可以看到不少檔案，雖然事隔多日當事人對檔案表示沒什麼印象，就姑且拿來看看吧 : 3

![](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_800/v1693063779/blog-posts/2023-08/26-malware-directory.webp)

入口點不意外就是那個 Setup.exe，但是用 IDA 追了一陣子沒看出甚麼端倪，倒是注意到 Exports 那邊都是以 go 為開頭的函式，拿去 Google 後可以找到 [GLFW](https://github.com/go-gl/glfw) 這個專案，所以可以知道這隻 .exe 是用 Golang 編譯的，但是在 function list 中並沒有看到 main.main 這類的 Golang 特徵，同時用 Mandiant 的 [GoReSym](https://github.com/mandiant/GoReSym) 也沒有還原出其他東西 （error message: `Failed to parse file: failed to read pclntab: failed to locate pclntab`），用 [go_parser](https://github.com/0xjiayu/go_parser) 拆也得到 Exception: `Failed to find firstmoduledata address!`，感覺該找個時間研究怎麼拆 Golang-based program（沉默）

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063779/blog-posts/2023-08/26-malware-ida-export.webp)


既然靜態看不懂，那就直接開 Debugger 跑動態，發現他會 drop 出一隻 .NET 後門 `%AppData%\Local\Temp\dfb5cc44a3e2a1762bc0f637ae29ffb43353755425`，並且在執行結束後自刪。

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063779/blog-posts/2023-08/26-debugger-backdoor-entry.webp)

用 [CFF Explorer](https://ntcore.com/?page_id=388) 打開注意到該檔案 internal name 為 `Radiogram.exe` ，在 VirusTotal 上搜尋 `name:Radiogram.exe` 發現樣本還不少，而且大部分的 engine 的 detection name 為 **RedLine Stealer**。

簡單用 [dnSpy](https://github.com/dnSpy/dnSpy) 瞄了一下，有些奇怪的 function name 居然是用**哈薩克文**寫的（by Google Translate），像是下面這個 function name 意思是 "check bot"，可以知道在以下條件會觸發 sleep 並結束程式。

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063778/blog-posts/2023-08/26-check-bot-algorithm.webp)

另外就是從網路上取得後續的 payload ，正好該有的資訊都寫在裡面，照著走會得到一個網址 hxxps[:]//pastebin[.]com/raw/tnW31tPp ，並且可以把裡面的 payload 抓下來。

- Config

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063778/blog-posts/2023-08/26-backdoor-config.webp)

- Decryption algorithm

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063780/blog-posts/2023-08/26-payload-decrypt-algorithm.webp)

- CyberChef

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063779/blog-posts/2023-08/26-decrypt-pastebin-link.webp)

pastebin.com 是個公開程式碼分享平台，毫不意外地被攻擊者拿來當作後續攻擊的媒介。  
而目前該網址 /tnW31tPp 已被通報，所以下架了。

> Not Found (#404)  
> This paste has been deemed potentially harmful. Pastebin took the necessary steps to prevent access on August 25, 2023, 12:55 am CDT. If you feel this is an incorrect assessment, please contact us within 14 days to avoid any permanent loss of content.

而我們拿到的 payload  `Nj9QUisHPA83L1AfKwc8ETcsBh4oLQ1TGg9UWg==` 可以用同一組 XOR Key 得到最後的 C2 server => moy.tapoq[.]top:28786 (IP: 95.216.180[.]12)。

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693067852/blog-posts/2023-08/26-decrypt-c2-server.webp)

不過有趣的是，同一個 /tnW31tPp 網址從 VirusTotal 可以找到不同的 payload ，表示攻擊者一直都是用相同網址去更新 C2 server 的位址，從收集來的 payload 可以再找到幾台 C2 server。
- ley.domest[.]top:28786 (IP: 95.216.180[.]12)
- gog.shopapi[.]top:28786 (IP: 95.216.180[.]12)
- m6o.braavaw[.]top:28786 (IP: 185.229.64[.]67)

共通點除了都是使用 Port 28786，以及註冊便宜的 .top TLD ，其中還有兩台跟本案的 C2 server 是同一組 IP。

<br><br>

其他一些功能從 function name 就能略知一二的了，並且大部分的 command 都有簡易混淆。

- CryptoHelper
    - 觀察到有疑似摳解密的 API ，猜測可能是在機器上把撿到的 credential 解完再傳回 C2 server，圖中的各種 `MSValue*` 可以對應到下方網路流量的使用，總之就是各種偷 `login data`, `cookies`, `Web Data`, `localprefs.json`, ...etc.。
    - ![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693240586/blog-posts/2023-08/26-possible-decrypt-credential-function.webp)

- SystemInfoHelper
    - GetProcessors (`SELECT * FROM Win32_Processor`)
    - GetGraphicCards (`SELECT * FROM Win32_VideoController`)
    - GetBrowsers (check `SOFTWARE\\WOW6432Node\\Clients\\StartMenuInternet`, `SOFTWARE\\Clients\\StartMenuInternet`)
    - GetSerialNumber (`SELECT * FROM Win32_DiskDrive`)
    - ListOfProcesses (`SELECT * FROM Win32_Process Where SessionId=`)
    - GetVs (check `AntivirusProduct`, `AntiSpyWareProduct`, `FirewallProduct`)
    - GetProcessesByName (`SELECT * FROM Win32_Process Where SessionId='`)
    - ListOfPrograms (check `SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Uninstall`)
    - AvailableLanguages
    - CollectMemory (`SELECT * FROM Win32_OperatingSystem`)
    - GetWindowsVersion (check `SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion`)



最後，從網路流量來看... 嗯 應該也不太需要多做解釋了。

紅字是電腦發出去的 request，藍字則是對方下達的指令，可以看到基本上裝置裡所有文字檔、錢包位址等等都會被傳回去，其他一些存在各式瀏覽器的資訊，還有針對加密貨幣的 plugin，這些被拿走就真的很麻煩呢QQ

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063780/blog-posts/2023-08/26-wireshark-screenshot-1.webp)

![](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1693063780/blog-posts/2023-08/26-wireshark-screenshot-2.webp)

↓↓↓ 簡單整理如下 ↓↓↓

```text
%USERPROFILE%\Desktop|*.txt,*.doc*,*.rdp,*.key*,*wallet*,*seed*|0
%USERPROFILE%\Documents|*.txt,*.doc*,*.rdp,*key*,*wallet*,*seed*|0
%appdata%\binance|*.fp*|0.E...c.
%USERPROFILE%\AppData\Local\Battle.net
%USERPROFILE%\AppData\Local\Chromium\User Data
%USERPROFILE%\AppData\Local\Google\Chrome\User Data
%USERPROFILE%\AppData\Local\Google(x86)\Chrome\User Data
%USERPROFILE%\AppData\Roaming\Opera Software\
%USERPROFILE%\AppData\Local\MapleStudio\ChromePlus\User Data
%USERPROFILE%\AppData\Local\Iridium\User Data
%USERPROFILE%\AppData\Local\7Star\7Star\User Data
%USERPROFILE%\AppData\Local\CentBrowser\User Data
%USERPROFILE%\AppData\Local\Chedot\User Data
%USERPROFILE%\AppData\Local\Vivaldi\User Data
%USERPROFILE%\AppData\Local\Kometa\User Data
%USERPROFILE%\AppData\Local\Elements Browser\User Data
%USERPROFILE%\AppData\Local\Epic Privacy Browser\User Data
%USERPROFILE%\AppData\Local\uCozMedia\Uran\User Data
%USERPROFILE%\AppData\Local\Fenrir Inc\Sleipnir5\setting\modules\ChromiumViewer
%USERPROFILE%\AppData\Local\CatalinaGroup\Citrio\User Data
%USERPROFILE%\AppData\Local\Coowon\Coowon\User Data
%USERPROFILE%\AppData\Local\liebao\User Data
%USERPROFILE%\AppData\Local\QIP Surf\User Data
%USERPROFILE%\AppData\Local\Orbitum\User Data
%USERPROFILE%\AppData\Local\Comodo\Dragon\User Data
%USERPROFILE%\AppData\Local\Amigo\User\User Data
%USERPROFILE%\AppData\Local\Torch\User Data
%USERPROFILE%\AppData\Local\Yandex\YandexBrowser\User Data
%USERPROFILE%\AppData\Local\Comodo\User Data
%USERPROFILE%\AppData\Local\360Browser\Browser\User Data
%USERPROFILE%\AppData\Local\Maxthon3\User Data
%USERPROFILE%\AppData\Local\K-Melon\User Data
%USERPROFILE%\AppData\Local\Sputnik\Sputnik\User Data
%USERPROFILE%\AppData\Local\Nichrome\User Data
%USERPROFILE%\AppData\Local\CocCoc\Browser\User Data
%USERPROFILE%\AppData\Local\Uran\User Data
%USERPROFILE%\AppData\Local\Chromodo\User Data
%USERPROFILE%\AppData\Local\Mail.Ru\Atom\User Data
%USERPROFILE%\AppData\Local\BraveSoftware\Brave-Browser\User Data
%USERPROFILE%\AppData\Local\Microsoft\Edge\User Data
%USERPROFILE%\AppData\Local\NVIDIA Corporation\NVIDIA GeForce Experience
%USERPROFILE%\AppData\Local\Steam
%USERPROFILE%\AppData\Local\CryptoTab Browser\User Data
%USERPROFILE%\AppData\Roaming\Mozilla\Firefox
%USERPROFILE%\AppData\Roaming\Waterfox
%USERPROFILE%\AppData\Roaming\K-Meleon
%USERPROFILE%\AppData\Roaming\Thunderbird
%USERPROFILE%\AppData\Roaming\Comodo\IceDragon
%USERPROFILE%\AppData\Roaming\8pecxstudios\Cyberfox
%USERPROFILE%\AppData\Roaming\NETGATE Technologies\BlackHaw
%USERPROFILE%\AppData\Roaming\Moonchild Productions\Pale Moon
%appdata%E%E'E...ArmoryE#..*.walletE%....E!E...AtomicE#.
%appdata%E%E'E...atomicE#..*E%....E!E...BinanceE#.
%appdata%E%E'E...BinanceE#..*app-store*E%....E!E...CoinomiE#..%localappdata%E%E'E...Coinomi\Coinomi\CacheE#..*E%..E'E...Coinomi\Coinomi\dbE#..*E%..E'E...Coinomi\Coinomi\walletsE#..*E%....E!E...ElectrumE#.
%appdata%E%E'E...Electrum\walletsE#..*E%....E!E...EthereumE#.
%appdata%E%E'E...Ethereum\walletsE#..*E%....E!E...ExodusE#.
%appdata%E%E'E...Exodus\exodus.walletE#..*E%..E'E...ExodusE#..*.jsonE%....E!E...GuardaE#.
%appdata%E%E'E...GuardaE#..*E%....E!E...JaxxE#.
%appdata%E%E'E...com.liberty.jaxxE#..*E%....E!E...MoneroE#..
%USERPROFILE%\DocumentsE%E'E...Monero\walletsE#..*E%.....E)...
ffnbelfdoeiohenkjibnmadjiehjhajb|YoroiWallet
ibnejdfjmmkpcnlpebklmnkoeoihofec|Tronlink
jbdaocneiiinmjbjlgalhcelgbejmnid|NiftyWallet
nkbihfbeogaeaoehlefnkodbefgpgknn|Metamask
afbcbjpbpfadlkmhmclhkeeodmamcflc|MathWallet
hnfanknocfeofbddgcijnmhnfnkdnaad|Coinbase
fhbohimaelbohpjbbldcngcnapndodjp|BinanceChain
odbfpeeihdkbihmopkbjmoonfanlbfcl|BraveWallet
hpglfhgfnhbgpjdenjgmdgoeiappafln|GuardaWallet
blnieiiffboillknjnepogjhkgnoapac|EqualWallet
cjelfplplebdjjenllpjcblmjkfcffne|JaxxxLiberty
fihkakfobkmkjojpchpfgcmhfjnmnfpi|BitAppWallet
kncchdigobghenbbaddojjnnaogfppfj|iWallet
amkmjjmmflddogmhpjloimipbofnfjih|Wombat
fhilaheimglignddkjgofkcbgekhenbh|AtomicWallet
nlbmnnijcnlegkjjpcfjclmcfggfefdm|MewCx
nanjmdknhkinifnkgdcggcfnhdaammmj|GuildWallet
nkddgncdjgjfcddamfgcmfnlhccnimig|SaturnWallet
fnjhmkhhmkbjkkabndcnnogagogbneec|RoninWallet
aiifbnbfobpmeekipheeijimdpnlpgpp|TerraStation
fnnegphlobjdpkhecapkijjdkgcjhkib|HarmonyWallet
aeachknmefphepccionboohckonoeemg|Coin98Wallet
cgeeodpfagjceefieflmdfphplkenlfk|TonCrystal
pdadjkfkgcafgbceimcpbkalnfnepbnk|KardiaChain
bfnaelmomeimhlpmgjnjophhpkkoljpa|Phantom
fhilaheimglignddkjgofkcbgekhenbh|Oxygen
mgffkfbidihjpoaomajlbgchddlicgpn|PaliWallet
aodkkagnadcbobfpggfnjeongemjbjca|BoltX
kpfopkelmapcoipemfendmdcghnegimn|LiqualityWallet
hmeobnfnfcmdkdcmlblgagmfpfboieaf|XdefiWallet
lpfcbjknijpeeillifnkikgncikgfhdo|NamiWallet
dngmlblcodfobpdpecaadgfbcggfjfnm|MaiarDeFiWallet
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AMAZON_AWS_ACCESS_KEY_ID
AMAZON_AWS_SECRET_ACCESS_KEY
ALGOLIA_API_KEY
AZURE_CLIENT_ID
AZURE_CLIENT_SECRET
AZURE_USERNAME
AZURE_PASSWORD
MSI_ENDPOINT
MSI_SECRET
binance_api
binance_secret
BITTREX_API_KEY
BITTREX_API_SECRET
CF_PASSWORD
CF_USERNAME
CODECLIMATE_REPO_TOKEN
COVERALLS_REPO_TOKEN
CIRCLE_TOKEN
DIGITALOCEAN_ACCESS_TOKEN
DOCKER_EMAIL
DOCKER_PASSWORD
DOCKER_USERNAME
DOCKERHUB_PASSWORD
FACEBOOK_APP_ID
FACEBOOK_APP_SECRET
FACEBOOK_ACCESS_TOKEN
FIREBASE_TOKEN
FOSSA_API_KEY
GH_TOKEN
GH_ENTERPRISE_TOKEN
CI_DEPLOY_PASSWORD
CI_DEPLOY_USER
GOOGLE_APPLICATION_CREDENTIALS
GOOGLE_API_KEY
CI_DEPLOY_USER
CI_DEPLOY_PASSWORD
GITLAB_USER_LOGIN
CI_JOB_JWT
CI_JOB_JWT_V2
CI_JOB_TOKEN
HEROKU_API_KEY
HEROKU_API_USER
MAILGUN_API_KEY
MCLI_PRIVATE_API_KEY
MCLI_PUBLIC_API_KEY
NGROK_TOKEN
NGROK_AUTH_TOKEN
NPM_AUTH_TOKEN
OKTA_CLIENT_ORGURL
OKTA_CLIENT_TOKEN
OKTA_OAUTH2_CLIENTSECRET
OKTA_OAUTH2_CLIENTID
OKTA_AUTHN_GROUPID
OS_USERNAME
OS_PASSWORD
PERCY_TOKEN
SAUCE_ACCESS_KEY
SAUCE_USERNAME
SENTRY_AUTH_TOKEN
SLACK_TOKEN
square_access_token
square_oauth_secret
STRIPE_API_KEY
STRIPE_DEVICE_NAME
SURGE_TOKEN
SURGE_LOGIN
TWILIO_ACCOUNT_SID
CONSUMER_KEY
CONSUMER_SECRET
TRAVIS_SUDO
TRAVIS_OS_NAME
TRAVIS_SECURE_ENV_VARS
VAULT_TOKEN
VAULT_CLIENT_KEY
TOKEN
VULTR_ACCESS
VULTR_SECRET
```

## In Closing
根據一些在網路上的資料，沒意外這隻就是 **RedLine Stealer** 了，原本想說哪可能這麼容易就撿到惡意程式，沒想到這查下去就是一串粽子XDDD

不過根據中獎的那位朋友表示，這陣子除了一直收到可疑的登入通知，在 Steam 方面即便之前就綁 Steam Guard ，但關鍵時刻卻還是沒有跳出來 2FA ，帳號就這樣直接被拿去亂傳訊息，在猜想是否因為存在瀏覽器的資訊被拿走，讓攻擊者可以直接用 session 做登入嗎，嗯 ... 好微妙。

至於事發兩天後我用同樣的 free-download 關鍵字在 Google Search 看到其他 GitHub repo 以不同的壓縮密碼散播 CPP-based 的惡意程式，那又是另一個故事了...

## Reference
- https://www.securityweek.com/takedown-of-github-repositories-disrupts-redline-malware-operations/
- https://c3rb3ru5d3d53c.github.io/2022/11/redline-stealer/
- https://www.esentire.com/blog/esentire-threat-intelligence-malware-analysis-redline-stealer
- https://blog.eclecticiq.com/redline-stealer-variants-demonstrate-a-low-barrier-to-entry-threat
- https://web.archive.org/web/20230606224056/https://apophis133.medium.com/redline-technical-analysis-report-5034e16ad152
- https://www.trendmicro.com/en_us/research/23/e/malicious-ai-tool-ads-used-to-deliver-redline-stealer.html
- [駭客將可竊取機密資訊的RedLine Stealer偽裝成Windows 11更新程式](https://www.ithome.com.tw/news/149295)
- [習慣用 Chrome 記住密碼，遠端工作恐成駭客破口](https://technews.tw/2022/01/04/chrome-edge-password/)


## IOCs
| Indicator | Description |
| :-------- | :---------- |
| f9186767c98490db03a70f511cb24a84eb906c94 | Setup.zip |
| ab741ccd807f6645c15aa9d69666b04ea8e6d408 | Setup.exe |
| 73ceacde9d7b7ede1c4b7615c1bd5ae2ace45b97 | Radiogram.exe (filename: dfb5cc44a3e2a1762bc0f637ae29ffb43353755425) |
| hxxps[:]//launchersoftware.godaddysites[.]com/ | The link redirect to discord link|
| hxxps[:]//cdn.discordapp[.]com/attachments/<br>1042051714754289756/1143174795983343656/Setup.zip | Download link |
| hxxps[:]//pastebin[.]com/raw/tnW31tPp | The site to store subsequent payload |
| moy.tapoq[.]top:28786 | C2 Server |
| 95.216.180[.]12 | C2 Server |

### Suspicious GitHub Users
```text
@hiinoo01aiiu, Joined on Aug 29, 2021
@MinhGachaCoder, Joined on Mar 12, 2023
@elvisEam, Joined on Jun 8, 2022
@MarkRoi, Uganda - Kampala
@Mr-krostopbey1903, Joined on Jan 29, 2021
@tccpro, Freelance @Shopify
@qarixaeed1, Joined on Aug 1, 2022
@Kashif818Mehmood, Joined on Jun 28, 2020
@TGRedDragon, Joined on Mar 19, 2023
@llstevenll, Joined on Oct 17, 2022
@RobinHuaman, Joined on Sep 4, 2021
@Masterkent11, Joined on Jul 9, 2019
@laoluibitoye, Joined on Jul 25, 2019
@gl0nDbeatz, Joined on Jul 28, 2023
@Issoufouiha, Niamey
@nazadam, Joined on Sep 27, 2022
@A-mal, Joined on Jun 6, 2021
@X-vinjin, Joined on Dec 6, 2022
@theillumina, Joined on Mar 26, 2023
@Sazzadur1991, Joined on Aug 4, 2023
@GitTrio3, Joined on Jun 7, 2023
@UnknownKingsy, Joined on Jan 25, 2021
@Kyori4k, Joined on Oct 28, 2022
@Developwithjaspreet, wpcodekit
@ajivijay, Joined on Jul 1, 2023
@soporteint2023, Joined on Apr 27, 2023
@KojoClinton, Joined on Jul 10, 2023
@intel1337, Selfish
@Kirarik21, Joined on Jun 10, 2023
@Emrx29y, Joined on Feb 26, 2023
@Clemens17, Joined on Nov 15, 2022
@Elissonamarodasilva, Joined on Feb 11, 2023
@Mcbrightyves, Joined on Oct 17, 2022
@wxmcode, Joined on Sep 3, 2019
@Aramayis1996, Joined on Nov 27, 2022
@yuanxiangliu, Joined on Aug 18, 2022
@ohTraveler, Joined on Aug 15, 2023
@Ladicor, Joined on Mar 1, 2022
@alexanderutmn, Joined on Apr 1, 2017
@guru9999, Joined on Aug 6, 2017
@MakareB, Joined on May 12, 2021
@olikke, Joined on Dec 11, 2018
@twirus7, Joined on Apr 13, 2023
@BabyNelo, Joined on Mar 21, 2023
@saintsblack, Joined on Mar 4, 2023
@un243alexxx, Joined on Nov 29, 2020
@gingazov2010, Joined on Nov 9, 2019
@dasdeutchguy, Joined on May 28, 2023
```

