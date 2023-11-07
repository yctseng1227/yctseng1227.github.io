# [Sharing] 2020 Summer Conf. PART 3


![ ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_800/v1620004322/blog-posts/2020-08/19-cover3.webp)

<!--more-->

事隔一個月後還有另外兩場資安相關的 Conference，雖然時間上不算是 Summer Conf.，但我也沒打算再開新系列介紹，所以就繼續在這篇往下更新了 XD

### CISC 全國資訊安全會議 (9/1 - 9/4) @NSYSU

原本也是預計辦在五月的會議，偉哉武漢肺炎各種大延期，這場的定位對我來說比較特別，身在主辦方校又是以工人的身份下場幫忙。和規模上和前幾場相比贊助商少了許多，整體會議稍微陽春一些，今年是第卅屆雖然我不確定之前是否都差不多，但各位工人們都很認真幫忙這倒是真的 :3

會議時間上長達四天，但實際上議程只有中間 Day 2, Day 3 兩天。  
Day 1 上午場佈、下午由我們 **MacacaHub** 以「基礎網頁安全演練」作為 CISC 開場打頭陣（現在想想我還是不解為何要開 Day 1 的行程 ...），Day 4 則是活動參訪，分別為爬柴山~~給猴子看~~，還有海域純玩水，但由於當時已經有下雨預告的消息，所以取消了 0 人報名的爬山活動（!）。

<br><br>

由於 Day 1 的資安演練是額外活動讓會眾自由報名參加，因此原本 @stavhaygn 設定的目標學生是以大學生/碩士生為主，強調<font color=red>基礎</font>網頁安全演練，結果從後台的表單上看到報名會眾都至少都是碩士生，甚至還有幾位是主管等級讓我們大感意外XD 不過最後證實參與的會眾確實都不是這方面專業的，讓我們連原本預期的進度(`XSS`)都沒時間細講只能匆匆帶過，就這樣~~抱著 @stavhaygn 的腿~~平安度過 Day 1。

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-cisc-1.webp)
> ~~發現野生的奧義間諜~~

Day 2 / Day 3 我的主要工作是和 Lab 學弟搭擋負責 Session A 論文發表的場控/計時/拍照以及協助主持人和 A1 場評審，所以很難花心思認真聽報告QQ 何況我被分配到的 Session 比其他場次的組別硬是多了一組，加上台下 Q&A 實在不受控，想當然爾時間上爆炸不可避ㄏㄏ，工人群組內幾乎只有我回報會晚結束，只能說當個時間管理大師真不容易，老實說只有 10 分鐘要講完一篇研究真的很不夠用，何況包含 Q&A 又要把時間壓得更緊，辛苦各位講者了。

值得一提的還有 Day 2 晚宴在西子灣沙灘會館，雖然從學長的反應看得出應該是已經吃到習以為常了，不過我們也是喝了不少酒（笑）

![ ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_600/v1618836340/blog-posts/2020-08/19-cisc-2.webp)

另外，Day 3 上午有資管系主辦的 CISC CTF，所以我們 **MacacaHub** 就挽起袖子下場一起玩XDDD 整體難度中間偏易，主要有幾題 Pwn 直接被我忽略XD 其中一題原本要考 [CVE-2019-18634](https://www.ithome.com.tw/news/135665)，結果因為所有參賽者共用主機，所以當我 Google 完 CVE 後就直接在主機內下`history` 指令看到前一位參賽者 git pull 下來的 PoC，直接被我拿來提權拿 Flag XDDDDD

還有連續兩題的SQLi，結果同樣`--`的註解在 SQLi-1 可繞過，但在 SQLi-2 不可行，原本以為是空白繞過沒成功（`/**/`），結果賽後討論才發現把註解改成`//`就拿到 flag 了...

原本我一直都穩定位居第四名，最後 40 多分鐘我集中在分析 Windows 的 event log，雖然用事件分析工具花了不少時間把 Powershell 的 command 還原出來但沒有成功執行QQ 賽後聽到 @stavhaygn 用 strings 就把 command dump 出來也沒成功（但至少不用像我還要慢慢還原XD），等回過神結束時才發現掉到第五名，和前一名差不到 50 分... **唔嗯，因為我沒有寫Welcome(50)** 笑死XD 反正最後拿了 **展露頭角獎** 回家~

> 順帶一提前三名: @CSY54、@stavhaygn、@MuMu，全部都是南部資安圈熟面孔自己人 ㄏㄏ

Day 4 本來應該是安排爬山和海域玩水，由於 Day 2 取消了爬山的部分，但因為海域的部分礙於不能取消，所以我們很多工人都被抓下海(??)強制玩水，甚至我明明沒填表單卻還是被秘書姊姊從後台偷偷報名XDDD 剛好拿CTF撿到的500摳家樂福禮券去買了件泳褲 : /


![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-cisc-3.webp)
![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-cisc-4.webp)

當天集合時有幾位會眾出現，可能是誤會有爬山活動、又或是以為只有踩踩海灘，總之他們最後都回頭了:))))  
剩下我們 10 來位被迫參加的工人各種玩風帆、獨木舟、趴板其實也蠻愉快的，而且岸邊似乎還有其他旅行團的遊客有點害羞XD

最後我真的菜逼八沒做好防曬整個曬到爆XD

![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-cisc-5.webp)
![ ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-cisc-6.webp)


### HITCON 臺灣駭客協會 (9/11 - 9/12) @中研院人文社科館

終於輪到我今年預定的最後一場 Conf.，同時是首次和 Lab 各位一起北上參加活動，某方面來說有點像畢業旅行(?)

這次同樣因為偉哉武漢肺炎的影響，在票種上分成線上 & 實體。  
線上的部分是用開源遊戲引擎 minetest 架設的虛擬會場，一整個 minecraft 風格，可惜我這方面並沒有涉獵太深。實體方面就和之前的 Conf. 形式上同樣是以議程為主，只是這場雖然有廠商但沒有大地遊戲，算是省了到處跑的力氣w

![ 南港高鐵票 ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_400/v1618836340/blog-posts/2020-08/19-hitcon-1.webp)

第一次做高鐵到南港終點站還蠻新鮮的... 雖然這裡看起來有點荒涼ㄏ  
飯店的部分我們住洛基大飯店，個人覺得蠻舒適的，說實在身為台北人實在很難得住到在地飯店 XDDD

Day 0 我們在 check in 後晚餐吃 YAYOI，雖然價格上有點小貴但我吃得很開心www 尤其我點的餐點還有附說明書(??)

![ YAYOI ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-hitcon-2.webp)

議程部分的講師已經可以認出很多熟面孔了：@CK、@Birdman、@Adr、@大Alan、@小Alan、@Orange、@Inndy、@ddaa、@yuawn、@Angelboy、@Lays、@Jeffxx，幾乎都是駭客圈的紅人XD 這些大神們場子人都不會太少，尤其 @Orange 再次打穿 Facebook 的主題在 R0 快被塞爆==

在廠商攤位方面，照往例就是到處認親、認識朋友，只是同學把我當許願機拿贈品還稱讚我議程蟑螂... 嗚嗚幹，另外咱們 @stavhaygn 在 UCCU 的主題是數位安全＆實體安全，蠻有趣的挑戰，讓我想起自己小時候筆電袋被父母上鎖時，也是用迴紋針慢慢解鎖ㄏ

![ UCCU 實體解鎖挑戰](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-hitcon-3.webp)

其他像是 DEVCORE、BambooFox、TeamT5、中華資安 都有各自的 CTF challenge，所以少了大地遊戲也不怕沒事做。另外 HITCON GIRLS 辦的 "Who is Lily?" OSINT 挑戰也很酷，還需要在 HITCON 現場找人、寄釣魚信件才算完成任務！

在官方的活動中還有線上密室逃脫，很感謝 AIS3 室友的邀請，找齊四人就在會場的角落玩，甚至還被 iThome 記者拍到寫進[新聞](https://www.ithome.com.tw/news/139967)裡wwwwww 至於玩法大致上都寫在文章內，比較障礙是我和揪來的學妹都沒有接觸過 minecraft，所以在操作移動上備受關懷ww

至於有沒有拿到 Flag 呢... 我忘記了w 不過有挖到一題 pwn 交給隊友處理，我自己是在不同房間到處亂晃找線索，還有玩 minetest 裡的電腦的踩地雷XD 活動結束後還有無差別槍戰不知道在幹嘛，我只記得我一直掉出場外領便當 : (


![ minetest 踩地雷](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-hitcon-4.webp)
![ minetest 風景?](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-hitcon-5.webp)

![ 槍戰 ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-hitcon-6.webp)

<br>

印象中往年都有 HITCON token，今年也不例外。不過由於是用虛擬貨幣，所以對沒有再接觸的朋友很多都懶得操作，所以我~~社交工程~~借用身邊同學把官方送的錢包(300 HITCON token)轉到我手上，然後拿去HITCON線上商城買了幾塊電路板，到 Day2 甚至還開放可以購買現場的特色貼紙
... 當然是給他買爆 嘻嘻!!

![ hitcon token ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_800/v1618836340/blog-posts/2020-08/19-hitcon-8.webp)


![ hitcon 貼紙 ](https://res.cloudinary.com/eevee-blog/image/upload/c_scale,f_auto,q_auto,w_800/v1618836340/blog-posts/2020-08/19-hitcon-7.webp)

沒有比較沒有傷害，雖然我是第一次參加 HITCON，但和其他場次相比感覺就是有點...掉漆，先不論少了往年慣例的電路板 challenge、煉蠱大會還開天窗。 Day1 報到的時候發了一個黑盒子沒有給提袋，看到大家都當寶貝到處抱著，結果那個黑盒子不僅是一次性的拆了就壞，而且裡頭都是廠商的 DM ... 怪不得 Day2 還能疊成塔拜託大家領走 = w =

![ 黑盒子 ](https://res.cloudinary.com/eevee-blog/image/upload/f_auto,q_auto/v1618836340/blog-posts/2020-08/19-hitcon-9.webp)

總之，還是多少在 HITCON 聽到一些有趣的議題/關鍵字，只差在有沒有時間回去翻QwQ  
接下來就是 10 月的 MOPCON，只是已經參加第四年應該寫不出心得了吧XDDDDDD


就醬。

